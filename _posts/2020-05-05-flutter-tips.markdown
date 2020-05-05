---
layout: post
title:  "Flutter Tips!"
date:   2020-05-05 10:49:31 +0800
categories: flutter 
---
# Dismiss keyboard onTap
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

# TextField avoid the bottom widget overflow
> add `resizeToAvoidBottomPadding` equal to false to the `Scaffold` widget

# ListView image rebuild when scroll
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

# ListTile onTap no ink effect
> Remove the parent widget `Container` of the `ListTile`
