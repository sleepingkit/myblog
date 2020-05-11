---
title:  "Displaying Ads in your Flutter Apps $_$!"
last_modified_at: 2020-05-08 00:00:00 +0800
categories:
  - flutter
  - admob
---
## Create Google Admob ID

Login in your [Google Admob](https://admob.google.com/intl/zh-CN_cn/home/) account. Click Apps-> ADD APP
![add_a_new_app](https://sleepingkit.github.io/myblog/assets/add_a_new_app.png)

Choose `No` if you haven't yet published your app to the store. You can connect your Play Store app with this app id when your app is published.

![set_up_a_new_app](https://sleepingkit.github.io/myblog/assets/set_up_a_new_app.png)

There are 4 types of ads: Banner, Interstitial, Rewarded video, Native.

Choose the suitable ads for your app. 

![add_ad_units](https://sleepingkit.github.io/myblog/assets/add_ad_units.png)

You will get the ad unit id after `SELECT` the ads above. The name of the ads is up to you.

![ad_units](https://sleepingkit.github.io/myblog/assets/ad_units.png)

### Android

AdMob 17 requires the `App ID` to be included in the `AndroidManifest.xml`. Failure to do so will result in a crash on launch of your app. The line should look like:

```xml
<meta-data
    android:name="com.google.android.gms.ads.APPLICATION_ID"
    android:value="[ADMOB_APP_ID]"/>
```

### iOS

Starting in version 7.42.0, you are required to add your AdMob app ID in your **Info.plist** file under the Runner directory. You can add it using Xcode or edit the file manually:

```xml
<dict>
	<key>GADApplicationIdentifier</key>
	<string>ca-app-pub-################~##########</string>
</dict>
```

You can find your **APP ID** in App settings

![app_id](https://sleepingkit.github.io/myblog/assets/app_id.png)



## Initializing the Plugins
For me, I would like to store all the IDs in a class
``` dart
class GoogleAdmob {
  static String appID = "ca-app-pub-xxxxxxxxxxxxx~xxxxxxxxxxxx";
  static String bannerID = "ca-app-pub-xxxxxxxxxxxxxxxx/xxxxxxxxxx";
  static String intID = "ca-app-pub-xxxxxxxxxxxxxxxx/xxxxxxxxxx";
  static String rewardID = "ca-app-pub-xxxxxxxxxxxxxxxx/xxxxxxxxxx";
}
```

Add the package [firebase_admob] into your project.

Paste this line on your home page `initState()`
```dart
FirebaseAdMob.instance.initialize(appId: GoogleAdmob.appID);
```

## Add BannerAd
Create a BannerAd object. Test the ad can be shown by using the testID.
``` dart
BannerAd myBanner = BannerAd(
  // Replace the testAdUnitId with an ad unit id from the AdMob dash.
  // https://developers.google.com/admob/android/test-ads
  // https://developers.google.com/admob/ios/test-ads
  adUnitId: BannerAd.testAdUnitId, //Replace this id with your BannerAd id you have created 
  size: AdSize.smartBanner
);
```
This line will show the banner at the bottom.
``` dart
myBanner
  // typically this happens well before the ad is shown
  ..load()
  ..show(
    anchorType: AnchorType.bottom,
  );
```
You can dispose the Banner:
``` dart
myBanner.dispose()
```

## Add InterstitialAd (A Full Screen Ad)

Basically same as BannerAd, you can check the [firebase_admob] document directly.


## Add RewardedVideoAd

`targetingInfo` must be added and the reward video must be loaded before show.

```dart
RewardedVideoAd.instance.load(myAdMobAdUnitId, targetingInfo);
```

Show rewarded video:

```dart
RewardedVideoAd.instance.show();
```
This can loaded the next video ads after the video ad was shown by using the listener.
``` dart
RewardedVideoAd.instance.listener =
        (RewardedVideoAdEvent event, {String rewardType, int rewardAmount}) {
      if (event == RewardedVideoAdEvent.rewarded) {
        setState(() {
          // Here, apps should update state to reflect the reward.
          // Give the reward to your user! He has watched the whole video!
        });
      } else if (event == RewardedVideoAdEvent.closed) {
        RewardedVideoAd.instance
            .load(adUnitId: GoogleAdmob.rewardID, targetingInfo: targetingInfo);
      }
    };
```

## Add NativeAd
Native ad can insert into anywhere you like, but I will use another package [flutter_native_admob]. This is much easier to use.

Follow the insturction of the package you will add the native ad easily. The only things I want to mention is that this native ad package will cause `RuntimeException: FlutterJNI.ensureAttachedToNative`. But this seems nothing happens to the app, so never mind? Tell me if it cause some bug in your app.

## Attention
- You may find that ads `cannot show` when you are using your own ID just created. Don't worry, this is because you have not set up your build `Release` setting. [Singing your app](https://flutter.dev/docs/deployment/android#signing-the-app) and see if it works.

- You will see the real ad when you build the `Release` version, and please `don't click` the ad by yourself, Google is watching you! He may warn you if you are doing this.

- If your Admob account is `just created`, you may need to wait for the verification, your ads will be shown after received a verified email from Google.
  
[flutter_native_admob]: https://pub.dev/packages/flutter_native_admob
[firebase_admob]: https://pub.dev/packages/firebase_admob