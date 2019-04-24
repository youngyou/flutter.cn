---
title: Storing key-value data on disk
title: 存储键值对数据
prev:
  title: Reading and Writing Files
  title: 文件读写
  path: /docs/cookbook/persistence/reading-writing-files
next:
  title: Play and pause a video
  path: /docs/cookbook/plugins/play-video
---

If you have a relatively small collection of key-values that you'd like
to save, you can use the
[shared_preferences]({{site.pub}}/packages/shared_preferences) plugin.

Normally you would have to write native platform integrations for storing
data on both platforms. Fortunately, the
[shared_preferences]({{site.pub-pkg}}/shared_preferences)
plugin can be used to persist key-value data on disk. The shared preferences
plugin wraps `NSUserDefaults` on iOS and `SharedPreferences` on Android,
providing a persistent store for simple data.

## Directions

  1. Add the dependency
  2. Save Data
  3. Read Data
  4. Remove Data

## 1. Add the dependency

Before starting, you need to add the
[shared_preferences]({{site.pub-pkg}}/shared_preferences)
plugin to the `pubspec.yaml` file:

```yaml
dependencies:
  flutter:
    sdk: flutter
  shared_preferences: "<newest version>"
```

## 2. Save data

To persist data, use the setter methods provided by the
`SharedPreferences` class. Setter methods are available for various primitive
types, such as `setInt`, `setBool`, and `setString`.

Setter methods do two things: First, synchronously update the key-value pair
in-memory. Then, persist the data to disk.

<!-- skip -->
```dart
// obtain shared preferences
final prefs = await SharedPreferences.getInstance();

// set value
prefs.setInt('counter', counter);
```

## 3. Read data

To read data, use the appropriate getter method provided by the
`SharedPreferences` class. For each setter there is a corresponding getter.
For example, you can use the `getInt`, `getBool`, and `getString` methods.

<!-- skip -->
```dart
final prefs = await SharedPreferences.getInstance();

// Try reading data from the counter key. If it does not exist, return 0.
final counter = prefs.getInt('counter') ?? 0;
```

## 4. Remove data

To delete data, use the `remove` method.

<!-- skip -->
```dart
final prefs = await SharedPreferences.getInstance();

prefs.remove('counter');
```

## Supported types

While it is easy and convenient to use key-value storage, it has limitations:

* Only primitive types can be used: `int`, `double`, `bool`, `string` and
  `stringList`
* It's not designed to store a lot of data.

For more information about Shared Preferences on Android, see
[Shared preferences
documentation]({{site.android-dev}}/guide/topics/data/data-storage#pref)
on the Android developers website.

## Testing support

It can be a good idea to test code that persists data using
`shared_preferences`. To do so, you'll need to mock out the
`MethodChannel` used by the `shared_preferences` library.

You can populate `SharedPreferences` with initial values in your tests
by running the following code in a `setupAll` method in your test files:

<!-- skip -->
```dart
const MethodChannel('plugins.flutter.io/shared_preferences')
  .setMockMethodCallHandler((MethodCall methodCall) async {
    if (methodCall.method == 'getAll') {
      return <String, dynamic>{}; // set initial values here if desired
    }
    return null;
  });
```

## Example

```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of the application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Shared preferences demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Shared preferences demo'),
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

  @override
  void initState() {
    super.initState();
    _loadCounter();
  }

  //Loading counter value on start
  _loadCounter() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _counter = (prefs.getInt('counter') ?? 0);
    });
  }

  //Incrementing counter after click
  _incrementCounter() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    setState(() {
      _counter = (prefs.getInt('counter') ?? 0) + 1;
      prefs.setInt('counter', _counter);
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
              'You have pushed the button this many times:',
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
