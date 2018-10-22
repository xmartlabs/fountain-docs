# Fountain RxJava

This module provides a [`Listing`] component based on a [Retrofit RxJava2 adapter](https://github.com/square/retrofit/tree/master/retrofit-adapters/rxjava2).

## Network Support Listing Creator for paged endpoints

A Listing with Network support for paged endpoints can be created invoking `createNetworkListing`.
```kotlin
FountainRx.createNetworkListing(
  networkDataSourceAdapter: RxNetworkDataSourceAdapter<out ListResponse<NetworkValue>>,
  firstPage: Int = FountainConstants.DEFAULT_FIRST_PAGE,
  ioServiceScheduler: Scheduler = Schedulers.io(),
  pagedListConfig: PagedList.Config = FountainConstants.DEFAULT_PAGED_LIST_CONFIG
)
```

There's only one required structure, [`RxNetworkDataSourceAdapter<out ListResponse<Value>>`], which Fountain uses to handle the paging.

In addition, there are some optional parameters that you can define:
- `firstPage: Int`: The initial page number, by default its value is 1.
- [`ioServiceScheduler: Scheduler`](http://reactivex.io/documentation/scheduler.html): The [Scheduler] that will be used to make the service call. By default, the library will use [`Schedulers.io()`].
- [`pagedListConfig: PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config): The paged list configuration.
In this object you can specify several options, for example the `pageSize` and the `initialPageSize`. 

## Network Support Listing Creator for not paged endpoints

A Listing with Network support for not paged endpoints can be created invoking `createNetworkListing`.

```kotlin
FountainRx.createNotPagedNetworkListing(
  notPagedRxPageFetcher: NotPagedRxPageFetcher<out ListResponse<NetworkValue>>,
  ioServiceScheduler: Scheduler = Schedulers.io()
)
```

There's only one required structure, [`NotPagedRxPageFetcher<out ListResponse<Value>>`], which Fountain uses to handle the paging.

In addition, there are some optional parameters that you can define:
- `firstPage: Int`: The initial page number, by default its value is 1.
Additionally you can specify the [`ioServiceScheduler: Scheduler`](http://reactivex.io/documentation/scheduler.html). It's the [Scheduler] that will be used to make the service call. By default, the library will use [`Schedulers.io()`].

## Cache + Network Support Listing Creator for paged endpoints


A Listing with Cache + Network Support for paged endpoints can be created invoking the `createNetworkWithCacheSupportListing`

```kotlin
FountainRx.createNetworkWithCacheSupportListing(
  networkDataSourceAdapter: RxNetworkDataSourceAdapter<out ListResponse<out NetworkValue>>,
  cachedDataSourceAdapter: CachedDataSourceAdapter<NetworkValue, DataSourceValue>,
  ioServiceScheduler: Scheduler = Schedulers.io(),
  ioDatabaseScheduler: Scheduler = FountainConstants.DATABASE_EXECUTOR.toScheduler(),
  firstPage: Int = FountainConstants.DEFAULT_FIRST_PAGE,
  pagedListConfig: PagedList.Config = FountainConstants.DEFAULT_PAGED_LIST_CONFIG
)
```

There are two required components:

1. A [`RxNetworkDataSourceAdapter<out ListResponse<Value>>`] to fetch all pages.
1. A [`CachedDataSourceAdapter<Value>`] to take control of the `DataSource`.

In addition, there are some optional parameters that you can define:
- `firstPage: Int`: The initial page number, by default its value is 1.
- [`ioServiceScheduler: Scheduler`](http://reactivex.io/documentation/scheduler.html): The [Scheduler] that will be used to make the service call. By default, the library will use [`Schedulers.io()`].
- [`ioDatabaseScheduler : Scheduler`](http://reactivex.io/documentation/scheduler.html): The [Scheduler] through which the database transactions will be made. By default the library will use a single thread [Scheduler].
- [`pagedListConfig: PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config): The paged list configuration.
In this object you can specify several options, for example the `pageSize` and the `initialPageSize`. 

## Cache + Network Support Listing Creator for paged endpoints


A Listing with Cache + Network Support for not paged endpoints can be created invoking the `createNetworkWithCacheSupportListing`

```kotlin
FountainRx.createNotPagedNetworkWithCacheSupportListing(
  notPagedRxPageFetcher: NotPagedRxPageFetcher<out ListResponse<out NetworkValue>>,
  cachedDataSourceAdapter: CachedDataSourceAdapter<NetworkValue, DataSourceValue>,
  ioServiceScheduler: Scheduler = Schedulers.io(),
  ioDatabaseScheduler: Scheduler = FountainConstants.DATABASE_EXECUTOR.toScheduler()
)
```

There are two required components:

1. A [`NotPagedRxPageFetcher<out ListResponse<Value>>`] to fetch all pages.
1. A [`CachedDataSourceAdapter<Value>`] to take control of the `DataSource`.

In addition, there are some optional parameters that you can define:
- [`ioServiceScheduler: Scheduler`](http://reactivex.io/documentation/scheduler.html): The [Scheduler] that will be used to make the service call. By default, the library will use [`Schedulers.io()`].
- [`ioDatabaseScheduler : Scheduler`](http://reactivex.io/documentation/scheduler.html): The [Scheduler] through which the database transactions will be made. By default the library will use a single thread [Scheduler].


[Scheduler]: http://reactivex.io/documentation/scheduler.html
[`CachedDataSourceAdapter<Value>`]: CachedDataSourceAdapter.md
[`Listing`]: Listing.md
[`NotPagedRxPageFetcher<out ListResponse<Value>>`]: RxNetworkDataSourceAdapter.md#not-paged-rx-page-fetcher
[`RxNetworkDataSourceAdapter<out ListResponse<Value>>`]: RxNetworkDataSourceAdapter.md#rxnetworkdatasourceadapter
[`Schedulers.io()`]: http://reactivex.io/RxJava/javadoc/rx/schedulers/Schedulers.html#io--
