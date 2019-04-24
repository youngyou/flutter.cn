---
title: Fade in images with a placeholder
title: 占位符和网络图片淡入
prev:
  title: Display images from the internet
  title: 显示网络上的远程图片
  path: /docs/cookbook/images/network-image
next:
  title: Working with cached images
  title: 使用缓存图片
  path: /docs/cookbook/images/cached-images
---

When displaying images using the default `Image` widget, you might notice they
simply pop onto the screen as they're loaded. This might feel visually jarring
to your users.

Instead, wouldn't it be nice if you could display a placeholder at first,
and images would fade in as they're loaded? You can use the
[`FadeInImage`]({{site.api}}/flutter/widgets/FadeInImage-class.html)
Widget packaged with Flutter for exactly this purpose.

`FadeInImage` works with images of any type: in-memory, local assets, or images
from the internet.

## In-Memory

In this example, you'll use the
[transparent_image]({{site.pub-pkg}}/transparent_image)
package for a simple transparent placeholder.

<!-- skip -->
```dart
FadeInImage.memoryNetwork(
  placeholder: kTransparentImage,
  image: 'https://picsum.photos/250?image=9',
);
```

### Complete example

```dart
import 'package:flutter/material.dart';
import 'package:transparent_image/transparent_image.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final title = 'Fade in images';

    return MaterialApp(
      title: title,
      home: Scaffold(
        appBar: AppBar(
          title: Text(title),
        ),
        body: Stack(
          children: <Widget>[
            Center(child: CircularProgressIndicator()),
            Center(
              child: FadeInImage.memoryNetwork(
                placeholder: kTransparentImage,
                image: 'https://picsum.photos/250?image=9',
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

![Fading In Image Demo](/images/cookbook/fading-in-images.gif){:.site-mobile-screenshot}

### From asset bundle

You can also consider using local assets for placeholders. First, add the asset
to the project’s `pubspec.yaml` file (for more details see
[Assets and images](/docs/development/ui/assets-and-images)):

<!-- skip -->
```diff
 flutter:
   assets:
+    - assets/loading.gif
```

Then, use the
[FadeInImage.assetNetwork()]({{site.api}}/flutter/widgets/FadeInImage/FadeInImage.assetNetwork.html)
constructor:

<!-- skip -->
```dart
FadeInImage.assetNetwork(
  placeholder: 'assets/loading.gif',
  image: 'https://picsum.photos/250?image=9',
);
```

### Complete example

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final title = 'Fade in images';

    return MaterialApp(
      title: title,
      home: Scaffold(
        appBar: AppBar(
          title: Text(title),
        ),
        body: Center(
          child: FadeInImage.assetNetwork(
            placeholder: 'assets/loading.gif',
            image: 'https://picsum.photos/250?image=9',
          ),
        ),
      ),
    );
  }
}
```

![Asset fade-in](/images/cookbook/fading-in-asset-demo.gif){:.site-mobile-screenshot}
