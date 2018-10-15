# Rx Network Data Source Adapter
The `RxNetworkDataSourceAdapter` is an adapter which provides the required methods to handle the network requests.

## Rx Page Fetcher
The `RxPageFetcher` is used to fetch each page from the service.

```kotlin
interface RxPageFetcher<T : ListResponse<*>> {
  fun fetchPage(page: Int, pageSize: Int): Single<T>
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
This configuration can be set in the [Fountain factory](FountainRxs.md).*

This method returns a `Single<out ListResponse<*>`, that the library will use to fetch an specific page.

## NetworkDataSourceAdapter
The `RxNetworkDataSourceAdapter` is a [NetworkDataSourceAdapter](NetworkDataSourceAdapter.md) based on a [`RxPageFetcher`](#rx-page-fetcher) 

```kotlin
interface RxNetworkDataSourceAdapter<T : ListResponse<*>> : NetworkDataSourceAdapter<RxPageFetcher<T>>
```
