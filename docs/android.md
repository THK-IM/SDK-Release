# Android

Add the public Maven directory in `settings.gradle`:

```groovy
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://jitpack.io' }
        maven { url 'https://raw.githubusercontent.com/vizoss/webrtc-build/maven-repo/' }
        exclusiveContent {
            forRepository {
                maven { url 'https://raw.githubusercontent.com/THK-IM/SDK-Release/main/android/maven' }
            }
            filter { includeGroup 'io.github.thk-im' }
        }
    }
}
```

Select the modules used by your application, replacing `VERSION` with a published version:

```groovy
implementation 'io.github.thk-im:core:VERSION'
implementation 'io.github.thk-im:ui:VERSION'
implementation 'io.github.thk-im:provider:VERSION'
implementation 'io.github.thk-im:preview:VERSION'
implementation 'io.github.thk-im:rtc:VERSION'
```

`ui` depends on `core`; `provider` and `preview` depend on `core` and `ui`;
`rtc` depends on `core`. Declare modules whose public types your application imports
explicitly. Do not use `+`, `latest.release` or SNAPSHOT versions.

No credentials are needed for SDK downloads. Third-party dependencies still use their
own repositories; moving SDK publication does not remove Maven Central as a dependency source.
The minimum Android version is API 24; the SDK is currently built with compileSdk 36.
