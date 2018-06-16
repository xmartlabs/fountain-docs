# DataSource & CachedDataSourceAdapter

## CachedDataSourceAdapter
The `CachedDataSourceAdapter` is an adapter that the library will use to take control of the [`DataSource`](https://developer.android.com/reference/android/arch/paging/DataSource).

```kotlin
interface CachedDataSourceAdapter<T> {
  fun getDataSourceFactory(): DataSource.Factory<*, T>

  @WorkerThread
  fun saveEntities(response: List<T>)

  @WorkerThread
  fun dropEntities()

  @WorkerThread
  fun runInTransaction(transaction: () -> Unit)
}
```

The adapter has four methods that the user has to implement:

- `getDataSourceFactory`: will be used to list the cached elements.
The returned value is used to create the [`LivePagedListBuilder`](https://developer.android.com/reference/android/arch/paging/LivePagedListBuilder).
- `runInTransaction` will be used to apply multiple `DataSource` operations in a single transaction. That means that if something fails, all operations will fail.
- `saveEntities` will be invoked to save all entities into the `DataSource`.
This will be executed in a transaction.
- `dropEntities` will be used to delete all cached entities from the `DataSource`.
This will be executed in a transaction.

## DataSource
The implementation of the `CachedDataSourceAdapter` will depend mostly in the `DataSource` that we choose.
So, in order to make the things easier we'll use the [Room Persistence Library](https://developer.android.com/topic/libraries/architecture/room) which provides a [`DataSource`](https://developer.android.com/reference/android/arch/paging/DataSource) trivially.
However, you could use any other [`DataSource`](https://developer.android.com/reference/android/arch/paging/DataSource).

### Save and retrieve data
The [`DataSource`](https://developer.android.com/reference/android/arch/paging/DataSource) must to have the ability of returning the entities in the same order that the entities were provided by the service.

There are, at least, two approaches that we can follow to solve this problem.
1. Add an index in the entity.
1. Add a secondary entity to save the index.

### Add an index in the entity

The first approach consist of adding an index position in the entity.
Then, when a new page come, you have to search the bigger index position in the [`DataSource`](https://developer.android.com/reference/android/arch/paging/DataSource) and update all entities in the response, incrementing that index by one.

```kotlin
@Entity
class Entity(@PrimaryKey var id: Long, val index: Long, ....)
```

Using room, we have to define four methods, in the `Entity` [`Dao`](https://developer.android.com/training/data-storage/room/accessing-data), to provide the `CachedDataSourceAdapter`.


```kotlin
@Dao
interface EntityDao {
  @Insert(onConflict = OnConflictStrategy.REPLACE)
  fun insertEntities(users: List<Entity>)

  @Query("SELECT * FROM Entity ORDER BY index ASC")
  fun getEntities(): DataSource.Factory<Int, Entity>

  @Query("SELECT MAX(index) + 1 FROM Entity")
  fun getNextIndex(): Long

  @Query("DELETE FROM Entity")
  fun deleteEntities()
}
```

- `insertEntities`: A method to insert the entities.
- `getEntities`: A method to retrieve all entities sorted by an index.
- `getNextIndex`: A method to return the next index value.
- `deleteEntities`: A method to delete the entities.

Using the `EntityDao` the `CachedDataSourceAdapter` is:

```kotlin
val cachedDataSourceAdapter = object : CachedDataSourceAdapter<Entity> {
      override fun getDataSourceFactory() = entityDao.getEntities()

      override fun saveEntities(response: List<Entity>) {
        val start = entityDao.getNextIndex()
        response
            .forEachIndexed { index, entity -> entity.index = start + index }
        userDao.insert(response)
      }

      override fun runInTransaction(transaction: () -> Unit) {
        db.runInTransaction(transaction)
      }

      override fun dropEntities() {
        entityDao.deleteEntities()
      }
    }
```

### Add a secondary entity to save the index
Although the previous approach work in some cases, there are other complex cases that this solution will not work.
Suppose there are multiple services that could return the same entities.
In this case, using the current approach, we have to add two position indexes in the `Entity`.
Even though that will work, that's no so clean.
We are adding some logic in an entity that is not directly connected to it.
Furthermore, suppose that you need to save some parameter, for example a filter field, in that case you will not able to model that with a single entity.

The second approach to solve the problem is having multiple entities to model this problem.
An entity to model the specific entity, which contains only the entity information, and one entity per sort relation.
In the last model category, you can also save the parameter list, such us the listing filters or sort parameters which were used to make the service request.

```kotlin
@Entity
class Entity(@PrimaryKey var id: Long, ....)

@Entity(
    foreignKeys = [
      ForeignKey(entity = Entity::class, parentColumns = ["id"], childColumns = ["entityId"])
    ],
    indices = [Index("entityId")]
)
data class EntitySortRelation(
    @PrimaryKey(autoGenerate = true) val id: Long? = null,
    val entityId: Long,
    val index: Long,
    .... // Optional parameter list
)
```

The `EntityDao` is:

```kotlin
@Dao
interface EntityDao {
  @Insert(onConflict = OnConflictStrategy.REPLACE)
  fun insert(users: List<Entity>)

  @Insert(onConflict = OnConflictStrategy.REPLACE)
  fun insertEntitySortRelation(posts: List<EntitySortRelation>)

  @Query("SELECT Entity.* FROM Entity INNER JOIN EntitySortRelation ON Entity.id = EntitySortRelation.entityId " +
      "WHERE .... " + // This is optional, it depends on the parameter list
      "ORDER BY index ASC")
  fun getEntities(search: String): DataSource.Factory<Int, Entity>

  @Query("SELECT MAX(index) + 1 FROM EntitySortRelation " +
      "WHERE ....") // This is optional, it depends on the parameter list
  fun getNextIndexInEntitySortRelation(....): Long

  @Query("DELETE FROM EntitySortRelation " +
      "WHERE .... ") // This is optional, it depends on the parameter list
  fun deleteEntitySortRelation(....)
}
```

Using the `EntityDao`, we can implement the `CachedDataSourceAdapter`.
Some considerations to take into account when we are implementing the `CachedDataSourceAdapter` are:
- The `saveEntities` method, has to save the entities and the related entities.
- The `dropEntities` method, can delete both entities or just drop the related entity.
If there are multiple services that can return the same entities, we should take some considerations to delete both entities and keep the [`DataSource`](https://developer.android.com/reference/android/arch/paging/DataSource) consistence. 

```kotlin
val cachedDataSourceAdapter = object : CachedDataSourceAdapter<Entity> {
      override fun getDataSourceFactory() = entityDao.getEntities()

      override fun saveEntities(response: List<Entity>) {
        val start = entityDao.getNextIndex()
        val relationItems = response
            .mapIndexed { index, entity -> 
            	EntitySortRelation(entityId = entity.d, index = start + index }
        userDao.insert(response)
        userDao.insertEntitySortRelation(relationItems)
      }}

      override fun runInTransaction(transaction: () -> Unit) {
        db.runInTransaction(transaction)
      }

      override fun dropEntities() {
        entityDao.deleteEntitySortRelation()
        // We can delete the entities too.
      }
    }
```

### One entity vs Multiple entities

Although we have presented present two approaches, we recommend use the second one.
This is a bit harder to implement than the other one, but it has some advantages.
It's cleaner and it's more flexible than the first one.
In both approaches you have to take account some considerations before drop the entities.
However, the second one provides a easier and cleaner way to implement and understand these considerations.
