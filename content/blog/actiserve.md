+++
title = "actiserve"
author = "dominic"
date = "2022-12-22"
categories = [
  "projects"
]
tags = [
  "rust",
  "hachyderm",
]
+++

i went quiet for a bit there, sorry.  http://grafana.hachyderm.io/public might
give you an idea of what happened.  tl;dr: i had a side project as admin of a
[mastodon instance](https://hachyderm.io), just for fun, and then it sort of
exploded.  thanks, elon!

and that's the only thing i'll ever thank him for, the last time i'll use his
name, and now onto the point of this post.

---

as part of the aforementioned mastodon instance, and as i've previously
[mentioned](/blog/rustypub/) [on this blog](/blog/rustypub2/), i've been working
on a rust implementation of the
[activity stream](https://www.w3.org/TR/activitystreams-core/) spec.  well, it's
no longer an abstract pet project.. it has a sibling project that makes use of
it.

one quick detour: one of the things about mastodon and the fediverse that causes
issues for smaller, eg personal, instances is that they don't organically get
content from the rest of the fediverse.  with no-one following accounts from
other instances, nothing can get picked up for the federated feed.  there's a
solution to this in the
[relay](https://joinfediverse.wiki/index.php?title=Fediverse_relays).
essentially, instances that join relays instantly fully federate with each
other.  this is great for smaller instances, and low impact for larger
instances, so it's a win-win.

there's a number of options for hosting a relay, but initial attempts to get
them running alongside our hachyderm infrastructure have been less than
fruitful.  i'm sure it's either something to do with how we're set up, or our
expectations rather than the software itself.

enter [actiserve](https://github.com/hachyserve/actiserve): a relay written in
rust and using [rustypub](https://github.com/hachyserve/rustypub).

it is not yet ready for primetime but the bones are in place.  the relay API
contract is not documented[^0] so we've been copying from other relays and trying
things out so far, so if anyone reading this has experience you're more than
welcome to jump in.

i had intended, for what it's worth, to contribute to existing relay software
but i found the lack of documentation on how things _should_ work to be a
blocker even for that.

stay tuned for more updates.. maybe once it's up and running for hachyderm!

[^0]: or not discoverable
