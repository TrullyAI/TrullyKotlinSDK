# TrullySDK

## Add TrullySDK repository and dependencies

### Add jitpack as repository store in `settings.gradle`

```groovy
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)

    repositories {
        google()
        mavenCentral()
        maven {
            url 'https://jitpack.io'
        }
    }
}
```

### Add dependencies to the App level `build.gradle`

```groovy
dependencies {
    //if you're using JetPack make sure to add this two dependencies otherwise you'll get resource missing error
    implementation("androidx.appcompat:appcompat:1.6.1")
    implementation("com.google.android.material:material:1.10.0")

    //We use retrofit to handle http requests. Add this if you don't already have it in your project
    implementation("androidx.databinding:viewbinding:8.1.4")
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
    implementation("com.squareup.okhttp3:logging-interceptor:4.9.3")

    //This are the dependencies that makes our SDK
    implementation("com.github.TrullyAI:FaceAPI:5.2.2715")
    implementation("com.github.TrullyAI:FaceCore:5.2.232")
    implementation("com.github.TrullyAI:CommonAPI:6.9.1398")
    implementation("com.github.TrullyAI:DocumentReaderFullAuth:6.9.9555")
    implementation("com.github.TrullyAI:DocumentReaderAPI:6.9.9406")
    implementation("com.github.TrullyAI:TrullyKotlinSDK:0.0.14")
}
```

### Add permission in manifest

```xml

    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.CAMERA" />
```

## Add it to you're project

### Listener

At the end of the process the SDK will trigger an HTTP Request. Add
TrullyResultListener to the activity that will launch the SDK and implement its
members so you can have access to the response.

| Method     | Description                         |
| ---------- | ----------------------------------- |
| `onResult` | Catch the results of the operation. |
| `onError`  | Catch the errors of the operation.  |

```java
class MainActivity : AppCompactActivity(), TrullyResultListener {

    override fun onResult(response: AppData) {
        TODO("On completed logic")
        //AppData.decisionMaker -> The result of the process. You'll find more details in https://trully.readme.io/docs/decision-maker-1
        //AppData.images -> base64 for the different images obtained during the process
    }

    override fun onError() {
        TODO("Error logic")
    }
}
```

### Configure and initialize

| Parameter        | Description                                                          |
| ---------------- | -------------------------------------------------------------------- |
| `packageContext` | Is the context of your Application/Activity.                         |
| `apiKey`         | You're client API_KEY. The SDK won't work without it                 |
| `styles`         | Styles object that will allow you to config color and logo. Optional |
| `config`         | Config object will pass the environment and the styles to the SDK    |

```java
  private fun initialize() {
    //Optionally change color and logo
    val styles: TrullyStyles = TrullyStyles()

    styles.primaryColor = ai.trully.sdk.R.color.primary
    styles.logo = ai.trully.sdk.R.drawable.logo

    styles.cameraStyle.cameraScreenSectorTarget = ai.trully.sdk.R.color.primary
    styles.cameraStyle.cameraScreenSectorActive = ai.trully.sdk.R.color.primary
    styles.cameraStyle.cameraScreenStrokeActive = ai.trully.sdk.R.color.primary
    styles.cameraStyle.cameraScreenStrokeNormal = ai.trully.sdk.R.color.primary

    //Set SDK configuration
    val config = TrullyConfig(Environment.DEBUG, styles) //For production environments use `Environment.RELEASE`.

    //Initialize SDK
    TrullySdk.init(packageContext, apiKey, config)
}
```

### Launch

To start the SDK you'll need to call the `start` method.

| Parameter        | Description                                               |
| ---------------- | --------------------------------------------------------- |
| `packageContext` | Is the context of your Application/Activity.              |
| `listener`       | Is the TrullyResultListener of your Application/activity. |

```java
private fun onLaunchTrully() {
        TrullySdk.start(packageContext, listener)
}
```

### Full Example

```java
class MainActivity : AppCompatActivity(), TrullyResultListener {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        findViewById<Button>(R.id.launchTrully)
            .setOnClickListener {
                initialize()
                onTap()
            }
    }

    override fun onResult(response: AppData) {
        TODO("On completed logic")
    }


    override fun onError() {
        TODO("Error logic")
    }


    private fun initialize() {
        val styles: TrullyStyles = TrullyStyles()

        styles.primaryColor = ai.trully.sdk.R.color.primary
        styles.logo = ai.trully.sdk.R.drawable.logo

        styles.cameraStyle.cameraScreenSectorTarget = ai.trully.sdk.R.color.primary
        styles.cameraStyle.cameraScreenSectorActive = ai.trully.sdk.R.color.primary
        styles.cameraStyle.cameraScreenStrokeActive = ai.trully.sdk.R.color.primary
        styles.cameraStyle.cameraScreenStrokeNormal = ai.trully.sdk.R.color.primary

        val config = TrullyConfig(Environment.DEBUG, styles)

        TrullySdk.init(this, "YOUR_API_KEY", config)
    }

    private fun onTap() {
        TrullySdk.start(this, this)
    }

}
```
