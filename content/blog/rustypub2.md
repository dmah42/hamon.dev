+++
title = "rustypub: part 2"
author = "dominic"
date = "2022-09-27"
categories = [
  "projects"
]
tags = [
  "rust",
]
+++

i hit a milestone with https://github.com/hachyserve/rustypub recently with all
of the [core types](https://www.w3.org/TR/activitystreams-vocabulary/#types)
implemented.

in doing so, i learned a couple of Rust tricks that i wanted to share.

## flattening base classes and `Deref`

base classes?  what are you talking about, dma?  rust doesn't have base classes!

unfortunately, someone forgot to tell the [activity
stream](https://www.w3.org/TR/activitystreams-core/) spec writers that when they
defined the spec as it explicitly requires inheritance between types.  for
example, the `IntransitiveActivity` type inherits everything from the `Activity`
type.  we can capture this in rust with:

{{< highlight rust >}}
#[derive(Serialize, Deserialize, Debug)]
pub struct IntransitiveActivity<'a> {
    #[serde(flatten, borrow)]
    base: Activity<'a>,
}
{{< / highlight >}}

note the [serde](https://serde.rs/) `flatten` directive that means when we
serialize an instance of `IntransitiveActivity` it will hide this indirection
and inline `Acitivity`'s members in the resulting JSON.

however, we still have an issue that all users of the type need to be aware of
this `base` field and reference `Acitivity`'s members through that.  this makes
for a clumsy API.  one more trick we can use is to implement
[`Deref`](https://doc.rust-lang.org/std/ops/trait.Deref.html) for the
derived type:

{{< highlight rust >}}
impl<'a> std::ops::Deref for IntransitiveActivity<'a> {
    type Target = Activity<'a>;

    fn deref(&self) -> &Self::Target {
        &self.base
    }
}
{{< / highlight >}}

this essentially treats `IntransitiveActivity` as if it were a pointer type and
allows direct access to the members of `Activity` as if `base` weren't there.

the `Deref` documentation points out that this "should only be implemented for
smart pointers to avoid confusion" as it is also used implicitly by the compiler
in some circumstances, and the rules under which it will do so are specifically
aimed at smart pointers.  however, i haven't found an alternative approach or
noticed any issues, so i'm sticking with it.

## handling `'de` lifetime for `Deserialize`

for convenience i defined a [`Serde`
trait](https://github.com/hachyserve/rustypub/blob/main/src/lib.rs#L7) that
provides default implementations of json serialization and deserialization.
this requires [plumbing through of the `'de`
lifetime](https://serde.rs/lifetimes.html) to ensure that any deserialized
objects respect the incoming stream's lifetime.  where this becomes tricky is
when a struct that derives from `Deserialize` has its own lifetime bound, for
example,
[`Uri<'a>`](https://github.com/hachyserve/rustypub/blob/main/src/core.rs#L235).

the tl;dr magic invocation is:

{{< highlight rust >}}
impl<'de: 'a, 'a> Serde<'de> for Uri<'a> {}
{{< / highlight >}}

which ensures that `'a` is bound by `'de`.

## borrowing for serde

one other consideration is that some types cannot have their lifetimes
implicitly managed by serde[^1].  of course, we can always just copy the data
and avoid the issue, but it is nicer to avoid this where possible[^2].

the magic parameter is `#[serde(borrow)]` applied to any field that needs it.

[^1] serde only supports borrowing of `str` and `[u8]` out of the box.
[^2] this is potentially premature optimization, but also a fun problem to
solve.
