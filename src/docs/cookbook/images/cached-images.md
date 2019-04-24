---
title: Working with cached images
title: 使用缓存图片
prev:
  title: Fade in images with a placeholder
  title: 占位符和网络图片淡入
  path: /docs/cookbook/images/fading-in-images
next:
  title: Basic List
  title: 基础列表
  path: /docs/cookbook/lists/basic-list
---

In some cases, it can be handy to cache images as they're downloaded from the
web so they can be used offline. For this purpose, you'll employ the
[`cached_network_image`]({{site.pub-pkg}}/cached_network_image) package.

In addition to caching, the cached_image_network package also supports
placeholders and fading images in as they're loaded.

<!-- skip -->
```dart
CachedNetworkImage(
  imageUrl: 'https://picsum.photos/250?image=9',
);
```

## Adding a placeholder

The `cached_network_image` package allows you to use any Widget as a
placeholder. In this example, you'll display a spinner while the image loads.

<!-- skip -->
```dart
CachedNetworkImage(
  placeholder: CircularProgressIndicator(),
  imageUrl: 'https://picsum.photos/250?image=9',
);
```

## Complete example

<!-- skip -->
```dart
import 'package:flutter/material.dart';
import 'package:cached_network_image/cached_network_image.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final title = 'Cached Images';

    return MaterialApp(
      title: title,
      home: Scaffold(
        appBar: AppBar(
          title: Text(title),
        ),
        body: Center(
          child: CachedNetworkImage(
            placeholder: CircularProgressIndicator(),
            imageUrl:
                'https://picsum.photos/250?image=9',
          ),
        ),
      ),
    );
  }
}
```
