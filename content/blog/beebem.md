+++
title = "beebem"
author = "dominic"
date = "2022-08-22"
categories = [
  "c++"
]
tags = [
  "c++",
  "retro"
]
+++

My first computer, like many other UK computer nerds, was the BBC microcomputer
B. I've run the beebem emulator on and off, but have been frustrated that a) the
maintained version runs on Windows and b) the less maintained version "for UNIX"
doesn't run on OSX.

I reached out to the last maintainer and asked if he'd mind me putting the
source code up on github (it was hosted as a tgz and some patches on his site)
to hack on it a bit. Thrillingly, he agreed, and so...
https://github.com/beebem-unix/beebem was born.

So far I have:

1. compiled it under modern compilers with all (reasonable) warnings enabled.
2. fixed a few issues raised by the previous (like statements not being covered
   by if and for statements that should have been (!))
3. introduced cmake instead of autotools. Don't get me wrong, i love autotools,
   but we can do better especially if we want to...
4. ... compile and run it on OSX!

version 0.14 has been released with all of the above, and some other smaller
cleanups.  i think next i need to figure out why the keyboard and disk menus are
somehow flipped and the keyboard one was disabled.  then maybe see what we can
bring across from newer windows versions.

Patches welcome, as they say, and ideas also at
[@dma@hachyderm.io](https://hachyderm.io/@dma)
