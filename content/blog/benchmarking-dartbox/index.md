+++
title = "benchmarking dartbox2d"
author = "dominic"
date = "2012-01-27"
categories = [
  "dart"
]
tags = [
  "dart",
  "dartbox2d",
  "benchmark"
]
+++

# Benchmarking DartBox2d

Joel Webber wrote [this excellent blog
post](http://blog.j15r.com/2011/12/for-those-unfamiliar-with-it-box2d-is.html "Box2D as a measure of runtime performance")
in which he tests native versions of [Box2D](http://box2d.org/ "Box2D") against
Javascript implementations. Perhaps unsurprisingly, he discovered that native
code is around 20 times faster than JavaScript.

Having just released
[DartBox2d](http://code.google.com/p/dartbox2d "dartbox2d"), I was curious to
see how [Dart](http://dartlang.org/ "The Dart language") stacks up against these
results. It should be noted that the Dart version has diverged a little from the
original port to make it more Dart-like. My measurements didn’t show any
significant performance change between the current version and the initial port.

I’m using the same test as Joel, taken from his [github
repo](https://github.com/joelgwebber/bench2d "Bench2d @ github"), and have
committed the Dart source used back into the tree, so you can check it out
[here](https://github.com/joelgwebber/bench2d/blob/master/dart/Bench2d.dart "Bench2d dart @ github").
The JavaScript there, and used below, was generated using frog rather than dartc
as it generates smaller, more readable output. The Dart VM does not currently
support any references to dart:dom or dart:html so running those required some
massaging of the code. Specifically, commenting out all of
dartbox2d/callbacks/CanvasDraw.dart and removing all references to Canvas from
Bench2d.dart.

All of the data and the graphs can be seen
[here](https://docs.google.com/spreadsheet/ccc?key=0Ag8kcyhkEonSdEE3WnMwMV82N2E2SDg0UDE3R0ZKMmc "Bench2d Dart results").

## JavaScript

First, JavaScript generated from Dart using frog vs hand-written
[Box2D-web](http://code.google.com/p/box2dweb/ "Box2D-web") JavaScript:

![](./chart\_11.png)

This is on a linear scale, unlike Joel’s graphs, as the difference between the
traces is much smaller. However, the raw frame times are higher, which is
probably due to the different machines we’re running on. The results, though,
are still clear: Box2D-web runs at an average 104 ms/frame while the JS
generated by frog from Dart is running at 135 ms/frame. There’s significant
variation in both implementations (standard deviation is ~18 – 19 ms in both
cases) which is either inherent in the simulation or indicates garbage
collection running.

## Native

Given the difference Joel saw between the Java VM and Javascript, with the Java
VM running 10 times faster than JavaScript, it is tempting to compare Dart
compiled to JavaScript with Dart running natively in a VM in
[Dartium](http://code.google.com/p/dart/wiki/BuildingDartium "Dartium").

![](./chart\_21.png)

There’s a massive 4800 ms frame that I had to cut off to see detail across all
the samples. I think this is some part of the VM being initialized and blocking
the process, but it’s hard to tell.

There’s some other really interesting things to note here. Firstly, the VM
performance improves over time, which is not something that I’ve seen in other
tests. It’s also faster than the generated JavaScript and the hand-written
Box2D-Web JavaScript *at it’s fastest*, however there is massive variance due
to a periodic slowdown. It’s running at an average 119 ms per frame but the
standard deviation is a massive 300 ms. I haven’t looked into the Dart VM but
I’m going to throw out a guess that this is some garbage collection kicking in
every few frames.

## Summary

Here are all three results together for comparison:

![](./chart\_3.png)

With a little optimization work in DartBox2d, and maybe a little work on the
code generation in Dart, I think it’s possible to get Dart-generated-JavaScript
to get close to the performance of hand-written JavaScript. However, it’s also
clear that the Dart VM, even in its current state, has the potential to
outperform both.
