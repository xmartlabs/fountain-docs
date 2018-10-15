# NetworkDataSourceAdapter
The `NetworkDataSourceAdapter` is an adapter which provides the required methods to handle the network requests.

```kotlin
interface NetworkDataSourceAdapter<PageFetcher> {
  val pageFetcher: PageFetcher

  fun canFetch(page: Int, pageSize: Int): Boolean
}
```

It has two main features:
- **Fetch** a page
- **Check** if a page can be fetched.


# Page Fetcher
Page fetcher is an structure with enables the library to fetch an specific page from a service call.
There are 3 page fetchers, one for each library module.
- [CoroutinePageFetcher](CoroutineNetworkDataSourceAdapter.md#coroutine-page-fetcher)
- [RetrofitPageFetcher](RetrofitNetworkDataSourceAdapter.md#retrofit-page-fetcher)
- [RxPageFetcher](RxNetworkDataSourceAdapter.md#rx-page-fetcher)


Given a specific `page` and `pageSize`, the `PageFetcher` provides a way to get a `ListResponse<T>`. 
There are several [`ListResponse`] types that you can use.


## NetworkDataSourceAdapter providers
If you know exactly the page or entity count, the library provides a way to generate a NetworkDataSourceAdapter without implementing the canFetch method.
If you use either [`ListResponseWithPageCount`](ListResponse.md#list-response-with-page-count) or [`ListResponseWithEntityCount`](ListResponse.md#list-response-with-entity-count) you can convert a `PageFetcher` to a `NetworkDataSourceAdapter`.

To do that Fountain provides some extensions:

```kotlin
fun <ServiceResponse : ListResponseWithEntityCount<*>>
    PageFetcher<ServiceResponse>.toTotalEntityCountNetworkDataSourceAdapter(firstPage: Int)
fun <ServiceResponse : ListResponseWithPageCount<*>>
    PageFetcher<ServiceResponse>.toTotalPageCountNetworkDataSourceAdapter(firstPage: Int)
```

These extensions require the first page number, it has to be the same used in the Fountain static factory constructor.

[`Fountain`]: Fountain.md
[`ListResponse`]: ListResponse.md
