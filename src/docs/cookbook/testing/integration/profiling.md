---
title: Performance profiling
title: 性能分析
prev:
  title: An introduction to integration testing
  title: Flutter 集成测试介绍
  path: /docs/cookbook/testing/integration/introduction
next:
  title: Scrolling
  title: 列表滚动
  path: /docs/cookbook/testing/integration/scrolling
---

When it comes to mobile apps, performance is critical to user experience. Users
expect apps to have smooth scrolling and meaningful animations free of
stuttering or skipped frames, known as "jank." How can we ensure our apps are
free of jank on a wide variety of devices?

There are two options: first, we could manually test the app on different
devices. While that approach might work for a smaller app, it will become more
cumbersome as an app grows in size. Alternatively, we can run an integration
test that performs a specific task and record a performance timeline. Then, we
can examine the results to determine whether or not a specific section of our
app needs to be improved.

In this recipe, we'll learn how to write a test that records a performance
timeline while performing a specific task and saves a summary of the results to
a local file.

### Directions

  1. Write a test that scrolls through a list of items
  2. Record the performance of the app
  3. Save the results to disk
  4. Run the test
  5. Review the results

### 1. Write a test that scrolls through a list of items

In this recipe, we'll record the performance of an app as it scrolls through a
list of items. In order to focus on performance profiling, this recipe builds
upon the
[Scrolling in integration tests](/docs/cookbook/testing/integration/scrolling)
recipe.

Please follow the instructions in that recipe to create an app, instrument the
app, and write a test to verify everything works as expected.

### 2. Record the performance of the app

Next, we need to record the performance of the app as it scrolls through the
list. To achieve this task, we can use the
[`traceAction`]({{site.api}}/flutter/flutter_driver/FlutterDriver/traceAction.html)
method provided by the
[`FlutterDriver`]({{site.api}}/flutter/flutter_driver/FlutterDriver-class.html)
class.

This method runs the provided function and records a
[`Timeline`]({{site.api}}/flutter/flutter_driver/Timeline-class.html)
with detailed information about the performance of the app. In this example, we
provide a function that scrolls through the list of items, ensuring a specific
item is displayed. When the function completes, the `traceAction` method returns
a `Timeline`.

<!-- skip -->
```dart
// Record a performance timeline as we scroll through the list of items
final timeline = await driver.traceAction(() async {
  await driver.scrollUntilVisible(
    listFinder,
    itemFinder,
    dyScroll: -300.0,
  );

  expect(await driver.getText(itemFinder), 'Item 50');
});
```

### 3. Save the results to disk

Now that we've captured a performance timeline, we need a way to review it!
The `Timeline` object provides detailed information about all of the events that
took place, but it does not provide a convenient way to review the results.

Therefore, we can convert the `Timeline` into a
[`TimelineSummary`]({{site.api}}/flutter/flutter_driver/TimelineSummary-class.html).
The `TimelineSummary` can perform two tasks that make it easier to review the
results:

  1. It can write a json document on disk that summarizes the data contained
  within the `Timeline`. This summary includes information about the number of
  skipped frames, slowest build times, and more.
  2. It can save the complete `Timeline` as a json file on disk. This file can
  be opened with the Chrome browser's tracing tools found at
  [chrome://tracing](chrome://tracing).

<!-- skip -->
```dart
// Convert the Timeline into a TimelineSummary that's easier to read and
// understand.
final summary = new TimelineSummary.summarize(timeline);

// Then, save the summary to disk
summary.writeSummaryToFile('scrolling_summary', pretty: true);

// Optionally, write the entire timeline to disk in a json format. This
// file can be opened in the Chrome browser's tracing tools found by
// navigating to chrome://tracing.
summary.writeTimelineToFile('scrolling_timeline', pretty: true);
```

### 4. Run the test

After we've configured our test to capture a performance `Timeline` and save a
summary of the results to disk, we can run the test with the following command:

```
flutter drive --target=test_driver/app.dart
```

### 5. Review the results

After the test completes successfully, the `build` directory at the root of
the project contains two files:

  1. `scrolling_summary.timeline_summary.json` contains the summary. Open
  the file with any text editor to review the information contained within.
  With a more advanced setup, we could save a summary every time the test
  runs and create a graph of the results.
  2. `scrolling_timeline.timeline.json` contains the complete timeline data.
  Open the file using the Chrome browser's tracing tools found at
  [chrome://tracing](chrome://tracing). The tracing tools provide a
  convenient interface for inspecting the timeline data in order to discover
  the source of a performance issue.

#### Summary Example

```json
{
  "average_frame_build_time_millis": 4.2592592592592595,
  "worst_frame_build_time_millis": 21.0,
  "missed_frame_build_budget_count": 2,
  "average_frame_rasterizer_time_millis": 5.518518518518518,
  "worst_frame_rasterizer_time_millis": 51.0,
  "missed_frame_rasterizer_budget_count": 10,
  "frame_count": 54,
  "frame_build_times": [
    6874,
    5019,
    3638
  ],
  "frame_rasterizer_times": [
    51955,
    8468,
    3129
  ]
}
```

### Complete example

```dart
import 'package:flutter_driver/flutter_driver.dart';
import 'package:test/test.dart';

void main() {
  group('Scrollable App', () {
    FlutterDriver driver;

    setUpAll(() async {
      driver = await FlutterDriver.connect();
    });

    tearDownAll(() async {
      if (driver != null) {
        driver.close();
      }
    });

    test('verifies the list contains a specific item', () async {
      final listFinder = find.byValueKey('long_list');
      final itemFinder = find.byValueKey('item_50_text');

      // Record a performance profile as we scroll through the list of items
      final timeline = await driver.traceAction(() async {
        await driver.scrollUntilVisible(
          listFinder,
          itemFinder,
          dyScroll: -300.0,
        );

        expect(await driver.getText(itemFinder), 'Item 50');
      });

      // Convert the Timeline into a TimelineSummary that's easier to read and
      // understand.
      final summary = new TimelineSummary.summarize(timeline);

      // Then, save the summary to disk
      summary.writeSummaryToFile('scrolling_summary', pretty: true);

      // Optionally, write the entire timeline to disk in a json format. This
      // file can be opened in the Chrome browser's tracing tools found by
      // navigating to chrome://tracing.
      summary.writeTimelineToFile('scrolling_timeline', pretty: true);
    });
  });
}
```
