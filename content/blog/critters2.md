+++
title = "critters2"
author = "dominic"
date = "2025-08-01"
categories = [
  "python"
]
tags = [
  "python",
  "ai"
]
+++

Adding pathfinding (see [critters](/blog/critters)) made all the difference,
and my Critters have settled into an equilibrium with ~2x the carnivores vs
the herbivores.  Apparently this is a a classic "Lotka-Volterra" predator-prey
cycle: The carnivores are so efficient that they've suppressed the herbivore
population.

Let me just take a moment to revel in a hobby project AI revealing some
similarities with actual science.

...

Anyway, this equilibrium lasted about 50 ticks before the carnivores started
dying out.  They couldn't find enough herbivores to eat and/or the hunting of
them was just too expensive.  Add to that that breeding is still extremely rare
(I'm not putting massively dense populations in place yet until the sim is a bit
more mature) and, well, population collapse.  The cows are doing fine.

One thing that I built into the sim early on that has bugged me is that hunger
increases every tick (as does thirst) and that hunger and energy aren't linked.
In other words, a carnivore can hunt down a prey burning its energy, eat, and
then be so tired it has to rest, during which time hunger ticks up.  Herbivores
on the other hand can just graze.

To resolve this, i'm going to add a new genetic trait and plumb it through:
Metabolism.  This will affect both the amount of hunger that is experienced when
burning energy, and the amount of energy realised by eating.

By linking them, and by allowing resting to be "hunger-free", I hope this will
balance the advantages the herbivores have towards the carnivores just a little.

Ideas welcome, at [@dma@hachyderm.io](https://hachyderm.io/@dma)
