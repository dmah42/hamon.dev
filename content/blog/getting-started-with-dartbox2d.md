+++
title = "getting started with dartbox2d"
author = "dominic"
date = "2012-01-11"
categories = [
  "dart"
]
tags = [
  "dart",
  "dartbox2d"
]
+++

# Getting started with DartBox2d

_This post is deprecated. Please see [this
post](/blog/getting-started-with-dartbox2d-part-2 "Getting started with DartBox2d part 2")
for the latest version._

The latest [Dart](http://dartlang.org/ "Dart") library to be released is one
that might see a fair bit of use, if the Java and JavaScript versions are
anything to go by.

[DartBox2d](http://code.google.com/p/dartbox2d "DartBox2d @ googlecode") is
the latest port of the immensely popular 2d physics engine seen in games across
the web. It has a very similar interface to the [Java version](http://jbox2d.org/ "JBox2d"),
so getting started shouldn’t be too hard for anyone familiar with that version.

That being said, here’s how to put together a simple application.

## Getting the library

Go
[here](http://code.google.com/p/dartbox2d/source/checkout "DartBox2d: Source checkout")
and follow the instructions to get a local copy of the code.

## The HTML page

Next, you need an HTML page to host the application. A simple
boilerplate would look something like:

```html
    <html>
      <body>
        <script type="text/javascript" src="tutorial.dart.js"></script>
      </body>
    </html>
```

## The Dart code

At the top of the dart file, you need to name your application and
import any libraries you want to use:

```dart
    #library('tutorial');
    #import('dart:dom');
    #import('[path_to_dartbox2d]/lib/box2d.dart');
```

Now create a class for your application and a simple main method:

```dart
    class Tutorial {
      ...
    }

    void main() {
      Tutorial.main();
    }
```

This simple main method is what will be called when your application starts. It
is calling a static method on your class that should now look something like:

```dart
    class Tutorial {
      static void main() {
        final app = new Tutorial();
        app.run();
      }

      Tutorial() {
        initializeWorld();
        initializeCanvas();
      }
    }
```

All we have left to do is to define the two initialization methods and
the run method. First, let’s initialize the world:

```dart
    class Tutorial {
      ...

      static final int BALL_RADIUS = 1;

      World world;

      void initializeWorld() {
        // Create a world with gravity and allow it to sleep.
        world = new World(new Vector(0, -10), true, new DefaultWorldPool());

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
        ballBodyDef.linearVelocity = new Vector(-2, -20);
        ballBodyDef.position = new Vector(15, 15);
        ballBodyDef.type = BodyType.DYNAMIC;
        ballBodyDef.bullet = true;

        final ballBody = world.createBody(ballBodyDef);
        ballBody.createFixture(ballFixtureDef);
      }
    }
```

And now we’re ready to initialize the canvas:

```dart
    class Tutorial {
      ...
      static final int CANVAS_WIDTH = 900;
      static final int CANVAS_HEIGHT = 600;
      static final int VIEWPORT_SCALE = 10;

      HTMLCanvasElement canvas;
      CanvasRenderingContext2D ctx;
      IViewportTransform viewport;
      DebugDraw debugDraw;

      void initializeCanvas() {
        // Create a canvas and get the 2d context.
        canvas = document.createElement('canvas');
        canvas.width = CANVAS_WIDTH;
        canvas.height = CANVAS_HEIGHT;
        document.body.appendChild(canvas);
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
    }
```

Now, all that’s left is to start the world running:

```dart
    class Tutorial {
      ...
      static final num TIME_STEP = 1/60;
      static final int VELOCITY_ITERATIONS = 10;
      static final int POSITION_ITERATIONS = 10;

      void run() {
        window.webkitRequestAnimationFrame((num time) {
          step(time);
        }, canvas);
      }

      void step(num time) {
        // Advance the world.
        world.step(TIME_STEP, VELOCITY_ITERATIONS, POSITION_ITERATIONS);

        // Clear the canvas and draw the frame.
        ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
        world.drawDebugData();

        // Request another animation frame.
        window.webkitRequestAnimationFrame((num t) {
          step(t);
        }, canvas);
      }
    }
```

Once you’ve put all this together, you’re ready to compile it to JavaScript.

I prefer the command-line frog compiler, but DartBox2d also compiles cleanly
with dartc with warnings as errors and fatal type errors enabled, so feel free
to use either. You can also use the Dart Editor to build your application, and
that’s almost certainly the best route to take. Refer to
[the Dart language site](http://dartlang.org/) for more details.

Once you’ve compiled to JavaScript, make sure that your html file is referencing
the generated file correctly and load it up in a browser. If all went well, you
should see a green box with a peach ball bouncing on it.

