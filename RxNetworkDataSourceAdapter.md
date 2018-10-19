# Rx Network Adapters

The Rx Network Adapters are structures, based on [RxJava](https://github.com/ReactiveX/RxJava) engine, that are used to handle the network request.

## Rx Network Adapters for paged endpoints.

### Rx Page Fetcher
The `RxPageFetcher` is used to fetch each page from the service.

```kotlin
interface RxPageFetcher<T : ListResponse<*>> {
  fun fetchPage(page: Int, pageSize: Int): Single<T>
}
```

This interface has only one method which is used to fetch every page.
The library will invoke it with two parameters: 
- `page`: The number of the page to be requested
- `pageSize`: The page size.
This parameter must be respected, usually the initial load page size has a different `pageSize` than the other requests.
*If the server doesn't support a custom `pageSize`, you have to setup the [`PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config.html) to use the same page size for all requests.
You can do it by setting `pageSize` in the [`setInitialLoadSizeHint()`](https://developer.android.com/reference/android/arch/paging/PagedList.Config.html#initialLoadSizeHint) method.
The `InitialLoadSizeHint` must be a multiple of the page size.
This configuration can be set in the [Fountain factory](FountainRxs.md).*

This method returns a `Single<out ListResponse<*>`, that the library will use to fetch a specific page.

### RxNetworkDataSourceAdapter
The `RxNetworkDataSourceAdapter` is a [NetworkDataSourceAdapter](NetworkDataSourceAdapter.md) based on a [`RxPageFetcher`](#rx-page-fetcher) 

```kotlin
interface RxNetworkDataSourceAdapter<T : ListResponse<*>> : NetworkDataSourceAdapter<RxPageFetcher<T>>
```

## Rx Network Adapters for not paged endpoints.

### Not Paged Rx Page Fetcher

The `NotPagedRxPageFetcher` provides a method to fetch the data from a service source.

```kotlin
interface NotPagedRxPageFetcher<T : ListResponse<*>> {
  fun fetchData(): Single<T>
}
```
