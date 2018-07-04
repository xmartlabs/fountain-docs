# NetworkState

`NetworkState` is a structure to handle the different network states.

```kotlin
enum class Status {
  RUNNING,
  SUCCESS,
  FAILED
}

@Suppress("DataClassPrivateConstructor")
data class NetworkState private constructor(
    val status: Status,
    val throwable: Throwable? = null) {

  companion object {
    val LOADED = NetworkState(Status.SUCCESS)
    val LOADING = NetworkState(Status.RUNNING)
    fun error(throwable: Throwable?) = NetworkState(Status.FAILED, throwable)
  }
}
```

The `NetworkState` has an associated `Status` with three possible values:
- `RUNNING`: Indicate that a service call is running.
- `SUCCESS`: Indicate that a service call executed successfully.
- `FAILED`: Indicate that a service call failed.
