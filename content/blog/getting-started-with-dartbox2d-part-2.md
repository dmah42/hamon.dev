+++
title = "getting started with dartbox2d: part 2"
author = "dominic"
date = "2013-03-06"
categories = [
  "dart"
]
tags = [
  "dart",
  "dartbox2d"
]
+++

# Getting started with DartBox2d: part 2

This is an update to [this
post](/blog/getting-started-with-dartbox2d "Getting started with DartBox2d")
as dartbox2d has undergone some drastic changes since that was written
over a year ago.

## Getting the code

The first of the main differences is that dartbox2d is now a package hosted on
pub. Getting the code is a matter of having a `pubspec.yaml` file in the root of
your project that looks something like:

```yaml
    name: dartbox2d-tutorial
    version: 0.0.1
    author: Dominic Hamon <dominic@google.com>
    homepage: http://dmadev.com
    dependencies:
        box2d: ">=0.1.1"
        browser: any"
```

The dependencies section is the important bit. This lets `pub` know that this
application depends on any version of `box2d` with a version number greater than
0.1.1. That version uses the
[vector\_math](http://pub.dartlang.org/packages/vector_math) package instead of
a hand-rolled math library and runs against the latest (as of this writing) dart
sdk r19447. It also depends on `browser` which we’ll need to pull in the script
to allow the code to run both as dart and javascript.

Then, just call `$DART_SDK/bin/pub install` and the `box2d` package will be
installed. If any errors about conflicting dependencies are printed, please
[let me know](http://code.google.com/p/dartbox2d/issues/entry "New issue for DartBox2D").

## The HTML page

```html
    <html>
      <body>
        <script type="application/dart" src="tutorial.dart"></script>
        <script src="packages/browser/dart.js"></script>
      </body>
    </html>
```

## The Dart code

At the top of the dart file, you would now import the libraries you
need:

```dart
    library dartbox2d_tutorial;
    import 'dart:html';
    import 'package:box2d/box2d.dart';
```

Note that the `box2d` package actually contains two libraries: `box2d.dart` for
use with the VM and `box2d_browser.dart` for use in the browser. The only
difference is that the latter enables debug drawing functions using `dart:html`.
If you’re planning on running through DartEditor, you probably want
`box2d_browser`.

The rest of the tutorial works almost as is, though the new math library means
that `initializeWorld` should look like:

```dart
    void initializeWorld() {
        // Create a world with gravity and allow it to sleep.
        world = new World(new vec2(0, -10), true, new DefaultWorldPool());

        // Create the ground.
        PolygonShape sd = new PolygonShape();
        sd.setAsBox(50.0, 0.4);

        BodyDef bd = new BodyDef();
        bd.position.setCoords(0.0, 0.0);
        Body ground = world.createBody(bd);
        ground.createFixtureFromShape(sd);

        // Create a bouncing ball.
        final bouncingBall = new CircleShape();
        bouncingBall.radius = BALL_RADIUS;

        final ballFixtureDef = new FixtureDef();
        ballFixtureDef.restitution = 0.7;
        ballFixtureDef.density = 0.05;
        ballFixtureDef.shape = bouncingBall;

        final ballBodyDef = new BodyDef();
        ballBodyDef.linearVelocity = new vec2(-2, -20);
        ballBodyDef.position = new vec2(15, 15);
        ballBodyDef.type = BodyType.DYNAMIC;
        ballBodyDef.bullet = true;

        final ballBody = world.createBody(ballBodyDef);
        ballBody.createFixture(ballFixtureDef);
    }
```

And the switch to `dart:html` leaves `initializeCanvas` looking like:

```dart
    CanvasElement canvas;
    CanvasRenderingContext2D ctx;
    ViewportTransform viewport;
    DebugDraw debugDraw

    void initializeCanvas() {
        // Create a canvas and get the 2d context.
        canvas = new CanvasElement(width:CANVAS_WIDTH, height:CANVAS_HEIGHT);
        document.body.append(canvas);
        ctx = canvas.getContext("2d");

        // Create the viewport transform with the center at extents.
        final extents = new Vector(CANVAS_WIDTH / 2, CANVAS_HEIGHT / 2);
        viewport = new CanvasViewportTransform(extents, extents);
        viewport.scale = VIEWPORT_SCALE;

        // Create our canvas drawing tool to give to the world.
        debugDraw = new CanvasDraw(viewport, ctx);

        // Have the world draw itself for debugging purposes.
        world.debugDraw = debugDraw;
    }
```

and `run` as:

```dart
    void run() {
        window.animationFrame.then((time) => step());
    }

    void step() {
        world.step(1/60, 10, 10);
        ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
        world.drawDebugData();
        run();
    }
```

Also, though the `frog` and `dartc` compilers have been replaced by `dart2js`
so this is compiled to javascript using:

```
    $ $DART_SDK/bin/dart2js tutorial.dart -otutorial.dart.js
```

There are a host of examples in the `demos` folder of the DartBox2d source
[here](http://code.google.com/p/dartbox2d/source/browse/#git%2Fexample%2Fdemos "DartBox2D demos").

