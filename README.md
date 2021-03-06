# kotlin-rxokhttp-websocket  [![kotlin-rxokhttp-websocket at gitpack](https://jitpack.io/v/cy6erGn0m/kotlin-rxokhttp-websocket.svg)](https://jitpack.io/#cy6erGn0m/kotlin-rxokhttp-websocket)

WebSocket library for Kotlin and RxJava/RxKotlin based on OkHttp and Gson

## Motivation
Do you even know how terrible it would be programming 
websocket on client side to get it async and robust? This is my try to adopt reactive approach to building websocket 
clients with RxJava/RxKotlin

# Getting started
You can include the library using jitpack repo to Gradle and Maven. Follow instructions here https://jitpack.io/#cy6erGn0m/kotlin-rxokhttp-websocket

## Examples

### WebSocket push example

```kotlin
import com.squareup.okhttp.*
import kotlinx.websocket.*
import kotlinx.websocket.gson.*
import rx.lang.kotlin.*
import java.util.concurrent.*

// class we use to keep location, send and serialize to json
data class GeoLocation(val lat: Double, val lon: Double)

// here is just dummy geoPositionObservable that produces random coordinates
val geoPositionObservable =
        generateSequence { GeoLocation(Math.random(), Math.random()) }.toObservable()

// create web socket that will check location every 5 seconds and
// send it if location changed since last time
//
// will automatically reconnect if loose connection
val geoPositionWebSocket = OkHttpClient().
        newWebSocket("ws://some-host:8080/ws").
        withGsonProducer(
                geoPositionObservable
                        .sample(5L, TimeUnit.SECONDS)
                        .distinctUntilChanged()).
        open()
```

### WebSocket pull example
Another example to receive events from server on Twitter stream:

```kotlin
import com.squareup.okhttp.*
import kotlinx.websocket.*
import kotlinx.websocket.gson.*
import rx.lang.kotlin.*

// class we use to keep tweet
data class Tweet(
        val user : String,
        val login : String,
        val text : String,
        val tags : List<String>
)

// here we can subscribe console logger, UI or something else
val observer = subscriber<Tweet>().
        onNext { tweet -> println(tweet) }

val twitterWebSocket = OkHttpClient().
        newWebSocket("ws://some-server:8080/ws").
        withGsonConsumer(observer).
        open()
```

Of course you can combine both pull and push


Enjoy :)

