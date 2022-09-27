+++
title = "rustypub"
author = "dominic"
date = "2022-09-15"
categories = [
  "projects"
]
tags = [
  "rust",
]
+++

a wild new project appears!

this time it's an excuse to learn a bit more [rust](https://www.rust-lang.org) 
than i have on previous toy projects.  over on the
[hachyderm](https://hachyderm.io) discord, we came up with the bright idea of
implementing the activity pub API in rust.

at this point, there's a basic implementation of a few core structs from the
spec (https://www.w3.org/TR/activitystreams-core/) over at
https://github.com/hachyserve/rustypub and the ultimate goal is to complete the
API, add an event handler or API server, and stick it under our mastodon
instance.

it's been quite the learning curve.  though i've managed to code up most of a
[programming language](https://github.com/dominichamon/mrdo) in rust before
(compiler, VM, REPL, ASM), this project is requiring getting to grips with
borrowing and lifetimes like never before.

follow along, contribute if you want, and watch me tear my hear out at the
borrow checker's demands.