+++
title = "critters"
author = "dominic"
date = "2025-07-31"
categories = [
  "python"
]
tags = [
  "python",
  "ai"
]
+++

If you've been on the internet for at least as long as I have, you may have a
fond but distant memory of a sort of game or sim in which you could create
creatures, herbivores or carnivores, and check in on their progress in an
online simulation.

Being the mid-90s (I think) the interface was simple, but it was a very
impressive bit of work for the time, and incredibly compelling.  As far as I
can tell, because information is sketchy, it was called "TUG" or "The Useless
Game", and was written by [Andrew
Gower](https://runescape.fandom.com/wiki/Andrew_Gower) of Runescape fame.

It seems to have disappeared from the internet entirely, so I thought it would
be fun to hack together something similar, with all due deference to the
original. I'm calling it Critters and the source is
[here](https://github.com/dmah42/critters).

So far, there's a web interface where you can watch the critters going about
their business, and check in on individuals.  Eventually i'll let folks create
or adopt them and get updates on them.

They have fairly sophisticated behaviour patterns already, and I've been
squashing as many bugs as I can.  The AI is something I'm relatively proud of.

It separates goal setting from actions, so a critter can decide based on a
weighted score which goal is most important to it in that moment (reducing
hunger, quenching thirst, recovering energy, etc) and then determine what the
best action to take is to achieve that goal (resting, hunting, etc).  This has
reduced some of the issues with flip-flopping decision making I had early on.

The latest bug is a fun one: I spotted two herbivores, both hungry, both at the
edge of a body of water, and both within a few tiles of some food (grass). 
However, neither were moving.  It turned out that although they could sense the
grass, and they knew they should move towards it, the non-flocking movement
behaviour was pointing them through the water as a straight line.  They can't
move through water, so they were stuck.

My first thought on how to fix it was to do something like track inactivity over
time and give them a sort of "kick" in a random direction, but then I realised
there was no way of avoiding it any further: I'd have to implement pathfinding.

As an ex-game developer, A* is what i'll be going with, but as the critters have
energy, and it costs more to go uphill than downhill already, i can optimise for
minimum energy spend as opposed to shortest distance, which i'll be doing.  Once
I have that, i'll plug it in to all the movement behaviours (except fleeing) and
I'm hoping to see some fairly rich behaviours coming out of it.

Once it's a bit further along I'll host it somewhere and update here.

Patches welcome, as they say, and ideas also at
[@dma@hachyderm.io](https://hachyderm.io/@dma)
