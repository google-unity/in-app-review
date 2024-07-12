# How To Install

### Install by add directly in `manifest.json` in folder `Packages/manifest.json`


for version `1.8.2`
```csharp
"com.google.play.review": "https://github.com/google-unity/in-app-review.git#1.8.2",
```


- dependency `google.play.core 1.8.4`, `google.play.common 1.9.1`, `google.android.appbundle 1.9.0`, `external-dependency-manager 1.2.172`
```csharp
"com.google.play.core": "https://github.com/google-unity/google-play-core.git#1.8.4",
"com.google.play.common": "https://github.com/google-unity/google-play-common.git#1.9.1",
"com.google.android.appbundle": "https://github.com/google-unity/android-app-bundle.git#1.9.0",
"com.google.external-dependency-manager": "https://github.com/google-unity/external-dependency-manager.git#1.2.172",
```


# Usages

```csharp
using System.Collections;
using UnityEngine;
#if UNITY_IOS
using UnityEngine.iOS;
#elif UNITY_ANDROID
using Google.Play.Review;
#endif

public class AppRating : MonoBehaviour
{
#if UNITY_ANDROID
    private ReviewManager _reviewManager;
    private PlayReviewInfo _playReviewInfo;
    private Coroutine _coroutine;
#endif

    private void Start()
    {
#if UNITY_ANDROID
        _coroutine = StartCoroutine(InitReview());
#endif
    }

    public void RateAndReview()
    {
#if UNITY_IOS
        Device.RequestStoreReview();
#elif UNITY_ANDROID
        StartCoroutine(LaunchReview());
#endif
    }

#if UNITY_ANDROID
    private IEnumerator InitReview(bool force = false)
    {
        if (_reviewManager == null) _reviewManager = new ReviewManager();

        var requestFlowOperation = _reviewManager.RequestReviewFlow();
        yield return requestFlowOperation;
        if (requestFlowOperation.Error != ReviewErrorCode.NoError)
        {
            if (force) DirectlyOpen();
            yield break;
        }

        _playReviewInfo = requestFlowOperation.GetResult();
    }

    public IEnumerator LaunchReview()
    {
        if (_playReviewInfo == null)
        {
            if (_coroutine != null) StopCoroutine(_coroutine);
            yield return StartCoroutine(InitReview(true));
        }

        var launchFlowOperation = _reviewManager.LaunchReviewFlow(_playReviewInfo);
        yield return launchFlowOperation;
        _playReviewInfo = null;
        if (launchFlowOperation.Error != ReviewErrorCode.NoError)
        {
            DirectlyOpen();
            yield break;
        }
    }
#endif

    private void DirectlyOpen() { Application.OpenURL($"https://play.google.com/store/apps/details?id={Application.identifier}"); }
}
```




# Knows Issue with unity 2021+

If you call `RequestReviewFlow` with `Coroutine` to display the review form in test environment (specifically Internal testing) but nothing happens and no error log appears in console.

The cause may be that the native plugin of in-app-review is missing when building the application, namely `com.google.android.play.review` and
`com.google.android.gms.play-services-tasks`. So you can try enabling proguard to keep them in the build

![image](https://user-images.githubusercontent.com/44673303/209791680-27351d06-2569-438e-97c1-bb2e537bb7db.png)


```text
-keep class com.google.android.play.core.** { *; }
-keep class com.google.android.gms.play-** { *; }
```
