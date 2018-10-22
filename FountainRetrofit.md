# Fountain Retrofit

This module provides a [`Listing`] component based on a Retrofit [call](https://square.github.io/retrofit/2.x/retrofit/retrofit2/Call.html) implementation.

## Network Support Listing Creator for paged endpoints

A Listing with Network support for paged endpoints can be created invoking `createNetworkListing`.
```kotlin
FountainRetrofit.createNetworkListing(
  networkDataSourceAdapter: RetrofitNetworkDataSourceAdapter<out ListResponse<out NetworkValue>>,
  firstPage: Int = FountainConstants.DEFAULT_FIRST_PAGE,
  ioServiceExecutor: Executor = FountainConstants.NETWORK_EXECUTOR,
  pagedListConfig: PagedList.Config = FountainConstants.DEFAULT_PAGED_LIST_CONFIG
)
```

There's only one required structure, [`RetrofitNetworkDataSourceAdapter<out ListResponse<Value>>`], which Fountain uses to handle the paging.

In addition, there are some optional parameters that you can define:
- `firstPage: Int`: The initial page number, by default its value is 1.
- [`ioServiceExecutor : Executor`](https://developer.android.com/reference/java/util/concurrent/Executor): The executor that will be used to make the service call. By default, the library will use a pool of 5 threads.
- [`pagedListConfig: PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config): The paged list configuration.
In this object you can specify several options, for example the `pageSize` and the `initialPageSize`. 

## Network Support Listing Creator for not paged endpoints

A Listing with Network support for paged not endpoints can be created invoking `createNetworkListing`.
```kotlin
FountainRetrofit.createNotPagedNetworkListing(
  notPagedRetrifitPageFetcher: NotPagedRetrifitPageFetcher<out ListResponse<out NetworkValue>>,
  ioServiceExecutor: Executor = FountainConstants.NETWORK_EXECUTOR
)
```

There's only one required structure, [`NotPagedRetrifitPageFetcher<out ListResponse<Value>>`], which Fountain uses to handle the paging.

Additionally you can specify the [`ioServiceExecutor : Executor`](https://developer.android.com/reference/java/util/concurrent/Executor). It's the executor that'll be used to make the service call.

## Cache + Network Support Listing Creator for paged endpoints

A Listing with Cache + Network Support for paged endpoints can be created invoking the `createNetworkWithCacheSupportListing`


```kotlin
FountainRetrofit.createNetworkWithCacheSupportListing(
  networkDataSourceAdapter: RetrofitNetworkDataSourceAdapter<out ListResponse<out NetworkValue>>,
  cachedDataSourceAdapter: CachedDataSourceAdapter<NetworkValue, DataSourceValue>,
  ioServiceExecutor: Executor = FountainConstants.NETWORK_EXECUTOR,
  ioDatabaseExecutor: Executor = FountainConstants.DATABASE_EXECUTOR,
  firstPage: Int = FountainConstants.DEFAULT_FIRST_PAGE,
  pagedListConfig: PagedList.Config = FountainConstants.DEFAULT_PAGED_LIST_CONFIG
)
```

There are two required components:

1. A [`RetrofitNetworkDataSourceAdapter<out ListResponse<Value>>`] to fetch all pages.
1. A [`CachedDataSourceAdapter<Value>`](CachedDataSourceAdapter.md) to take control of the `DataSource`.

In addition, there are some optional parameters that you can define:
- `firstPage: Int`: The initial page number, by default its value is 1.
- [`ioServiceExecutor : Executor`](https://developer.android.com/reference/java/util/concurrent/Executor): The executor through which the service call will be made  By default, the library will use a pool of 5 threads.
- [`ioDatabaseExecutor : Executor`](https://developer.android.com/reference/java/util/concurrent/Executor): The executor through which the database transactions will be made. By default the library will use a single thread executor.
- [`pagedListConfig: PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config): The paged list configuration.
In this object you can specify several options, for example the `pageSize` and the `initialPageSize`. 

## Cache + Network Support Listing Creator for not paged endpoints

A Listing with Cache + Network Support for paged endpoints can be created invoking the `createNetworkWithCacheSupportListing`


```kotlin
FountainRetrofit.createNotPagedNetworkWithCacheSupportListing(
  notPagedRetrifitPageFetcher: NotPagedRetrifitPageFetcher<out ListResponse<out NetworkValue>>,
  cachedDataSourceAdapter: CachedDataSourceAdapter<NetworkValue, DataSourceValue>,
  ioServiceExecutor: Executor = FountainConstants.NETWORK_EXECUTOR,
  ioDatabaseExecutor: Executor = FountainConstants.DATABASE_EXECUTOR
)
```
There are two required components:

1. A [`NotPagedRetrifitPageFetcher<out ListResponse<Value>>`] to fetch all pages.
1. A [`CachedDataSourceAdapter<Value>`](CachedDataSourceAdapter.md) to take control of the `DataSource`.

In addition, there are some optional parameters that you can define:
- [`ioServiceExecutor : Executor`](https://developer.android.com/reference/java/util/concurrent/Executor): The executor through which the service call will be made. By default, the library will use a pool of 5 threads.
- [`ioDatabaseExecutor : Executor`](https://developer.android.com/reference/java/util/concurrent/Executor): The executor through which the database transactions will be made. By default the library will use a single thread executor.

[`Listing`]: Listing.md
[`NotPagedRetrifitPageFetcher<out ListResponse<Value>>`]: RetrofitNetworkDataSourceAdapter.md#not-paged-retrofit-page-fetcher
[`RetrofitNetworkDataSourceAdapter<out ListResponse<Value>>`]: RetrofitNetworkDataSourceAdapter.md#networkdatasourceadapter
