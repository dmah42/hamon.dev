+++
title = "url shortener in go"
author = "dominic"
date = "2011-09-20"
categories = [
  "nacl"
]
tags = [
  "nacl",
  "retrogaming"
]
+++

# Porting Colossal Cave Adventure to Native Client

[Native Client](http://code.google.com/chrome/nativeclient) (NaCl) is a new
technology built in to Chrome that allows native code (read C++) to be compiled
into a form that is then executed within the browser.  Several impressive ports
have already been completed, including
[ScummVM](https://chrome.google.com/webstore/detail/ddjnneijgokkhfkjlnodlpmjdamlkbmi "ScummVM @ Chrome Web Store"),
[OGRE](http://www.ogre3d.org/forums/viewtopic.php?f=4&t=66394 "OGRE port to Native Client"),
and [Unity
3D](http://blogs.unity3d.com/2010/05/19/google-android-and-the-future-of-games-on-the-web/ "Google, Android, and the Future of Games on the Web").

A host of other open source libraries have also been ported and are available in
the [naclports](http://code.google.com/p/naclports/) repository.

I am really excited about what this could mean for the future of the web as a
platform for technology; even old crusty low-level coders like me can get in on
this internet thing all the young people are talking about. I had the
opportunity recently to take part in a
[hackathon](http://en.wikipedia.org/wiki/Hackathon) centred around Native Client
and took the opportunity to port a game that should need no introduction:
[Colossal Cave Adventure](http://www.rickadams.org/adventure/).

For those who just want to try it out, you’ll need Chrome 14 as a minimum and
you can find it in the Chrome web store right
[here](https://chrome.google.com/webstore/detail/famnhmobejhfdanbcpebcckehbjjgehb "Colossal Cave Adventure").
If you just want to see the source, which is open of course, you’ll find that
[here](http://code.google.com/p/nacl-adventure). Please understand that this was
written over the course of a few hours so it is not necessarily as elegant a
solution as it might be given more time.  Perhaps I will return to it and clean
it up later _[Ed: he did not]_.

There were a number of hurdles I had to overcome to complete this port and it
might be interesting to others so this post will cover the issues and my
approaches to them.

There were three key components to getting the code to work under Native Client:

-   Static data
-   stdin/stdout
-   Synchronous calls

## Static data

### The problem

The original source contains a utility `advent0` that takes the text messages
from a set of .txt files and creates look-up tables that match a message with a
byte offset. At run-time, these look-up tables are used to seek through the .txt
files and read the message out using `fseek` and `fread`. This is not feasible
under Native Client as this interface to file I/O is not (yet?) supported. Other
ports have successfully used forms to allow users to upload files that are
required, but that also is not applicable to this app.

### The solution

A new utility was created (`advtxt_to_c`) that converts the .txt files into
arrays of c-style strings. These are then statically linked into the executable
so that instead of using byte offsets, the run-time can access the message array
directly. This does increase the final executable size, but only by about 64kb.
For reference, the total executable size is on the order of 4Mb, most of which
is library code.

## stdin/stdout

### The problem

The original source obviously relies heavily on `printf`, `fputc`, etc to write
to stdout. Under Native Client these will end up in the console logs rather than
appearing on screen. Using `fgets` and `scanf` to get input from the user are
equally incorrect.

### The solution

The solution to this was to write a thin wrapper around any console output. At
this point, I also split the output into three:

-   screen printing
-   console printing
-   error printing

All of these functions call through to the Native Client module which then calls
through to JavaScript methods.

Screen printing is used whenever the output should go to the, well, screen and
in this case is appended to a textarea on the host HTML page.  Console output is
redirected to the JavaScript console where they can be read using the Developer
Tools built in to Chrome, and error messages are added to a special span on the
host HTML page that shows up red and fades over time. The game is also restarted
whenever an error is produced, which is a far better approach than the original
`exit(-1)`.

Input from stdin was fixed by adding a method to the Native Client module that
would call a callback when an ‘input’ message was passed from the JavaScript
with a string parameter that comes from a text box on the HTML host page. This
required some further changes as an synchronous call becomes an asynchronous
call. See the next section for more on that.

## Synchronous calls

### The problem

As well as console output, the original source depended on `fgets` for reading
user input. This is a synchronous call which has no analogue on the Native
Client side. In fact, the whole game was originally written with an assumption
that the game loop would block waiting for user input which, well, doesn’t work
out all that well.

### The solution

As mentioned above, `fgets` has been replaced by a call to the Native Client
module that registers a callback to be called when the user submits text through
the input control on the HTML page. The places that called `fgets` and assumed a
synchronous return therefore had to be split into two functions. In some cases,
they had to take in function pointers to call after the input had been received.

The main gameloop, that was essentially `while (true) { turn(); }`, has been
replaced by a single call to the `turn` function that contains a single tick of
the gameloop. When input is expected and received, a callback is called that
once again calls the `turn` function.

