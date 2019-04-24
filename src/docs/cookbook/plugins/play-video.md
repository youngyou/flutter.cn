---
title: Play and pause a video
title: 视频的播放和暂停
prev:
  title: Storing key-value data on disk
  title: 存储键值对数据
  path: /docs/cookbook/persistence/key-value
next:
  title: Take a picture using the Camera
  title: 使用 Camera 插件实现拍照功能
  path: /docs/cookbook/plugins/picture-using-camera
---

Playing videos is a common task in app development, and Flutter apps are no
exception. In order to play videos, the Flutter team provides the
[`video_player`]({{site.pub-pkg}}/video_player) plugin. You can
use the `video_player` plugin to play videos stored on the file system, as an
asset, or from the internet.

On iOS, the `video_player` plugin makes use of
[`AVPlayer`](https://developer.apple.com/documentation/avfoundation/avplayer) to
handle playback. On Android, it uses
[`ExoPlayer`](https://google.github.io/ExoPlayer/).

This recipe demonstrates how to use the `video_player` package to stream a
video from the internet with basic play and pause controls.

## Directions

  1. Add the `video_player` dependency
  2. Add permissions to your app
  3. Create and initialize a `VideoPlayerController`
  4. Display the video player
  5. Play and pause the video

## 1. Add the `video_player` dependency

This recipe depends on one Flutter plugin: `video_player`. First, add this
dependency to your `pubspec.yaml`.

```yaml
dependencies:
  flutter:
    sdk: flutter
  video_player:
```

## 2. Add permissions to your app

Next, you need to ensure your app has the correct permissions to stream videos
from the internet. To do so, update your `android` and `ios` configurations.

### Android

Add the following permission to the `AndroidManifest.xml` just after the
`<application>` definition. The `AndroidManifest.xml` can be found at `<project
root>/android/app/src/main/AndroidManifest.xml`

<!-- skip -->
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <application ...>
        
    </application>

    <uses-permission android:name="android.permission.INTERNET"/>
</manifest>
```

### iOS

For iOS, you need to add the following to your `Info.plist` file found at 
`<project root>/ios/Runner/Info.plist`. 

<!-- skip -->
```xml
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSAllowsArbitraryLoads</key>
  <true/>
</dict>
```

{{site.alert.warning}}
The `video_player` plugin does not work on iOS simulators. You must test videos 
on real iOS devices.
{{site.alert.end}}

## 3. Create and initialize a `VideoPlayerController`

Now that you have the `video_player` plugin installed with the correct
permissions, you need to create a `VideoPlayerController`. The
`VideoPlayerController` class allows you to connect to different types of
videos and control playback.

Before you can play videos, you must also `initialize` the controller. This
establishes the connection to the video and prepare the controller for playback.

To create an initialize the `VideoPlayerController`, please:

  1. Create a `StatefulWidget` with a companion `State` class 
  2. Add a variable to the `State` class to store the `VideoPlayerController`
  3. Add a variable to the `State` class to store the `Future` returned from
  `VideoPlayerController.initialize`
  4. Create and initialize the controller in the `initState` method
  5. Dispose of the controller in the `dispose` method
  
<!-- skip -->
```dart
class VideoPlayerScreen extends StatefulWidget {
  VideoPlayerScreen({Key key}) : super(key: key);

  @override
  _VideoPlayerScreenState createState() => _VideoPlayerScreenState();
}

class _VideoPlayerScreenState extends State<VideoPlayerScreen> {
  VideoPlayerController _controller;
  Future<void> _initializeVideoPlayerFuture;

  @override
  void initState() {
    // Create an store the VideoPlayerController. The VideoPlayerController
    // offers several different constructors to play videos from assets, files,
    // or the internet.
    _controller = VideoPlayerController.network(
      'https://flutter.github.io/assets-for-api-docs/assets/videos/butterfly.mp4',
    );

    _initializeVideoPlayerFuture = _controller.initialize();

    super.initState();
  }

  @override
  void dispose() {
    // Ensure you dispose the VideoPlayerController to free up resources
    _controller.dispose();

    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    // Show the video in the next step
  }
}
```

## 4. Display the video player

Now, it's time to display the video. The `video_player` plugin provides the
[`VideoPlayer`]({{site.pub-api}}/video_player/latest/video_player/VideoPlayer-class.html)
Widget to display the video initialized by the `VideoPlayerController`. By
default, the `VideoPlayer` Widget takes up as much space as possible. This
often isn't ideal for videos because they are meant to be displayed in a
specific aspect ratio, such as 16x9 or 4x3.

Therefore, you can wrap the `VideoPlayer` widget in an
[`AspectRatio`]({{site.api}}/flutter/widgets/AspectRatio-class.html)
widget to ensure the video is the correct proportions.

Furthermore, you must display the `VideoPlayer` widget after the
`_initializeVideoPlayerFuture` completes. You can use a `FutureBuilder` to
display a loading spinner until finishes initializing. Note: initializing the
controller does not begin playback.

<!-- skip -->
```dart
// Use a FutureBuilder to display a loading spinner while you wait for the
// VideoPlayerController to finish initializing.
FutureBuilder(
  future: _initializeVideoPlayerFuture,
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.done) {
      // If the VideoPlayerController has finished initialization, use
      // the data it provides to limit the Aspect Ratio of the VideoPlayer
      return AspectRatio(
        aspectRatio: _controller.value.aspectRatio,
        // Use the VideoPlayer widget to display the video
        child: VideoPlayer(_controller),
      );
    } else {
      // If the VideoPlayerController is still initializing, show a
      // loading spinner
      return Center(child: CircularProgressIndicator());
    }
  },
)
```

## 5. Play and pause the video

By default, the video starts in a paused state. To begin playback,
call the
[`play`]({{site.pub-api}}/video_player/latest/video_player/VideoPlayerController/play.html)
method provided by the `VideoPlayerController`. To pause playback, call the
[`pause`]({{site.pub-api}}/video_player/latest/video_player/VideoPlayerController/pause.html)
method.

For this example, add a `FloatingActionButton` to your app that displays a play
or pause icon depending on the situation. When the user taps the button, play
the video if it's currently paused, or pause the video if it's playing.

<!-- skip -->
```dart
FloatingActionButton(
  onPressed: () {
    // Wrap the play or pause in a call to `setState`. This ensures the correct 
    // icon is shown
    setState(() {
      // If the video is playing, pause it.
      if (_controller.value.isPlaying) {
        _controller.pause();
      } else {
        // If the video is paused, play it
        _controller.play();
      }
    });
  },
  // Display the correct icon depending on the state of the player.
  child: Icon(
    _controller.value.isPlaying ? Icons.pause : Icons.play_arrow,
  ),
)
``` 
 
## Complete Example

```dart
import 'dart:async';

import 'package:flutter/material.dart';
import 'package:video_player/video_player.dart';

void main() => runApp(VideoPlayerApp());

class VideoPlayerApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Video Player Demo',
      home: VideoPlayerScreen(),
    );
  }
}

class VideoPlayerScreen extends StatefulWidget {
  VideoPlayerScreen({Key key}) : super(key: key);

  @override
  _VideoPlayerScreenState createState() => _VideoPlayerScreenState();
}

class _VideoPlayerScreenState extends State<VideoPlayerScreen> {
  VideoPlayerController _controller;
  Future<void> _initializeVideoPlayerFuture;

  @override
  void initState() {
    // Create and store the VideoPlayerController. The VideoPlayerController
    // offers several different constructors to play videos from assets, files,
    // or the internet.
    _controller = VideoPlayerController.network(
      'https://flutter.github.io/assets-for-api-docs/assets/videos/butterfly.mp4',
    );

    // Initialize the controller and store the Future for later use
    _initializeVideoPlayerFuture = _controller.initialize();

    // Use the controller to loop the video
    _controller.setLooping(true);

    super.initState();
  }

  @override
  void dispose() {
    // Ensure you dispose the VideoPlayerController to free up resources
    _controller.dispose();

    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Butterfly Video'),
      ),
      // Use a FutureBuilder to display a loading spinner while you wait for the
      // VideoPlayerController to finish initializing.
      body: FutureBuilder(
        future: _initializeVideoPlayerFuture,
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.done) {
            // If the VideoPlayerController has finished initialization, use
            // the data it provides to limit the Aspect Ratio of the Video
            return AspectRatio(
              aspectRatio: _controller.value.aspectRatio,
              // Use the VideoPlayer widget to display the video
              child: VideoPlayer(_controller),
            );
          } else {
            // If the VideoPlayerController is still initializing, show a
            // loading spinner
            return Center(child: CircularProgressIndicator());
          }
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          // Wrap the play or pause in a call to `setState`. This ensures the
          // correct icon is shown
          setState(() {
            // If the video is playing, pause it.
            if (_controller.value.isPlaying) {
              _controller.pause();
            } else {
              // If the video is paused, play it
              _controller.play();
            }
          });
        },
        // Display the correct icon depending on the state of the player.
        child: Icon(
          _controller.value.isPlaying ? Icons.pause : Icons.play_arrow,
        ),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}
```
