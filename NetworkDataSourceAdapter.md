# NetworkDataSourceAdapter
The `NetworkDataSourceAdapter` is an adapter which provides the required methods to handle the network requests.

Let's introduce the `PageFetcher` concept.

## Page Fetcher
The `PageFetcher` is used to fetch each page from the service.

```kotlin
interface PageFetcher<T> {
  @CheckResult
  fun fetchPage(page: Int, pageSize: Int): Single<out T>
}
```

This interface has only one method which is used to fetch every page.
The library will invoke it with two parameters: 
- `page`: The page number to be requested.
- `pageSize`: The page size.
This parameter must be respected, usually the initial load page size has a different `pageSize` than the other requests.
*If the server doesn't support a custom `pageSize`, you have to setup the [`PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config.html) to use the same page size for all requests.
You can do it by setting `pageSize` in the [`setInitialLoadSizeHint()`](https://developer.android.com/reference/android/arch/paging/PagedList.Config.html#initialLoadSizeHint) method.
The `InitialLoadSizeHint` must to be a multiple of the page size.
This configuration can be set in the [Fountain factory](Fountain.md).*

## NetworkDataSourceAdapter
The `NetworkDataSourceAdapter` is an adapter with two features:
- **Fetch** a page
- **Check** if a page can be fetched.

```kotlin
interface NetworkDataSourceAdapter<T> : PageFetcher<T> {
  @CheckResult
  fun canFetch(page: Int, pageSize: Int): Boolean
}
```

`NetworkDataSourceAdapter` is a `PageFetcher` with an additional method, `canFetch`, used to stop requesting pages.
For example, if you know that the endpoint returns only 3 pages of 10 items each, and the library invokes `canFetch(page = 5, pageSize = 10)` then you should return `false`.
You have to implement this function using the service specification.
Sometimes the service returns the page or entity amount in the response headers or in the response body, so you have to use that information to implement this function.

## NetworkDataSourceAdapter of ListResponse
The library requires a `NetworkDataSourceAdapter<ListResponse<T>>` to execute the requests and consume the responses. There are several [`ListResponse`] types that you can use.

## NetworkDataSourceAdapter providers

if you know exactly the page or entity count, the library provides a way to generate a `NetworkDataSourceAdapter` without implementing the `canFetch` method.


```kotlin
class NetworkDataSourceWithTotalEntityCountAdapter<T>(
  val pageFetcher: PageFetcher<out ListResponseWithEntityCount<T>>,
  firstPage: Int = 1
) : NetworkDataSourceAdapter<ListResponse<T>>

class NetworkDataSourceWithTotalPageCountAdapter<T>(
  val pageFetcher: PageFetcher<out ListResponseWithPageCount<T>>,
  firstPage: Int = 1
) : NetworkDataSourceAdapter<ListResponse<T>>
```

Depending on whether you know the entity or the page count, we will use either `NetworkDataSourceWithTotalEntityCountAdapter` or `NetworkDataSourceWithTotalPageCountAdapter`.

[`Fountain`]: Fountain.md
[`ListResponse`]: ListResponse.md
