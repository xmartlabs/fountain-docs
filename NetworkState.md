# NetworkState

`NetworkState` is a structure to handle the different network states.

```kotlin
sealed class NetworkState(
  open val page: Int, 
  open val pageSize: Int, 
  open val isFirstPage: Boolean, 
  open val isLastPage: Boolean
) {
  
  data class Loading(
      override val page: Int,
      override val pageSize: Int,
      override val isFirstPage: Boolean,
      override val isLastPage: Boolean
  ) : NetworkState(page = page, pageSize = pageSize, isFirstPage = isFirstPage, isLastPage = isLastPage)

  data class Loaded(
      override val page: Int,
      override val pageSize: Int,
      override val isFirstPage: Boolean,
      override val isLastPage: Boolean
  ) : NetworkState(page = page, pageSize = pageSize, isFirstPage = isFirstPage, isLastPage = isLastPage)

  data class Error(
      val exception: Throwable,
      override val page: Int,
      override val pageSize: Int,
      override val isFirstPage: Boolean,
      override val isLastPage: Boolean
  ) : NetworkState(page = page, pageSize = pageSize, isFirstPage = isFirstPage, isLastPage = isLastPage)
}
```

The `NetworkState` structure has 3 different states
- `NetworkState.Loading`: Indicate that a service call is running.
- `NetworkState.Loaded`: Indicate that a service call executed successfully.
- `NetworkState.Error`: Indicate that a service call failed.

Each state provides some information related the performed request:
- `page`: The requested page number.
- `pageSize`: The requested page size.
- `isFirstPage`: Indicates if the requested page is the first page.
- `isLastPage`: Indicates if the requested page is the last page.

The `NetworkState.Error` class provides an additional field including the Throwable that caused the error.
