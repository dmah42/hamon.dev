+++
title = "tcp estats ebpf"
author = "dominic"
date = "2022-05-30"
categories = [
  "go",
  "C"
]
tags = [
  "go",
  "C",
  "ebpf",
  "tcp"
]
+++

## Background 

About 10 years ago, when I was working on M-Lab[^1], one of my projects was to,
along with [Matt Mathis](https://research.google/people/MattMathis/), bring
[RFC4898](https://www.ietf.org/rfc/rfc4898) both up to date as
[web10g](https://github.com/rapier1/web10g), but also try to integrate it into
the Linux kernel.

Ultimately, upstreaming didn't happen[^2] even though we ironed out a lot of the
overhead, ensuring that it could be compiled out cleanly and had 0 runtime
overhead if the syscall wasn't used to enable it.  This was largely due to a
shift to the `TCP_INFO` socket option for all TCP stats, rather than the
netlink-based estats.

This has itched at the back of my brain since, as the RFC4898 spec is far richer
than something that could be captured in `tcp_info`, and arguably [more
useful](https://www.measurementlab.net/tests/ndt/web100/).

## enter eBPF

Thanks to [kris nóva](https://www.twitch.tv/krisnova), i heard about
[eBPF](https://ebpf.io), and specifically how it allowed a sandboxed userspace
program to attach to kernel functions.  It was already being used to do some
network traffic analysis, and even rerouting, but it struck me that maybe it
could also be used to collect rich statistics on TCP connections.

So I built a thing[^3]: https://github.com/dominichamon/tcp_estats-ebpf.

## tcp\_estats-ebpf

It's taken a few attempts to get both the approach right, and BPF itself is
quite finicky.  The C code is only validated at runtime, the error messages are
arcane, and knowing when to copy things from kernel to userspace is something
that isn't well documented.  However, it can be picked up (even if i still don't
feel like I know _exactly_ what i'm doing).

In its current form, the project is hooked into a few kernel functions, allowing
~half of the RFC4898 metrics to be collected.  It dumps the results to stdout as
JSON, and the latest set of commits have refined the project structure.  It's
just beyond "proof of concept" phase.

## next steps

* Add more hooks
* Validate the metrics
* Add a bunch of tests!

If any of you reading this are interested in helping out, the
[repo](https://github.com/dominichamon/tcp_estats-ebpf) is open for
business!


[^1]: see [here](/blog/visualizing-m-lab-data-with-bigquery) and
[here](/blog/visualizing-m-lab-data-with-bigquery-part-two)
[^2]: https://lists.psc.edu/pipermail/web10g-user/2013-January/000026.html
[^3]: which as of this post is now the title of this blog.
