![](images/banner.png)

Fountain is an Android Kotlin library conceived to make your life easier when dealing with **paged** endpoint **services**, where the paging is based on incremental page numbers (e.g. 1, 2, 3, ...).
It uses the [Google Android Architecture Components](https://developer.android.com/topic/libraries/architecture/), mainly the [Android Paging Library] to make it easier to work with paged services.

The main goal of the library is to provide a [Listing](Listing.md) component from a common service specification, where the paginated strategy is based on an incremental page number.
It provides an awesome way of displaying the paged entity list and reflecting the network state in the UI.
If you use this library, you can create an MVVM architecture app and combine it with the repository pattern.
If you get a repository which provides a [`Listing`] component of each paged list, you will be creating a robuster app.

This library was designed to work with paged endpoints.
However, it also supports working with not paged endpoints.
That means that you can use all [`Listing`] features in services that return a list that's not paged.

It also provides two ways to go: a mode with [**network** support](#network-support) and a mode with [**network + cache** support](#network--cache-support). 

The strategy you choose will depend on your problem.

## Modules
Fountain supports 2 types of Retrofit service adapters:
- A [RxJava2 Retrofit adapter.](https://github.com/square/retrofit/tree/master/retrofit-adapters/rxjava2)
- A [Coroutine Retrofit adapter.](https://github.com/JakeWharton/retrofit2-kotlin-coroutines-adapter)

It also supports not using any of them, as you could work with a simple Retrofit [call](https://square.github.io/retrofit/2.x/retrofit/retrofit2/Call.html).

It has 3 different dependencies that you can [include](IncludingInProject.md) depending on what libraries are you using.

## Factory constructors
There's one static factory object class for each each dependency.
- [FountainCoroutines](FountainCoroutines.md): Used to get a [`Listing`] from a Retrofit service which uses a Coroutine adapter.
- [FountainRetrofit](FountainRetrifit.md): Used to get a [`Listing`] from a Retrofit service without using a special adapter.
- [FountainRx](FountainRxJava2.md): Used to get a [`Listing`] from a Retrofit service which uses a RxJava2 adapter.

Each static factory has the same constructors with different params:
- [`createNetworkListing`](#network-support-for-paged-endpoints): A constructor to get a `Listing` component from a common **paged** Retrofit **service** implementation.
- [`createNotPagedNetworkListing`](#network-support-for-not-paged-endpoints): A constructor to get a `Listing` component from a common **not paged** Retrofit **service** implementation.
- [`createNetworkWithCacheSupportListing`](#cache--network-support-for-paged-endpoints): A constructor to get a `Listing` component with **cache support** from a common **paged** Retrofit **service** implementation.
- [`createNotPagedNetworkWithCacheSupportListing`](#cache--network-support-for-not-paged-endpoints): A constructor to get a `Listing` component with **cache support** from a common **not paged** Retrofit **service** implementation.

## Fountain Modes

Fountain has two modes: a mode with [**network** support](#network-support) and another one with [**network + cache** support](#network--cache-support). 

Take into account that this is merely an introduction. The full documentation for all of Fountain's modes is available in each module's page:
- [FountainCoroutines](FountainCoroutines.md).
- [FountainRetrofit](FountainRetrifit.md).
- [FountainRx](FountainRxJava2.md).

### **Network Support**

It provides a [`Listing`] structure based on a common Retrofit service implementation.
Note that the entities aren't saved anywhere.

### **Network + Cache Support** 

Provides a [`Listing`] with cache support using a common Retrofit service implementation and a [`DataSource`] for caching the data.

#### Caching strategy
The pagination strategy that **Fountain** is using can be seen in the following image:
<br> <p align="center"> <img src="images/paginationStrategy.png" /> </p>

It starts with an initial service data request.
By default the initial data requested is three pages, but this value can be changed, in the [`PagedList.Config`](https://developer.android.com/reference/android/arch/paging/PagedList.Config.html), using the [`setInitialLoadSizeHint`](https://developer.android.com/reference/android/arch/paging/PagedList.Config.html#initialLoadSizeHint) method.
This parameter can be set in the factory constructor method. 
When the service data comes from the service, all data is refreshed in the `DataSource` using the `CachedDataSourceAdapter`.
Note that the `Listing` component will notify that the data has changed.

After that, the [Android Paging Library] will require pages when the local data is running low.
When a new page is required, the paging library will invoke a new service call, and will use the `CachedDataSourceAdapter` to save the returned data into the `DataSource`.

## Architecture recommendations

It's strongly recommended to integrate this component in a MVVM architecture combined with the Repository Pattern.
The [`Listing`] component should be provided by the repository.
The `ViewModel`, can use the different [`Listing`] elements, provided by the repository, to show the data and the network changes in the UI.

[Android Paging Library]: https://developer.android.com/topic/libraries/architecture/paging/
[`CachedDataSourceAdapter`]: CachedDataSourceAdapter.md
[`DataSource`]: https://developer.android.com/reference/android/arch/paging/DataSource
[`Fountain`]: Fountain.md
[`Listing`]: Listing.md
[`NetworkDataSourceAdapter`]: NetworkDataSourceAdapter.md
