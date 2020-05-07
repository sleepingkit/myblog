---
title:  "Flutter Android Platform Channel"
categories: Flutter
---

## Import Flutter Module into the native Android Project
1. Create a flutter module and build an aar
``` shell
flutter build aar
```
You will see the followings result after built, follow those steps
``` shell
Consuming the Module
  1. Open <host>/app/build.gradle
  2. Ensure you have the repositories configured, otherwise add them:

      repositories {
        maven {
            url 'yourpatch/test_module/build/host/outputs/repo'
        }
        maven {
            url 'https://storage.googleapis.com/download.flutter.io'
        }
      }

  3. Make the host app depend on the Flutter module:

    dependencies {
      debugImplementation 'com.example.testmodule:flutter_debug:1.0'
      profileImplementation 'com.example.testmodule:flutter_profile:1.0'
      releaseImplementation 'com.example.testmodule:flutter_release:1.0'
    }


  4. Add the `profile` build type:

    android {
      buildTypes {
        profile {
          initWith debug
        }
      }
    }

```
2. The Flutter Android engine uses Java 8 features.  
Before attempting to connect your Flutter module project to your host Android app, ensure that your host Android app declares the following source compatibility within your app’s build.gradle file, under the android { } block, such as: 
``` 
android {
  //...
  compileOptions {
    sourceCompatibility 1.8
    targetCompatibility 1.8
  }
}
```
3. Add flutter activity in *./AndroidManifest.xml*
``` xml
        <activity
            android:name="io.flutter.embedding.android.FlutterActivity"
            android:configChanges="orientation|keyboardHidden|keyboard|screenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
            android:hardwareAccelerated="true"
            android:windowSoftInputMode="adjustResize" />
```

## Start flutter activity
In *MainActivity.java*
Pre-warm the flutter engine
``` java
flutterEngine = new FlutterEngine(this);
        GeneratedPluginRegistrant.registerWith(flutterEngine);
// Start executing Dart code to pre-warm the FlutterEngine.
flutterEngine.getDartExecutor().executeDartEntrypoint(
        DartExecutor.DartEntrypoint.createDefault()
);
```
 Cache the flutter engine
``` java
 FlutterEngineCache
                .getInstance()
                .put("my_engine_id", flutterEngine);
```
Start flutter activity with cached flutter engine
``` java
// This method is used so that your flutter activity
// can cover the entire screen.
getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
        WindowManager.LayoutParams.FLAG_FULLSCREEN);

// Defining the FlutterActivity to display
// the Flutter UI within this host app.
startActivity(
        FlutterActivity
                .withCachedEngine("my_engine_id")
                .build(this)
);
```

## Send data from Flutter to android
In android side *MainActivity.java*, setup a method channel to receive the message from flutter, `"com.example.importfluttermodule/data"` should be an unique id which created by yourself.

``` java
MethodChannel methodChannel = new MethodChannel(flutterEngine.getDartExecutor().getBinaryMessenger(), "com.example.importfluttermodule/data");
        methodChannel.setMethodCallHandler(
                (call, result) -> {
                    if (call.method.equals("sendDataFromFlutter")) {
                        Log.w("receive", call.arguments.toString()); // arguments passed from flutter
                        result.success("receive your message"); // return a feedback message to flutter
                    } else {
                        result.notImplemented();
                    }
                });
```

In Flutter side *main.dart*, create a mehod channel with the same channel id
``` dart
static const platform = const MethodChannel(
      'com.example.importfluttermodule/data');
var data = await platform.invokeMethod('sendDataFromFlutter',"hello from flutter");
```
When `invokeMethod` is called, Android side will trigger the method call handler and receive the message from Flutter. So `hello from flutter is printed in the console.

## Send data from android to Flutter
In android side *MainActivity.java*, send data by using invokeMethod
``` java
methodChannel.invokeMethod("passToFlutter", "Hello from android");
```
In Flutter side *main.dart*
``` dart
platform.setMethodCallHandler(this.receiveData);

Future<void> receiveData(MethodCall call) async {
    final String data = call.arguments;
    switch(call.method) {
        case "passToFlutter":
        setState(() {
            text=data;
        });
        break;
    }
}
```
## Complete scripts

*MainActivity.java*
``` java
package com.example.importfluttermodule;

import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.WindowManager;

import io.flutter.embedding.android.FlutterActivity;
import io.flutter.embedding.engine.FlutterEngine;
import io.flutter.embedding.engine.FlutterEngineCache;
import io.flutter.embedding.engine.dart.DartExecutor;
import io.flutter.plugin.common.MethodChannel;
import io.flutter.plugins.GeneratedPluginRegistrant;

public class MainActivity extends FlutterActivity {
    FlutterEngine flutterEngine;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        flutterEngine = new FlutterEngine(this);
        GeneratedPluginRegistrant.registerWith(flutterEngine);


        // Start executing Dart code to pre-warm the FlutterEngine.
        flutterEngine.getDartExecutor().executeDartEntrypoint(
                DartExecutor.DartEntrypoint.createDefault()
        );

////        Cache the FlutterEngine to be used by FlutterActivity.
        FlutterEngineCache
                .getInstance()
                .put("my_engine_id", flutterEngine);
        MethodChannel methodChannel = new MethodChannel(flutterEngine.getDartExecutor().getBinaryMessenger(), "com.example.importfluttermodule/data");
        methodChannel.setMethodCallHandler(
                (call, result) -> {
                    if (call.method.equals("sendDataFromFlutter")) {
                        Log.w("receive", call.arguments.toString());
                        result.success("receive your message");
                    } else {
                        result.notImplemented();
                    }
                });
        methodChannel.invokeMethod("passToFlutter", "Hello from android");

    }


    public void openFlutter(View view) {
        // This method is used so that your flutter activity
        // can cover the entire screen.
        getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
                WindowManager.LayoutParams.FLAG_FULLSCREEN);

        // Defining the FlutterActivity to display
        // the Flutter UI within this host app.
        startActivity(
                FlutterActivity
                        .withCachedEngine("my_engine_id")
                        .build(this)
        );
    }

}


```

*main.dart*
``` dart

import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() => runApp(MyApp());


class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title:"Success!"),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);
  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;
  static const platform = const MethodChannel(
      'com.example.importfluttermodule/data');
  String text="nothing";
  @override
  void initState() {
    super.initState();
    sendDataFromFlutter();
    platform.setMethodCallHandler(this.receiveData);

  }
  
  Future<void> receiveData(MethodCall call) async {
    final String data = call.arguments;
    switch(call.method) {
      case "passToFlutter":
        setState(() {
          text=data;
        });
        break;
    }
  }

  Future sendDataFromFlutter() async {
    try {
      var data = await platform.invokeMethod('sendDataFromFlutter',"hello from flutter");
      setState(() {
        text = data;
      });
      print("data: $data");
    }catch(e){
      print(e);
    }
  }

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              text,
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}

```