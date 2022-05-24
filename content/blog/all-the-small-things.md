+++
title = "all the small things"
author = "dominic"
date = "2014-01-10"
categories = [
  "projects"
]
tags = [
  "C",
  "C++",
  "go"
]
+++

# All the small things

Someone, on checking out my
[github](http://github.com/dominichamon "github") page, recently
commented that I’m more of a breadth than a depth developer. I’m not
sure that this is entirely accurate, though I didn’t take offence, but
it is true that many of my personal projects are small one-off toys.
This post is a tour of some of them.

## dmanix
https://github.com/dominichamon/dmanix

I didn’t study Computer Science at university[^1] and I sometimes feel like I
missed out on some key programming projects like writing your own operating
systems, compilers, and path tracers. I’ve been steadily remedying this and
*dmanix* is my attempt at an operating system.

It only runs on 386 and has a scheduler, ramdisk, memory allocation,
etc. The next step will be writing an ELF parser and compiling programs
for it.

The only thing that separates it from other \*nix OSes is that it’s
written in more C++ than C.

## LRU cache
https://github.com/dominichamon/lru-cache

One of the interview questions I ask on occasion[^2] is to implement an LRU
cache in C or C++. The idea behind an LRU cache is that you have a fixed
capacity cache in which you want to store data such that only the most recently
used things are in it. In this case, I’m storing key/value pairs.

The specific reason why this is an interesting implementation is that it
uses only operations that are average-case O(1). Ie, it should scale
without significant performance reduction. This is managed by using both
a
[list](http://en.cppreference.com/w/cpp/container/list "list @ cppreference")
and an
[unordered\_map](http://en.cppreference.com/w/cpp/container/unordered_map "unordered_map @ cppreference");
the unordered\_map to store the key-value pairs for fast lookup, and the
list to track how recently items are accessed.

## buckets
https://github.com/dominichamon/buckets

There’s a well-known class of puzzles in which one is asked to fill a
container using two other odd-sized containers. For example, fill a 5l
container using a 3l and 4l container. I don’t enjoy these puzzles so I
decided to write code to solve them for me.

It uses a [depth-first graph
search](http://en.wikipedia.org/wiki/Depth-first_search "depth-first search @ wikipedia")
algorithm  to explore the solution space and then prints out the steps
needed to solve the problem. There’s one issue which I should fix at
some point: It might be better to use a [breadth-first
search](http://en.wikipedia.org/wiki/Breadth-first_search "breadth-first search @ wikipedia") as
the solution space is relatively small and this would return the
shortest solution.

## queueserve
https://github.com/dominichamon/queueserve

One of the things I’ve been doing recently is learning
[Go](http://golang.org/)[^3].  I felt that I needed to get to grips with the
scalability and use for a web service, so I built a highly concurrent queue
server and client.

It uses JSON over HTTP as a wire protocol and supports many, many
concurrent clients thanks to Go’s built-in support for concurrent HTTP
serving. The server is using lock-free queues to maximize concurrency.

## evernote
https://github.com/dominichamon/evernote

[Evernote](http://evernote.com/ "evernote") have a series of [coding
challenges](https://evernote.com/careers/challenge.php "coding challenge @ evernote")
on their careers website. I enjoy a challenge so I tackled the problems.
No-one ever contacted me so I don’t know if I did well, but my code
passes all of the test cases and is relatively fast. All the other
solutions posted around the web are in Python (that I found, anyway) so
I thought having C++ solutions would be a neat change.

[^1]: Theoretical physics, if you were wondering
[^2]: and presumably can’t ask any more after posting this
[^3]: the language, not the game

