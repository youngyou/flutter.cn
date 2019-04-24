---
title: Take a picture using the Camera
title: 使用 Camera 插件实现拍照功能
prev:
  title: Play and pause a video
  title: 视频的播放和暂停
  path: /docs/cookbook/plugins/play-video
next:
  title: An introduction to integration testing
  title: 集成测试介绍
  path: /docs/cookbook/testing/integration/introduction
---

Many apps require working with the device's cameras to take photos and videos.
Flutter provides the [`camera`]({{site.pub-pkg}}/camera) plugin
for this purpose. The `camera` plugin provides tools to get a list of the
available cameras, display a preview coming from a specific camera, and take
photos or videos.

This recipe demonstrates how to use the `camera` plugin to display a preview, 
take a photo, and display it.

## Directions

  1. Add the required dependencies
  2. Get a list of the available cameras
  3. Create and initialize the `CameraController`
  4. Use a `CameraPreview` to display the camera's feed
  5. Take a picture with the `CameraController`
  6. Display the picture with an `Image` Widget

## 1. Add the required dependencies

To complete this recipe, you need to add three dependencies to your app:

  - [`camera`]({{site.pub-pkg}}/camera) - Provides tools to work with the cameras on device
  - [`path_provider`]({{site.pub-pkg}}/path_provider) - Finds the correct paths to store images
  - [`path`]({{site.pub-pkg}}/path) - Creates paths that work on any platform

```yaml
dependencies:
  flutter:
    sdk: flutter
  camera:
  path_provider:
  path:
```

## 2. Get a list of the available cameras

Next, you can get a list of available cameras using the `camera` plugin.

<!-- skip -->
```dart
// Obtain a list of the available cameras on the device.
final cameras = await availableCameras();

// Get a specific camera from the list of available cameras
final firstCamera = cameras.first; 
```

## 3. Create and initialize the `CameraController`

Once you have a camera to work with, you need to create and initialize a
`CameraController`. This process establishes a connection to the device's camera
that allows you to control the camera and display a preview of the camera's
feed.

To achieve this, please:

  1. Create a `StatefulWidget` with a companion `State` class 
  2. Add a variable to the `State` class to store the `CameraController`
  3. Add a variable to the `State` class to store the `Future` returned from
  `CameraController.initialize`
  4. Create and initialize the controller in the `initState` method
  5. Dispose of the controller in the `dispose` method
  
<!-- skip -->
```dart
// A screen that takes in a list of Cameras and the Directory to store images.
class TakePictureScreen extends StatefulWidget {
  final CameraDescription camera;

  const TakePictureScreen({
    Key key,
    @required this.camera,
  }) : super(key: key);

  @override
  TakePictureScreenState createState() => TakePictureScreenState();
}

class TakePictureScreenState extends State<TakePictureScreen> {
  // Add two variables to the state class to store the CameraController and
  // the Future
  CameraController _controller;
  Future<void> _initializeControllerFuture;

  @override
  void initState() {
    super.initState();
    // In order to display the current output from the Camera, you need to
    // create a CameraController.
    _controller = CameraController(
      // Get a specific camera from the list of available cameras
      widget.camera,
      // Define the resolution to use
      ResolutionPreset.medium,
    );

    // Next, you need to initialize the controller. This returns a Future
    _initializeControllerFuture = _controller.initialize();
  }

  @override
  void dispose() {
    // Make sure to dispose of the controller when the Widget is disposed
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    // Fill this out in the next steps
  }
}
```

{{site.alert.warning}}
If you do not initialize the `CameraController`, you *cannot* use the camera
to display a preview and take pictures.
{{site.alert.end}}

## 4. Use a `CameraPreview` to display the camera's feed

Next, you can use the `CameraPreview` Widget from the `camera` package to
display a preview of the camera's feed.

Remember: You must wait until the controller has finished initializing before
working with the camera. Therefore, you must wait for the
`_initializeControllerFuture` created in the previous step to complete before
showing a `CameraPreview`.

You can use a
[`FutureBuilder`]({{site.api}}/flutter/widgets/FutureBuilder-class.html)
for exactly this purpose.

<!-- skip -->
```dart
// You must wait until the controller is initialized before displaying the
// camera preview. Use a FutureBuilder to display a loading spinner until the
// controller has finished initializing
FutureBuilder<void>(
  future: _initializeControllerFuture,
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.done) {
      // If the Future is complete, display the preview
      return CameraPreview(_controller);
    } else {
      // Otherwise, display a loading indicator
      return Center(child: CircularProgressIndicator());
    }
  },
)
```

## 5. Take a picture with the `CameraController`

You can also use the `CameraController` to take pictures using the
[`takePicture`]({{site.pub-api}}/camera/latest/camera/CameraController/takePicture.html)
method. In this example, create a `FloatingActionButton` that takes a picture
using the `CameraController` when a user taps on the button.

Saving a picture requires 3 steps:

  1. Ensure the camera is initialized
  2. Construct a path that defines where the picture should be saved
  3. Use the controller to take a picture and save the result to the path
  
It is good practice to wrap these operations in a `try / catch` block in order
to handle any errors that might occur.

<!-- skip -->
```dart
FloatingActionButton(
  child: Icon(Icons.camera_alt),
  // Provide an onPressed callback
  onPressed: () async {
    // Take the Picture in a try / catch block. If anything goes wrong,
    // catch the error.
    try {
      // Ensure the camera is initialized
      await _initializeControllerFuture;

      // Construct the path where the image should be saved using the path
      // package.
      final path = join(
        // In this example, store the picture in the temp directory. Find
        // the temp directory using the `path_provider` plugin.
        (await getTemporaryDirectory()).path,
        '${DateTime.now()}.png',
      );

      // Attempt to take a picture and log where it's been saved
      await _controller.takePicture(path);
    } catch (e) {
      // If an error occurs, log the error to the console.
      print(e);
    }
  },
)
```
## 6. Display the picture with an `Image` Widget

If you take the picture successfully, you can then display the saved picture
using an `Image` widget. In this case, the picture is stored as a file on
the device.

Therefore, you must provide a `File` to the `Image.file` constructor. You
can create an instance of the `File` class by passing in the path you created in
the previous step.

<!-- skip -->
```dart
Image.file(File('path/to/my/picture.png'))
```

## Complete Example

```dart
import 'dart:async';
import 'dart:io';

import 'package:camera/camera.dart';
import 'package:flutter/material.dart';
import 'package:path/path.dart' show join;
import 'package:path_provider/path_provider.dart';

Future<void> main() async {
  // Obtain a list of the available cameras on the device.
  final cameras = await availableCameras();

  // Get a specific camera from the list of available cameras
  final firstCamera = cameras.first;

  runApp(
    MaterialApp(
      theme: ThemeData.dark(),
      home: TakePictureScreen(
        // Pass the appropriate camera to the TakePictureScreen Widget
        camera: firstCamera,
      ),
    ),
  );
}

// A screen that allows users to take a picture using a given camera
class TakePictureScreen extends StatefulWidget {
  final CameraDescription camera;

  const TakePictureScreen({
    Key key,
    @required this.camera,
  }) : super(key: key);

  @override
  TakePictureScreenState createState() => TakePictureScreenState();
}

class TakePictureScreenState extends State<TakePictureScreen> {
  CameraController _controller;
  Future<void> _initializeControllerFuture;

  @override
  void initState() {
    super.initState();
    // In order to display the current output from the Camera, you need to
    // create a CameraController.
    _controller = CameraController(
      // Get a specific camera from the list of available cameras
      widget.camera,
      // Define the resolution to use
      ResolutionPreset.medium,
    );

    // Next, you need to initialize the controller. This returns a Future
    _initializeControllerFuture = _controller.initialize();
  }

  @override
  void dispose() {
    // Make sure to dispose of the controller when the Widget is disposed
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Take a picture')),
      // You must wait until the controller is initialized before displaying the
      // camera preview. Use a FutureBuilder to display a loading spinner until
      // the controller has finished initializing
      body: FutureBuilder<void>(
        future: _initializeControllerFuture,
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.done) {
            // If the Future is complete, display the preview
            return CameraPreview(_controller);
          } else {
            // Otherwise, display a loading indicator
            return Center(child: CircularProgressIndicator());
          }
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.camera_alt),
        // Provide an onPressed callback
        onPressed: () async {
          // Take the Picture in a try / catch block. If anything goes wrong,
          // catch the error.
          try {
            // Ensure the camera is initialized
            await _initializeControllerFuture;

            // Construct the path where the image should be saved using the path
            // package.
            final path = join(
              // In this example, store the picture in the temp directory. Find
              // the temp directory using the `path_provider` plugin.
              (await getTemporaryDirectory()).path,
              '${DateTime.now()}.png',
            );

            // Attempt to take a picture and log where it's been saved
            await _controller.takePicture(path);

            // If the picture was taken, display it on a new screen
            Navigator.push(
              context,
              MaterialPageRoute(
                builder: (context) => DisplayPictureScreen(imagePath: path),
              ),
            );
          } catch (e) {
            // If an error occurs, log the error to the console.
            print(e);
          }
        },
      ),
    );
  }
}

// A Widget that displays the picture taken by the user
class DisplayPictureScreen extends StatelessWidget {
  final String imagePath;

  const DisplayPictureScreen({Key key, this.imagePath}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Display the Picture')),
      // The image is stored as a file on the device. Use the `Image.file`
      // constructor with the given path to display the image
      body: Image.file(File(imagePath)),
    );
  }
}
```
