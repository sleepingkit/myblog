---
title:  "Flutter Tips!"
last_modified_at: 2020-07-13 00:00:00 +0800
categories:
  - flutter
  - tips
---

## Constructor assign parameters value to field values
```dart
class Test extends StatelessWidget {
  final Color color;
  final String text;
  final bool isSquare;
  final double size;
  final Color textColor;
  final int textLength;
  final List<bool> boolList;
  // Constructor should not be const if you want to do this
  Test({
    Key key,
    this.color,
    this.text,
    this.isSquare,
    this.size = 16,
    this.boolList=const []; // empty list should be const
    this.textColor = const Color(0xff505050),
  }) : textLength=text.length,super(key: key); //super must be initialized at the last
}
```

## Callback After `build` context
```dart
executeAfterWholeBuildProcess(){
  // Do something you want after build
}

@override
Widget build(BuildContext context) {
  WidgetsBinding.instance
    .addPostFrameCallback((_) => executeAfterWholeBuildProcess());
```

## Partial blur of image
``` dart
Stack(
  children: <Widget>[
    Image.asset(
      'assets/images/background.jpg',
      width: _width,
      height: _height,
      fit: BoxFit.cover,
    ),
    FlutterLogo(size: 80, colors: Colors.red),
    Positioned(
      top: 0,
      left: 0,
      width: _blurWidth,
      height: _blurHeight,
      // Note: without ClipRect, the blur region will be expanded to full
      // size of the Image instead of custom size
      child: ClipRect(
        child: BackdropFilter(
          filter: ImageFilter.blur(sigmaX: _sigmaX, sigmaY: _sigmaY),
          child: Container(
            color: Colors.black.withOpacity(_opacity),
          ),
        ),
      ),
    )
  ],
);
```

## Dismiss keyboard onTap
> Wrap the whole page widget with `GestureDetector` and change the current focus

{% highlight dart %}
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        FocusScopeNode currentFocus = FocusScope.of(context);
         if (!currentFocus.hasPrimaryFocus) {
          currentFocus.unfocus();
        }
      },
      child: MaterialApp(
        title: 'Flutter Demo',
        theme: ThemeData(
          primarySwatch: Colors.blue,
        ),
        home: MyHomePage(),
      ),
    );
  }
}
{% endhighlight %}

## TextField avoid the bottom widget overflow
> Set `resizeToAvoidBottomPadding` equal to false to the `Scaffold` widget

## ListView image rebuild when scroll
> Add keep alive  
> Ref: [flutter-listview-keepalive-after-some-scroll](https://stackoverflow.com/questions/52541172/flutter-listview-keepalive-after-some-scroll)


{% highlight dart %}
class Foo extends StatefulWidget {
  @override
  FooState createState() {
    return new FooState();
  }
}
class FooState extends State<Foo> with AutomaticKeepAliveClientMixin {
  @override
  Widget build(BuildContext context) {
    return Container(
    );
  }

  @override
  bool get wantKeepAlive => true;
}
{% endhighlight %}

## ListTile onTap no ink effect
> Remove the parent widget `Container` of the `ListTile`
