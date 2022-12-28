# How To Install

### Install by add directly in `manifest.json` in folder `Packages/manifest.json`


for version `1.8.1`
```csharp
"com.google.play.review": "https://github.com/google-unity/in-app-review.git#1.8.1",
```


- dependency `google.play.core 1.8.1`, `google.play.common 1.8.1`, `google.android.appbundle 1.8.0`, `external-dependency-manager 1.2.172`
```csharp
"com.google.play.core": "https://github.com/google-unity/google-play-core.git#1.8.1",
"com.google.play.common": "https://github.com/google-unity/google-play-common.git#1.8.1",
"com.google.android.appbundle": "https://github.com/google-unity/android-app-bundle.git#1.8.0",
"com.google.external-dependency-manager": "https://github.com/google-unity/external-dependency-manager.git#1.2.172",
```


# Knows Issue with unity 2021+

If you call `RequestReviewFlow` with `Coroutine` to display the review form in test environment (specifically Internal testing) but nothing happens and no error log appears in console.

The cause may be that the native plugin of in-app-review is missing when building the application, namely `com.google.android.play.review` and
`com.google.android.gms.play-services-tasks`. So you can try enabling proguard to keep them in the build

![image](https://user-images.githubusercontent.com/44673303/209791680-27351d06-2569-438e-97c1-bb2e537bb7db.png)


```text
-keep class com.google.android.play.core.** { *; }
-keep com.google.android.gms.play-** { *; }
```
