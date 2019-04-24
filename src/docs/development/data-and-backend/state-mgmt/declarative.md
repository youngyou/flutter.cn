---
title: Start thinking declaratively
title: 状态管理中的声明式编程思维
prev:
  title: Intro
  title: 状态 (State) 管理介绍
  path: /docs/development/data-and-backend/state-mgmt
next:
  title: Ephemeral versus app state
  title: 短时 (ephemeral) 和共享 (app) 状态
  path: /docs/development/data-and-backend/state-mgmt/ephemeral-vs-app
---

If you're coming to Flutter from an imperative framework (such as Android SDK or 
iOS UIKit), you need to start thinking about app development from a new 
perspective. 

Many assumptions that you might have don't apply to Flutter. For example, in 
Flutter it's okay to rebuild parts of your UI from scratch instead of modifying 
it. Flutter is fast enough to do that, even on every frame if needed.

Flutter is _declarative_. This means that Flutter builds its user interface to 
reflect the current state of your app:

{% asset development/data-and-backend/state-mgmt/ui-equals-function-of-state alt="A mathematical formula of UI = f(state). 'UI' is the layout on the screen. 'f' is your build methods. 'state' is the application state." %}

{% comment %}
Source drawing for the png above: : https://docs.google.com/drawings/d/1RDcR5LyFtzhpmiT5-UupXBeos2Ban5cUTU0-JujS3Os/edit?usp=sharing
{% endcomment %}

When the state of your app changes (for example, the user flips a switch in the 
settings screen), you change the state, and that triggers a redraw of the user 
interface. There is no imperative changing of the UI itself (like 
`widget.setText`) — you change the state, and the UI rebuilds from scratch.

Read more about the declarative approach to UI programming [in 
the get started guide](/docs/get-started/flutter-for/declarative). 

The declarative style of UI programming has many benefits. Remarkably, there is 
only one code path for any state of the UI. You describe what the UI should look 
like for any given state, once — and that is it.

At first, this style of programming may not seem as intuitive as the imperative 
style. This is why this section is here. Read on.
