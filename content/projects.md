+++
title = "projects"
slug = "projects"
date = "2022-05-24"
+++

this is a list of projects, some more active and others more experimental.

## active projects

### [google benchmark](https://github.com/google/benchmark)

a library to run microbenchmarks on C++ code, similar to unit tests.

_#benchmark #c++_

### [swarm](https://github.com/dmah42/swarm)

swarm is a project that offers a dead simple way to run jobs on a cluster.  run
a worker on every member of the cluster, and swarm will discover those workers
using UDP multicast (for now).  once found, you can run jobs and it will pick
the best worker on which to run the job.

_#go #grpc_

### [tcp\_estats-ebpf](https://github.com/dmah42/tcp\_estats-ebp)

an attempt to bring RFC4898 to the linux kernel using eBPF, rather than direct
integration.

_#go #c #ebpf_

## inactive projects

### [mrdo](https://github.com/dmah42/mrdo)

a toy language, the main idea of which was that operations on collections are
run in parallel by the vm without developer hints.

originally written in C++, but rewritten from scratch with a VM, REPL, etc in
Rust.

_#c++ #rust_

### [dmanix](https://github.com/dmah42/dmanix)

an i386 OS written in C++.  i didn't study comp sci so i never wrote an OS.  so
i wrote an OS.

_#c++_

### [gomud](https://github.com/dmah42/gomud)

i cut my teeth as a programmer writing code for a MUD so when it came to
learning Go, i built a MUD engine.

more context in [this blog post](/blog/gomud).

_#go_

## experimental stuff

### [z80cpp](https://github.com/dmah42/z80cpp)

a very silly project that enables writing of optimal z80 assembly from C++.  no,
it's not a backend for LLVM (that would be the obvious way of doing it).
instead, we compile to 32-bit x86 assembly and then transpile it to z80.

the transpiler is written in Go.

_#go #asm #z80_

