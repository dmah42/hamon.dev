+++
title = "embedding the dart vm: part one"
author = "dominic"
date = "2012-09-17"
categories = [
  "dart"
]
tags = [
  "dart",
  "vm"
]
+++

The [Dart](http://dartlang.org/) VM can be run as a standalone tool from
command-line, and is embedded in a branch of Chromium named Dartium, but all of
the public instructions I could find only discuss how to extend the VM with
native methods. Definitely useful, but there are some applications when it would
be useful to directly embed the VM in a native executable. For instance, if a
game wanted to use Dart as an alternative to [Lua](http://www.lua.org/).

The instructions for adding [native extensions](http://www.dartlang.org/articles/native-extensions-for-standalone-dart-vm/)
recommend building the extension as a shared library that the VM can load. We
need to flip that model around and build the VM as a static library that our
native executable can link against. This, part one of a series of
who-knows-how-many, will focus on getting the source and building a native
executable with the VM embedded. We won’t expect to actually run a script just
yet, but by the end of this blog we will be loading and compiling a script.

## Getting and Building the VM

The instructions for getting all of the Dart source (including the editor, VM,
and runtime libraries) are
[here](https://code.google.com/p/dart/wiki/GettingTheSource).

Once we have the source, we need to build the VM as a static library instead of
the default. [gyp](http://code.google.com/p/gyp/) is used to generate the
Makefile, and it already includes a static library target for the VM. Sadly make
doesn’t seem to be fully supported on OSX, given the pile of compiler errors
that I hit, but opening the xcode project and building the dart-runtime project
directly does work. I needed to tweak a few of the settings in the xcodeproject
as it was set up to use GCC 4.2, which I don’t have, and I wanted the Debug
build to be unoptimized for easy debugging, but eventually I had a few shiny
static libs to play with.

Quite a bit is hidden in that ‘eventually’. There are a number of subtleties
around linking in generated source files to get a snapshot buffer loaded that
contains much of the core libraries. There’s also the issue of initializing the
built-in libraries; the VM source contains some files that handle this, but it’s
unclear which should be included and which shouldn’t. Similarly, some libraries
are named with a \_withcore suffix, and it’s not clear whether they are the ones
that should be linked in or not.

## Loading a script

To load a script we need an `Isolate` and some core libraries loaded to do
things like resolve the path to the script, read the source, and compile it.
Eventually, when we come to run, we’ll have to register some native methods, but
for now just loading the core, io and uri libraries is a good start.

It took a while, but the end result is both exciting, and suggests the next step
that’s required:

```
    Dart Initialized
    LoadScript: helloworld.dart
    CreateIsolate: helloworld.dart, main, 1
    Created isolate
    Loaded builtin libraries
    About to load helloworld.dart
    LoadScript: helloworld.dart, 1
    Script loaded into 0x100702590
    Invoking 'main'
    Invoke: main
    Failed to invoke main: Unhandled exception:
    UnsupportedOperationException: 'print' is not supported
    #0 _unsupportedPrint._unsupportedPrint (dart:core-patch:2525:3)
    #1 print (dart:core-patch:2521:16)
    #2 main (file:////~/git/embed-dart-vm/helloworld.dart:4:8)Done
```

There was one rather nice step where I was getting an error compiling the test
Dart script. After spending a couple of hours debugging the code it turned out
that I actually had a syntax error in the Dart script. I should have trusted my
code after all!

Next time we’ll figure out those native extensions, built-in libraries, and get
the script running.

Full source for this project can be found
[here](https://github.com/google/embed-dart-vm "embed-dart-vm project on github")
and the version that this post describes is
[here](https://github.com/google/embed-dart-vm/tree/ad0adb00af510482982123bafc61018f4805b75c "embed-dart-vm project on github: compiling but not running").

