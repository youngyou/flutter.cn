---
title: Code formatting
title: 代码格式
description: Flutter's code formatter formats your code along recommended style guidelines.
description: Flutter 的代码格式化工具以及推荐的代码格式。
---


While your code may follow any preferred style&mdash;in our
experience&mdash;teams of developers may find it more productive to:

* Have a single, shared style, and
* Enforce this style through automatic formatting.

The alternative is often tiring formatting debates during code reviews, where
time may be better spent on code behavior rather than code style.

## Automatically formatting code in Android Studio and IntelliJ

Install the `Dart` plugin (see [Editor setup](/docs/get-started/editor))
to get automatic formatting of code in Android Studio and IntelliJ.

To automatically format the code in the current source code window, right-click
in the code window and select `Reformat Code with dartfmt`.
You can add a keyboard shortcut to this in Keymap section of IntelliJ
Preferences.

## Automatically formatting code in VS Code

Install the `Flutter` extension (see [Editor setup](/docs/get-started/editor))
to get automatic formatting of code in VS Code.

To automatically format the code in the current source code window, right-click
in the code window and select `Format Document`. You can add a keyboard
shortcut to this VS Code Preferences.

To automatically format code whenever you save a file, set the
`editor.formatOnSave` setting to `true`.

## Automatically formatting code with the 'flutter' command

You can also automatically format code in the command line interface (CLI) using
the `flutter format` command:

```terminal
$ flutter format path1 path2 ...
```

## Using trailing commas

Flutter code often involves building fairly deep tree-shaped data structures,
for example in a `build` method. To get good automatic formatting, we recommend
you adopt the optional *trailing commas*. The guideline for adding a trailing
comma is simple: Always add a trailing comma at the end of a parameter list in
functions, methods, and constructors where you care about keeping the formatting
you crafted. This helps the automatic formatter to insert an appropriate
amount of line breaks for Flutter-style code.

Here is an example of automatically formatted code *with* trailing commas:

![Automatically formatted code with trailing commas]({% asset tools/android-studio/trailing-comma-with.png @path %})

And the same code automatically formatted code *without* trailing commas:

![Automatically formatted code without trailing commas]({% asset tools/android-studio/trailing-comma-without.png @path %})
