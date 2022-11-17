+++
title = "gomud"
author = "dominic"
date = "2013-11-01"
categories = [
  "go"
]
tags = [
  "go",
  "mud"
]
+++


I needed an excuse to learn [go](http://golang.org/), and none of the usual
candidate projects that I usually use to pick up a new language seemed to fit
well. I was thinking back to how I started programming, and the transition from
hobby programming to professional was largely thanks to working as a creator on
[Discworld MUD](http://discworld.starturtle.net/lpc/). It struck me that a
[MUD](https://en.wikipedia.org/wiki/MUD) could be the perfect platform, given
the support for concurrency and networking. And then I realised it would be even
more interesting to write a MUD engine that others could use to develop MUDs. So
I did: [gomud](http://github.com/dmah42/gomud).

Right now you can define rooms that have exits, players are persistent (though
there are no passwords yet) and supported commands include ‘say’, ‘tell’, ‘me’,
‘shout’, ‘who’, ‘look’, and ‘go’.

Future plans include defining commands through data for easy expansion, add
passwords for players, items, creatures, and then things like combat, skills,
etc.

It turns out that it really is the perfect vehicle for learning go. The network
support and concurrency in the language lend themselves really well to the core
loop for a MUD, and the message passing works wonders for sending text to
different connected players. Built in testing means that unit test coverage is
strong, and it’s really easy to hack something up that works before iterating it
into something more modular and well-structured.

If you can’t tell, I’m somewhat enamoured with both the project, and the
language. I’m open to pull requests, so if you also want to get started with the
language, or if you want to use it to build a MUD, please get in touch and help
me build something neat.

