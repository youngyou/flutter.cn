---
title: Introduction to animations
title: 动画效果介绍
short-title: Animations
short-title: 动画
description: How to perform animations in Flutter
description: 如何使用 Flutter 实现动画效果
---

Well-designed animations makes a UI feel more intuitive, contribute to the slick
look and feel of a polished app, and improve the user experience. Flutter's
animation support makes it easy to implement a variety of animation types. Many
widgets, especially [Material widgets][], come with the standard motion effects
defined in their design spec, but it's also possible to customize these effects.

The following resources are a good place to start learning the Flutter animation
framework. Each of these documents shows, step by step, how to write animation
code.

{% comment %}
More documentation is in the works on how to implement common design
patterns, such as shared element transitions,
and physics-based animations.
If you have a specific request, please
[file an issue]({{site.github}}/flutter/website/issues).
{% endcomment -%}

* [Animations tutorial](/docs/development/ui/animations/tutorial)<br>
  Explains the fundamental classes in the Flutter animation package
  (controllers, Animatable, curves, listeners, builders),
  as it guides you through a progression of tween animations using
  different aspects of the animation APIs.

* [Zero to One with Flutter, part
  1]({{site.medium}}/dartlang/zero-to-one-with-flutter-43b13fd7b354) and [part
  2]({{site.medium}}/dartlang/zero-to-one-with-flutter-part-two-5aa2f06655cb)<br>
  Medium articles showing how to create an animated chart using tweening.

* [Building Beautiful UIs with
  Flutter]({{site.codelabs}}/codelabs/flutter)<br>
  Codelab demonstrating how to build a simple chat app. [Step 7 (Animate
  your app)]({{site.codelabs}}/codelabs/flutter/#6)
  shows how to animate the new message&mdash;sliding it from the input area up
  to the message list.

We also have some videos that discuss aspects of Flutter animation.

<iframe width="560" height="315" src="https://www.youtube.com/embed/yI-8QHpGIP4?rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
AnimatedContainer

<iframe width="560" height="315" src="https://www.youtube.com/embed/9hltevOHQBw?rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
Opacity, including the implicit AnimatedOpacity widget

<iframe width="560" height="315" src="https://www.youtube.com/embed/pK738Pg9cxc?rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
FadeInImage

<iframe width="560" height="315" src="https://www.youtube.com/embed/Be9UH1kXFDw?rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
Hero

<iframe width="560" height="315" src="https://www.youtube.com/embed/9z_YNlRlWfA?rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
Transform

<iframe width="560" height="315" src="https://www.youtube.com/embed/N-RiyZlv8v8?rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
AnimatedBuilder

## Animation types

Animations fall into one of two categories: tween- or physics-based.
The following sections explain what these terms mean, and points you to
resources where you can learn more. In some cases,
the best documentation we currently have is example code in the
Flutter gallery.

### Tween animation

Short for _in-betweening_. In a tween animation, the beginning
and ending points are defined, as well as a timeline, and a curve
that defines the timing and speed of the transition.
The framework calculates how to transition from the beginning point
to the end point.

The documents listed above, such as the [animations
tutorial](/docs/development/ui/animations/tutorial) are not about tweening,
specifically, but they use tweens in their examples.

### Physics-based animation

In physics-based animation, motion is modeled to resemble real-world
behavior. When you toss a ball, for example, where and when it lands
depends on how fast it was tossed and how far it was from the ground.
Similarly, dropping a ball attached to a spring falls
(and bounces) differently than dropping a ball attached to a string.

* [Flutter Gallery]({{site.github}}/flutter/flutter/tree/master/examples/flutter_gallery)<br>
Under **Material Components**, the
[Grid]({{site.github}}/flutter/flutter/blob/master/examples/flutter_gallery/lib/demo/material/grid_list_demo.dart) example
demonstrates a fling animation. Select one of the images from
the grid and zoom in. You can pan the image with flinging or dragging
gestures.

* Also see the API documentation for
  [AnimationController<wbr>.animateWith][AnimationController.animateWith] and
  [SpringSimulation][].

## Common animation patterns

Most UX or motion designers find that certain animation patterns are
used repeatedly when designing a UI. This section lists some of the commonly
used animation patterns, and tells you where you can learn more.

### Animated list or grid
This pattern involves animating the addition or removal of elements from a
list or grid.

* [AnimatedList example](/docs/catalog/samples/animated-list)<br>
This demo, from the [Sample App Catalog](/docs/catalog/samples), shows how to
animate adding an element to a list, or removing a selected element.
The internal Dart list is synced as the user modifies the list using
the plus (+) and minus (-) buttons.

### Shared element transition

In this pattern, the user selects an element&mdash;often an
image&mdash;from the page, and the UI animates the selected element
to a new page with more detail. In Flutter, you can easily implement
shared element transitions between routes (pages) using the Hero widget.

* [Hero Animations](/docs/development/ui/animations/hero-animations)
How to create two styles of Hero animations:
  * The hero flies from one page to another while changing position
    and size.
  * The hero's boundary changes shape, from a circle to a square,
    as its flies from one page to another.

* [Flutter Gallery][]<br>
You can build the Gallery app yourself, or download it from the Play Store.
The
[Shrine]({{site.github}}/flutter/flutter/blob/master/examples/flutter_gallery/lib/demo/shrine_demo.dart)
demo includes an example of a Hero animation.

* Also see the API documentation for the
[Hero,]({{site.api}}/flutter/widgets/Hero-class.html)
[Navigator,]({{site.api}}/flutter/widgets/Navigator-class.html) and
[PageRoute]({{site.api}}/flutter/widgets/PageRoute-class.html)
classes.

### Staggered animation

Animations that are broken into smaller motions, where some of the motion is delayed.
The smaller animations may be sequential, or may partially or completely overlap.

* [Staggered Animations](/docs/development/ui/animations/staggered-animations)

{% comment %}
  Save so I can remember how to add it back later.
  <img src="/images/ic_new_releases_black_24px.svg" alt="this doc is new!"> NEW<br>
{% endcomment -%}

## Other resources

Learn more about Flutter animations at the following links:

* [Animations: Technical Overview](/docs/development/ui/animations/overview.html)<br>
A look at some of the major classes in the animations library,
and Flutter's animation architecture.

* [Animation and Motion Widgets](/docs/development/ui/widgets/animation)<br>
A catalog of some of the animation widgets provided in the Flutter APIs.

{% comment %}
Until the landing page for the animation library is reworked, leave this
link out.
* The [animation
library]({{site.api}}/flutter/animation/animation-library.html)
in the [Flutter API documentation]({{site.api}})<br>
The animation API for the Flutter framework.
{% endcomment %}

<hr>

If there is specific animation documentation you'd like to see,
[file an issue]({{site.github}}/flutter/website/issues).

[AnimationController.animateWith]: {{site.api}}/flutter/animation/AnimationController/animateWith.html
[Flutter Gallery]: {{site.repo.flutter}}/tree/master/examples/flutter_gallery
[Material widgets]: /docs/development/ui/widgets/material
[SpringSimulation]: {{site.api}}/flutter/physics/SpringSimulation-class.html
