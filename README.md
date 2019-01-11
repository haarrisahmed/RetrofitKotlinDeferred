# RetrofitKotlinDeferred
Simple to Complex Tutorial for making network calls in Android using Retrofit2, Kotlin and its Deferred Type

### Dependencies

Add the following to the App dependencies

```kotlin
// build.gradle(Module: app)
dependencies {

    def moshiVersion="1.8.0"
    def retrofit2_version = "2.5.0"
    def okhttp3_version = "3.12.0"
    def kotlinCoroutineVersion = "1.0.1"

     
    //Moshi
    implementation "com.squareup.moshi:moshi-kotlin:$moshiVersion"
    kapt "com.squareup.moshi:moshi-kotlin-codegen:$moshiVersion"

    //Retrofit2
    implementation "com.squareup.retrofit2:retrofit:$retrofit2_version"
    implementation "com.squareup.retrofit2:converter-moshi:$retrofit2_version"
    implementation "com.jakewharton.retrofit:retrofit2-kotlin-coroutines-adapter:0.9.2"

    //Okhttp3
    implementation "com.squareup.okhttp3:okhttp:$okhttp3_version"
    implementation 'com.squareup.okhttp3:logging-interceptor:3.11.0'

    //Kotlin Coroutines
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:$kotlinCoroutineVersion"
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:$kotlinCoroutineVersion"

    //Picasso for Image Loading
    implementation ('com.squareup.picasso:picasso:2.71828'){
        exclude group: "com.android.support"
    }
}
```

## [API #1 : JSONPlaceHolder API](https://jsonplaceholder.typicode.com ) 
A Fake Online REST API for Testing and Prototyping

JSONPlaceholder comes with a set of 6 common resources:

* [/posts](https://jsonplaceholder.typicode.com/posts)	100 posts
* [/comments](https://jsonplaceholder.typicode.com/comments)	500 comments
* [/albums](https://jsonplaceholder.typicode.com/albums)	100 albums
* [/photos](https://jsonplaceholder.typicode.com/photos)	5000 photos
* [/todos](https://jsonplaceholder.typicode.com/todos)	200 todos
* [/users](https://jsonplaceholder.typicode.com/users)	10 user



### A simple example

**Global Setup**
```kotlin

//ApiFactory to create jsonPlaceHolder Api
object Apifactory{
    fun retrofit() : Retrofit = Retrofit.Builder()
                .client(OkHttpClient().newBuilder().build())
                .baseUrl("https://jsonplaceholder.typicode.com")
                .addConverterFactory(MoshiConverterFactory.create())
                .addCallAdapterFactory(CoroutineCallAdapterFactory())
                .build()   

    val placeHolderApi : PlaceholderApi = retrofit().create(PlaceholderApi::class.java)
} 

```

**Fetching Posts from /posts**

```kotlin

//Data Model for Post
data class PlaceholderPosts(
    val id: Int,
    val userId : Int,
    val title: String,
    val body: String
)

//A retrofit Network Interface for the Api
interface PlaceholderApi{
    @GET("/posts")
    fun getPosts() : Deferred<Response<List<PlaceholderPosts>>>
}

//Finally making the call
val service = ApiFactory.placeholderApi
GlobalScope.launch(Dispatchers.Main) {
    val postRequest = service.getPosts() // Making Network call
    try {
        val response = postRequest.await()
        val posts = response.body() // This is List<PlaceholderPosts> 
    }catch (e: Exception){

    }
}
```

