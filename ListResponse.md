# List Response

The library defines a common service response type which is used to fetch the pages.

```kotlin
interface ListResponse<T> {
  fun getElements(): List<T>
}
```

Additionally, there are other responses types that can be used when the service provides you more information in the response.

## List Response with Entity Count

Used when the service provides you the amount of entities.

```kotlin
interface ListResponseWithEntityCount<T> : ListResponse<T> {
  fun getEntityCount() : Long
}
```

## List Response with Page Count

Used when the service provides you the amount of pages.

```kotlin
interface ListResponseWithPageCount<T> : ListResponse<T> {
  fun getPageCount(): Long
}
```
