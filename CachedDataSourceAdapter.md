## CachedDataSourceAdapter
The `CachedDataSourceAdapter` is an adapter that the library will use to cache the entities in the [`DataSource`].

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
- `runInTransaction` will be used to apply multiple [`DataSource`] operations in a single transaction. That means that if something fails, all operations will fail.
- `saveEntities` will be invoked to save all entities into the [`DataSource`].
This will be executed in a transaction.
- `dropEntities` will be used to delete all cached entities from the [`DataSource`].
This will be executed in a transaction.

# DataSource
The implementation of the `CachedDataSourceAdapter` will depend mostly on the [`DataSource`] that we choose.
We will use the [Room Persistence Library](https://developer.android.com/topic/libraries/architecture/room) which provides a [`DataSource`] trivially.
However, you could use any other [`DataSource`].

## Save and retrieve data
The [`DataSource`] should return the entities in the same order as provided by the service.

There are at least two approaches we can follow to solve this problem:
1. Add an index to the entity.
2. Add a secondary entity to save the index.

### Add an index to the entity

The first approach consists of adding an index position to the entity.
Then, when a new page comes, you have to search for the biggest index in the [`DataSource`] and then adding to all new entities an index value, starting from that index plus one.

```kotlin
@Entity
class Entity(@PrimaryKey var id: Long, val index: Long, ...)
```

Using Room we have to define four methods in the `Entity` [`Dao`](https://developer.android.com/training/data-storage/room/accessing-data), to provide the `CachedDataSourceAdapter`.


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

Using the `EntityDao`, the `CachedDataSourceAdapter` is:

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
Although the previous approach works in some cases, there are other complex cases in which it will not work.
Suppose there are multiple services that could return the same entities but in different order.
In this case, using the current approach, we have to add two position indexes to the `Entity`.
It will work, but it's not an elegant solution.
We are adding some logic to an entity that is not directly connected to it.
Furthermore, suppose that you need to save some parameter, for example a filter field: you will not be able to model it with a single entity.

The second approach to solve the problem is having multiple objects to model the situation.
One object to model the entity itself and one object for each relationship or ordering of this entity.
In the last model category you can also save the parameter list, such as the listing filters or sort parameters which were used to make the service request.

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
- The `saveEntities` method has to save the entities and the related entities.
- The `dropEntities` method can delete both entities or just drop the related entity.
If there are multiple services that can return the same entities, we should take some considerations to delete both entities and keep the [`DataSource`] consistency. 

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

Although we have presented two approaches, we recommend using the second one.
It's a bit harder to implement than the other one, but it has its advantage.
In both approaches you have to remember the extra steps to take before dropping the entities.
However, the second one provides an easier and more flexible way to implement them.

[`DataSource`]: https://developer.android.com/reference/android/arch/paging/DataSource
