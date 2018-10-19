# Fountain Coroutines

This module provides a [`Listing`] component based on a Retrofit Coroutine adapter like [this one](https://github.com/JakeWharton/retrofit2-kotlin-coroutines-adapter).

## Network Support Listing Creator for paged endpoints

A Listing with Network support for paged endpoints can be created invoking `createNetworkListing`.
```kotlin
FountainCoroutines.createNetworkListing(
  networkDataSourceAdapter: CoroutineNetworkDataSourceAdapter<out ListResponse<out NetworkValue>>,
  firstPage: Int = FountainConstants.DEFAULT_FIRST_PAGE,
  ioServiceCoroutineDispatcher: CoroutineDispatcher = Dispatchers.IO,
  coroutineScope: CoroutineScope = GlobalScope,
  pagedListConfig: PagedList.Config = FountainConstants.DEFAULT_PAGED_LIST_CONFIG
)
```

There's only one required structure, [`CoroutineNetworkDataSourceAdapter<out ListResponse<Value>>`], which Fountain uses to handle the paging.

In addition, there are some optional parameters that you can define:
- `firstPage: Int`: The initial page number, by default its value is 1.
- [`ioServiceCoroutineDispatcher : CoroutineDispatcher`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-dispatcher/): The [CoroutineDispatcher] with which the service call will be made. By default, the library will use [Dispatchers.IO].
- [`coroutineScope: CoroutineScope`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-scope/): Define the scope where the coroutines will run.
- [`pagedListConfig: PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config): The paged list configuration.
In this object you can specify several options, for example the `pageSize` and the `initialPageSize`. 

## Network Support Listing Creator for not paged endpoints

A Listing with Network support for not paged endpoints can be created invoking `createNotPagedNetworkListing`.
```kotlin
fun <NetworkValue> createNotPagedNetworkListing(
  notPagedCoroutinePageFetcher: NotPagedCoroutinePageFetcher<out ListResponse<out NetworkValue>>,
  ioServiceCoroutineDispatcher: CoroutineDispatcher = FountainConstants.NETWORK_EXECUTOR.asCoroutineDispatcher(),
  coroutineScope: CoroutineScope = GlobalScope
)
```

There's only one required structure, [`NotPagedCoroutinePageFetcher<out ListResponse<Value>>`], which Fountain uses to handle the paging.

In addition, there are some optional parameters that you can define:
- [`ioServiceCoroutineDispatcher : CoroutineDispatcher`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-dispatcher/): The [CoroutineDispatcher] with which the service call will be made. By default, the library will use [Dispatchers.IO].
- [`coroutineScope: CoroutineScope`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-scope/): Define the scope where the coroutines will run.


## Cache + Network Support Listing Creator for paged endpoints

A Listing with Cache + Network Support for paged endpoints can be created invoking the `createNetworkWithCacheSupportListing`

```kotlin
FountainCoroutines.createNetworkWithCacheSupportListing(
  networkDataSourceAdapter: CoroutineNetworkDataSourceAdapter<out ListResponse<out NetworkValue>>,
  cachedDataSourceAdapter: CachedDataSourceAdapter<NetworkValue, DataSourceValue>,
  ioServiceCoroutineDispatcher: CoroutineDispatcher =  Dispatchers.IO,
  ioDatabaseCoroutineDispatcher: CoroutineDispatcher = FountainConstants.DATABASE_EXECUTOR.asCoroutineDispatcher(),
  coroutineScope: CoroutineScope = GlobalScope,
  firstPage: Int = FountainConstants.DEFAULT_FIRST_PAGE,
  pagedListConfig: PagedList.Config = FountainConstants.DEFAULT_PAGED_LIST_CONFIG
)
```

There are two required components:

1. A [`CoroutineNetworkDataSourceAdapter<out ListResponse<Value>>`] to fetch all pages.
1. A [`CachedDataSourceAdapter<Value>`] to take control of the `DataSource`.

In addition, there are some optional parameters that you can define:
- `firstPage: Int`: The initial page number, by default its value is 1.
- [`ioServiceCoroutineDispatcher : CoroutineDispatcher`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-dispatcher/): The [CoroutineDispatcher] with which the service call will be made. By default, the library will use [Dispatchers.IO].
- [`ioDatabaseCoroutineDispatcher : CoroutineDispatcher`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-dispatcher/): The [CoroutineDispatcher] through which the database transactions will be made. By default the library will use a single thread [CoroutineDispatcher].
- [`coroutineScope: CoroutineScope`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-scope/): Define the scope where the coroutines will run.
- [`pagedListConfig: PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config): The paged list configuration.
In this object you can specify several options, for example the `pageSize` and the `initialPageSize`. 

## Cache + Network Support Listing Creator for not paged endpoints

A Listing with Cache + Network Support for not paged endpoints can be created invoking the `createNotPagedNetworkWithCacheSupportListing`

```kotlin
FountainCoroutines.createNotPagedNetworkWithCacheSupportListing(
    notPagedCoroutinePageFetcher: NotPagedCoroutinePageFetcher<out ListResponse<out NetworkValue>>,
    cachedDataSourceAdapter: CachedDataSourceAdapter<NetworkValue, DataSourceValue>,
    ioServiceCoroutineDispatcher: CoroutineDispatcher = FountainConstants.NETWORK_EXECUTOR.asCoroutineDispatcher(),
    ioDatabaseCoroutineDispatcher: CoroutineDispatcher = FountainConstants.DATABASE_EXECUTOR.asCoroutineDispatcher(),
    coroutineScope: CoroutineScope = GlobalScope
)
```

There are two required components:

1. A [`NotPagedCoroutinePageFetcher<out ListResponse<Value>>`] to fetch all pages.
1. A [`CachedDataSourceAdapter<Value>`] to take control of the `DataSource`.

In addition, there are some optional parameters that you can define:
- [`ioServiceCoroutineDispatcher : CoroutineDispatcher`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-dispatcher/): The [CoroutineDispatcher] with which the service call will be made. By default, the library will use [Dispatchers.IO].
- [`ioDatabaseCoroutineDispatcher : CoroutineDispatcher`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-dispatcher/): The [CoroutineDispatcher] through which the database transactions will be made. By default the library will use a single thread [CoroutineDispatcher].
- [`coroutineScope: CoroutineScope`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-scope/): Define the scope where the coroutines will run.

[CoroutineDispatcher]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-coroutine-dispatcher/
[Dispatchers.IO]: https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.experimental/-i-o.html
[`Listing`]: Listing.md
[`CoroutineNetworkDataSourceAdapter<out ListResponse<Value>>`]: CoroutineNetworkDataSourceAdapter.md#network-data-source-adapter
[`NotPagedCoroutinePageFetcher<out ListResponse<Value>>`]: CoroutineNetworkDataSourceAdapter.md#not-paged-coroutine-page-fetcher
[`CachedDataSourceAdapter<Value>`]: CachedDataSourceAdapter.md
