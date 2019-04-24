---
title: Parsing JSON in the background
title: 在后台处理 JSON 数据解析
prev:
  title: Making authenticated requests
  title: 发起 HTTP 认证授权请求
  path: /docs/cookbook/networking/authenticated-requests
next:
  title: Working with WebSockets
  title: 发起 WebSockets 请求
  path: /docs/cookbook/networking/web-sockets
---

By default, Dart apps do all of their work on a single thread. In many cases,
this model simplifies coding and is fast enough that it does not result in
poor app performance or stuttering animations, often called "jank."

However, you may need to perform an expensive computation, such as parsing a
very large JSON document. If this work takes more than 16 milliseconds, your
users will experience jank.

To avoid jank, you need to perform expensive computations like this in the
background. On Android, this would mean scheduling work on a different thread.
In Flutter, you can use a separate
[Isolate]({{site.api}}/flutter/dart-isolate/Isolate-class.html).

## Directions

  1. Add the `http` package
  2. Make a network request using the `http` package
  3. Convert the response into a List of Photos
  4. Move this work to a separate isolate

## 1. Add the `http` package

First, add the [`http`]({{site.pub-pkg}}/http) package to your project.
The `http` package makes it easier to perform network
requests, such as fetching data from a JSON endpoint.

```yaml
dependencies:
  http: <latest_version>
```

## 2. Make a network request

In this example, you'll fetch a JSON large document that contains a list of
5000 photo objects from the [JSONPlaceholder REST
API](https://jsonplaceholder.typicode.com)
using the [http.get()]({{site.pub-api}}/http/latest/http/get.html) method.

<!-- skip -->
```dart
Future<http.Response> fetchPhotos(http.Client client) async {
  return client.get('https://jsonplaceholder.typicode.com/photos');
}
```

Note: You're providing an `http.Client` to the function in this example.
This makes the function easier to test and use in different environments.

## 3. Parse and Convert the json into a List of Photos

Next, following the guidance from the [Fetch data from the
internet](/docs/cookbook/networking/fetch-data)
recipe, you'll want to convert the `http.Response` into a list of Dart objects.
This makes the data easier to work with in the future.

### Create a `Photo` class

First, create a `Photo` class that contains data about a photo.
You will include a `fromJson` factory method to make it easy to create a
`Photo` starting with a json object.

<!-- skip -->
```dart
class Photo {
  final int id;
  final String title;
  final String thumbnailUrl;

  Photo({this.id, this.title, this.thumbnailUrl});

  factory Photo.fromJson(Map<String, dynamic> json) {
    return Photo(
      id: json['id'] as int,
      title: json['title'] as String,
      thumbnailUrl: json['thumbnailUrl'] as String,
    );
  }
}
```

### Convert the response into a List of Photos

Now, update the `fetchPhotos` function so it can return a
`Future<List<Photo>>`. To do so, you'll need to:

  1. Create a `parsePhotos` that converts the response body into a `List<Photo>`
  2. Use the `parsePhotos` function in the `fetchPhotos` function

<!-- skip -->
```dart
// A function that converts a response body into a List<Photo>
List<Photo> parsePhotos(String responseBody) {
  final parsed = json.decode(responseBody).cast<Map<String, dynamic>>();

  return parsed.map<Photo>((json) => Photo.fromJson(json)).toList();
}

Future<List<Photo>> fetchPhotos(http.Client client) async {
  final response =
      await client.get('https://jsonplaceholder.typicode.com/photos');

  return parsePhotos(response.body);
}
```

## 4. Move this work to a separate isolate

If you run the `fetchPhotos` function on a slower phone, you may notice the app
freezes for a brief moment as it parses and converts the json. This is jank,
and we want to be rid of it.

So how can we do that? By moving the parsing and conversion to a background
isolate using the [`compute`]({{site.api}}/flutter/foundation/compute.html)
function provided by Flutter. The `compute` function runs expensive
functions in a background isolate and returns the result. In this case,
we want to run the `parsePhotos` function in the background.

<!-- skip -->
```dart
Future<List<Photo>> fetchPhotos(http.Client client) async {
  final response =
      await client.get('https://jsonplaceholder.typicode.com/photos');

  // Use the compute function to run parsePhotos in a separate isolate
  return compute(parsePhotos, response.body);
}
```

## Notes on working with Isolates

Isolates communicate by passing messages back and forth. These messages can
be primitive values, such as `null`, `num`, `bool`, `double`, or `String`, or
simple objects such as the `List<Photo>` in this example.

You may experience errors if you try to pass more complex objects, such as
a `Future` or `http.Response` between isolates.

## Complete example

```dart
import 'dart:async';
import 'dart:convert';

import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

Future<List<Photo>> fetchPhotos(http.Client client) async {
  final response =
      await client.get('https://jsonplaceholder.typicode.com/photos');

  // Use the compute function to run parsePhotos in a separate isolate
  return compute(parsePhotos, response.body);
}

// A function that converts a response body into a List<Photo>
List<Photo> parsePhotos(String responseBody) {
  final parsed = json.decode(responseBody).cast<Map<String, dynamic>>();

  return parsed.map<Photo>((json) => Photo.fromJson(json)).toList();
}

class Photo {
  final int albumId;
  final int id;
  final String title;
  final String url;
  final String thumbnailUrl;

  Photo({this.albumId, this.id, this.title, this.url, this.thumbnailUrl});

  factory Photo.fromJson(Map<String, dynamic> json) {
    return Photo(
      albumId: json['albumId'] as int,
      id: json['id'] as int,
      title: json['title'] as String,
      url: json['url'] as String,
      thumbnailUrl: json['thumbnailUrl'] as String,
    );
  }
}

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final appTitle = 'Isolate Demo';

    return MaterialApp(
      title: appTitle,
      home: MyHomePage(title: appTitle),
    );
  }
}

class MyHomePage extends StatelessWidget {
  final String title;

  MyHomePage({Key key, this.title}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(title),
      ),
      body: FutureBuilder<List<Photo>>(
        future: fetchPhotos(http.Client()),
        builder: (context, snapshot) {
          if (snapshot.hasError) print(snapshot.error);

          return snapshot.hasData
              ? PhotosList(photos: snapshot.data)
              : Center(child: CircularProgressIndicator());
        },
      ),
    );
  }
}

class PhotosList extends StatelessWidget {
  final List<Photo> photos;

  PhotosList({Key key, this.photos}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return GridView.builder(
      gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
        crossAxisCount: 2,
      ),
      itemCount: photos.length,
      itemBuilder: (context, index) {
        return Image.network(photos[index].thumbnailUrl);
      },
    );
  }
}
```

![Isolate Demo](/images/cookbook/isolate.gif){:.site-mobile-screenshot}
