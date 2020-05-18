---
title:  "Flutter X Firebase dynamic link!"
last_modified_at: 2020-05-04 00:00:00 +0800
categories:
  - jekyll
  - github
  - setup
---

# Create a Firebase project
Login [Firebase](https://firebase.google.com/) and create a new project.

[Create your debug SHA-256 key]("https://developers.google.com/android/guides/client-auth"). 

Windows:

``` shell
keytool -list -v \
-alias androiddebugkey -keystore %USERPROFILE%\.android\debug.keystore
```

Mac/Linux:

``` shell
keytool -list -v \
-alias androiddebugkey -keystore ~/.android/debug.keystore
```



Then go to Project settings and paste your debug SHA-256 fingerprint.

![Screenshot 2020-05-18 at 2.43.06 PM](https://sleepingkit.github.io/myblog/assets/Screenshot 2020-05-18 at 2.43.06 PM.png)

![Screenshot 2020-05-18 at 2.45.22 PM](https://sleepingkit.github.io/myblog/assets/Screenshot 2020-05-18 at 2.45.22 PM.png)


Then go to the dynamic links page. Click `Get started`.

![dynamic_links](https://sleepingkit.github.io/myblog/assets/dynamic_links.png)



## Implement Firebase dynamic links

Add [firebase_dynamic_links](https://pub.dev/packages/firebase_dynamic_links) into your project.

Initial the dynamic link setting in your root page.

``` dart
void initDynamicLinks() async {
    final PendingDynamicLinkData data =
        await FirebaseDynamicLinks.instance.getInitialLink();
    final Uri deepLink = data?.link;
		
  	// ------------------
  	//This will trigger when the app is not opened/killed and the dynamic links is clicked.
    if (deepLink != null) {
      // deeplink.path == https://the-link-you-want-to-receive/?par=par_you_want_to_pass
      Navigator.pushNamed(context, deepLink.path);
    }
  	// ------------------

  	// ------------------
  	// This will trigger when the app is opened and the dynmaic links is clicked
    FirebaseDynamicLinks.instance.onLink(
        onSuccess: (PendingDynamicLinkData dynamicLink) async {
      final Uri deepLink = dynamicLink?.link;

      if (deepLink != null) {
        Navigator.pushNamed(context, deepLink.path);
      }
    }, onError: (OnLinkErrorException e) async {
      print('onLinkError');
      print(e.message);
    });
  	// -----------------
  }
```

Create the dynamic links 
``` dart 
Future<void> _createDynamicLink(bool short) async {
    setState(() {
      _isCreatingLink = true;
    });

    final DynamicLinkParameters parameters = DynamicLinkParameters(
      uriPrefix: 'https://projectname.page.link', // Your firebase dynamic link prefix
      link: Uri.parse('https://the-link-you-want-to-receive/?par=par_you_want_to_pass'),
      androidParameters: AndroidParameters(
        packageName: 'com.example.test', // this is the package name of your project, user will pop up the play store if he hasn't install the app.
        minimumVersion: 0,
      ),
      dynamicLinkParametersOptions: DynamicLinkParametersOptions(
        shortDynamicLinkPathLength: ShortDynamicLinkPathLength.short, // Shorten your dynamic link
      ),
      iosParameters: IosParameters(
        bundleId: 'com.google.FirebaseCppDynamicLinksTestApp.dev',
        minimumVersion: '0',
      ),
    );

    Uri url;
    if (short) {
      final ShortDynamicLink shortLink = await parameters.buildShortLink();
      url = shortLink.shortUrl;
    } else {
      url = await parameters.buildUrl();
    }

    setState(() {
      _linkMessage = url.toString();
      _isCreatingLink = false;
    });
  }
```

See the complete [example](https://pub.dev/packages/firebase_dynamic_links#-example-tab-) of firebase_dynamic_links



