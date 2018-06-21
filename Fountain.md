# Fountain

Fountain is a [`Listing`] factory, that provides two static methods: one for **Network support** and another one for **Cache + Network support**.

## Network Support Listing Creator

A Listing with Network support can be created invoking the `createNetworkListing` method in the `Fountain` structure.
```kotlin
fun <Value> createNetworkListing(
  networkDataSourceAdapter: NetworkDataSourceAdapter<out ListResponse<Value>>,
  firstPage: Int = DEFAULT_FIRST_PAGE,
  ioServiceExecutor: Executor = IoExecutors.NETWORK_EXECUTOR,
  pagedListConfig: PagedList.Config = DEFAULT_PAGED_LIST_CONFIG
)
```

There's only one required structure, [`NetworkDataSourceAdapter<out ListResponse<Value>>`](NetworkDataSourceAdapter.md), which this library uses to handle the paging.

In addition, there are some optional parameters that you can define:
- `firstPage: Int`: The initial page number, by default its value is 1.
- [`ioServiceExecutor : Executor`](https://developer.android.com/reference/java/util/concurrent/Executor): The executor with which the service call will be made. By default, the library will use a pool of 5 threads.
- [`pagedListConfig: PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config): The paged list configuration.
In this object you can specify several options, for example the `pageSize` and the `initialPageSize`. 


## Cache + Network Support Listing Creator

A Listing with Cache + Network Support can be created invoking the `createNetworkWithCacheSupportListing` method in the `Fountain` structure

```kotlin
fun <Value> createNetworkWithCacheSupportListing(
  networkDataSourceAdapter: NetworkDataSourceAdapter<out ListResponse<Value>>,
  cachedDataSourceAdapter: CachedDataSourceAdapter<Value>,
  ioServiceExecutor: Executor = IoExecutors.NETWORK_EXECUTOR,
  ioDatabaseExecutor: Executor = IoExecutors.DATABASE_EXECUTOR,
  firstPage: Int = DEFAULT_FIRST_PAGE,
  pagedListConfig: PagedList.Config = DEFAULT_PAGED_LIST_CONFIG
)
```

There are two required components 
1. A [`NetworkDataSourceAdapter<out ListResponse<Value>>`](NetworkDataSourceAdapter.md) to fetch all pages.
1. A [`CachedDataSourceAdapter<Value>`](CachedDataSourceAdapter.md) to take control of the `DataSource`.

In addition, there are some optional parameters that you can define:
- `firstPage: Int`: The initial page number, by default its value is 1.
- [`ioServiceExecutor : Executor`](https://developer.android.com/reference/java/util/concurrent/Executor): The executor with which the service call will be made.  By default, the library will use a pool of 5 threads.
- [`ioDatabaseExecutor : Executor`](https://developer.android.com/reference/java/util/concurrent/Executor): The executor where database transactions will be done. By default the library will use a single thread executor.
- [`pagedListConfig: PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config): The paged list configuration.
In this object you can specify several options, for example the `pageSize` and the `initialPageSize`. 

[`Listing`]: Listing.md
