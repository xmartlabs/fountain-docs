# Listing

`Listing` is a structure designed to manage a list stream.

```kotlin
data class Listing<T>(
    val pagedList: LiveData<PagedList<T>>,
    val networkState: LiveData<NetworkState>,
    val refresh: () -> Unit,
    val refreshState: LiveData<NetworkState>,
    val retry: () -> Unit
)
```

It contains basically five elements:
1. **pagedList:** A changing data stream of type `T` represented as a [`LiveData`](https://developer.android.com/topic/libraries/architecture/livedata) of a [`PagedList`](https://developer.android.com/reference/android/arch/paging/PagedList).
A [`PagedList`](https://developer.android.com/reference/android/arch/paging/PagedList) is a List which loads its data in chunks (pages) from a [`DataSource`](https://developer.android.com/reference/android/arch/paging/DataSource).
To display a [`PagedList`](https://developer.android.com/reference/android/arch/paging/PagedList), you must use a [`PagedListAdapter`](https://developer.android.com/reference/android/arch/paging/PagedListAdapter), which enables the binding of a [`PagedList`](https://developer.android.com/reference/android/arch/paging/PagedList) to a [`RecyclerView`](https://developer.android.com/reference/android/support/v7/widget/RecyclerView).
2. **[networkState](NetworkState.md):** A stream that notifies network state changes, such as when a new page started loading (so you can show a spinner in the UI).
3. **refresh:** A refresh function, to refresh all data.
4. **refreshState:** A stream that notifies the status of the refresh request.
5. **retry:** A retry function to execute if something fails.
