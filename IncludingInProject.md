# Including in your Project

Fountain has a number of artifacts that you can include in the project.

**Retrofit module**: Uses a simple Retrofit [call](https://square.github.io/retrofit/2.x/retrofit/retrofit2/Call.html) to fetch the pages.

**Coroutine module:** Uses a [Coroutine Retrofit adapter](https://github.com/JakeWharton/retrofit2-kotlin-coroutines-adapter) to fetch the pages.

**RxJava2 module:** Uses a RxJava2 Retrofit adapter to fetch the pages.

_These modules are independent and none of them are strictly required._

### Add the jitpack.io repository

This repo is used to publish the artifacts. It also enables [dynamic builds](https://jitpack.io/docs/), allowing you to specify specific branches or commit hashes of the project to include outside of normal releases.

```Groovy
allProjects {
  repositories {
    // required to find the project's artifacts
    maven { url "https://www.jitpack.io" }
  }
}
```

```groovy
dependencies {
    // This dependency is required only if you want to use a Retrofit service without a special adapter. 
    implementation 'com.github.xmartlabs.fountain:fountain-retrofit:${fountain_version}'

    // This dependency is required only if you want to use a Coroutine retrofit adapter.
    implementation 'com.github.xmartlabs.fountain:fountain-coroutines:${fountain_version}'

    // This dependency is required only if you want to use a RxJava2 retrofit adapter.
    implementation 'com.github.xmartlabs.fountain:fountain-rx2:${fountain_version}'
}
```
