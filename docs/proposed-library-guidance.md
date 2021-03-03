# How to maximize adoption of your library

***Guidance for .NET library authors***

As library developers, we often need to depend on other people's work to
implement our own libraries. However, when building high-quality libraries,
dependencies are rarely an implementation detail. At the minimum it's code that
runs in the user's application, but due to the way .NET works, the APIs of the
dependencies often also become part of the public API of our own library.
Historically, most of us have been very shy to depend on other people's
libraries, for various reasons. Sometimes we get away with a statically linking
another library (for example, by coping the code and making all types internal)
and sometimes we even go through the trouble of implementing the functionality
ourselves, just to avoid taking a dependency. However, for an ecosystem to be
healthy it's vital that it avoids duplicating community efforts and instead
people standardize and join forces so that we can all benefit from each other's
work.

The goal of this document is to capture what characteristics a library should
have so that it's easier for a consumer to depend on it. It's not meant to be
exhaustive, nor does it mean if you don't follow all of them you're not gaining
any adoption. For example, if you're building a closed source library, that's
still fine, you should just be aware that open-source projects might not be able
to depend on you.

As such, it's not intended to be a prescriptive guide for every library but
rather a list of areas to consider in order to maximize the number of consumers.

## Your library is open source under a compatible license

Since many of the libraries on nuget.org are open source, most consumers
generally don't want to depend on closed source libraries. This also means your
license must be compatible with the consumers. For the most part, this means the
library should be under a liberal license such as MIT, Apache, or BSD (as
opposed to a copy-left license, such as GPL or LGPGL).

## Your open-source project is healthy

Ideally your library should be on GitHub because that's where majority of
open-source momentum is these days. More importantly, it needs to be a true
open-source project that accepts contributions and has a public home where
contributors can discuss, review code, and file suggestions. The key is that
your project is healthy, which includes having recent commits, having multiple
contributors, and generally a productive and civil engagement between you and
your community.

## Your library is compatible with .NET Framework & strong named

While .NET Framework is considered feature complete, it still has a very large &
active customer base, which means many libraries still want to be consumable by
.NET Framework users. For the most part, this means your library should support
.NET Standard 2.0 (or offer a .NET Framework specific binary). This also means
your library should be strong-named, otherwise consumers who are already
strong-named can't take a dependency on your library. In order to allow your
consumers to rebuild & potentially patch your library, they need to have access
to your strong name. It's easiest if you commit the public & private key
directly to source control (because [it's no longer used for security][sn] and a
[recommended practice for OSS libraries][sn-commit]).

## You are avoiding API breaking changes

Successful libraries are usually long lived and are often at the bottom of the
dependency graph in an application. For such libraries, API breaking changes are
generally unworkable. [Semantic Versioning (SemVer)][sem-ver] doesn't solve this
issue either. The problem is that most applications have diamond dependencies
(i.e., multiple parts in the system depend on the same library in different
versions). The only sane model to resolve these conflicts is by unifying to the
higher version. SemVer would effectively mean that you can't unify across major
version boundaries. Too often this results in graphs where there is no
combination of library versions that can compose without conflicts.

This means that (outside of previews) your library should have a track record of
generally not performing API breaking changes. Well-motivated and rare
exceptions are likely unavoidable, but the important part is the track record.

This is vitally important for [binary breaking changes but also includes source
breaking changes][breaking-changes].

## You generally maintain behavior compatibility

We know that it's impractical to test everything all the time. Worse, in some
cases behavior breaks are only observed when data is being persisted or
exchanged between different version of a consumer's application. Thus, you
generally need to deliver a consistent behavior over time and avoid surprising
consumers when behaviors change. That is not to say that behavior can never
change, but the expectation is that a library announces such changes well in
advance. And if the change is impactful, also allow an application to get back
the old behavior, at least for some time so that applications that use both, new
and old versions of the component, can agree on a behavior.

The .NET team has documented examples of [behavioral breaking
changes][behavioral-breaks] and also [the process they use to inform their
users][breaking-change-process] when breaking changes are deemed unavoidable.

## Your target frameworks are compatible

Unless your library is inherently platform specific, most consumers will expect
it to be portable, meaning they can consume it from .NET Standard. However, in
some cases you may need to multi-target, meaning, you need to provide different
binaries for different frameworks and/or operating systems. That's totally fine,
so long your library still offers a portable target so that your consumer isn't
forced to multi-target as well.

When your library offers multiple frameworks, it's important that it is
compatible with itself across frameworks that can reference each other. For
example, when your library offers a binary for both .NET Standard 2.0 and .NET
Framework 4.8, code compiled against the .NET Standard binary must be able to
run against the .NET Framework binary. Among other rules, this also means the
binaries can't be named differently across frameworks.

## Your APIs should adhere to common .NET design guidelines

There is a book called [Framework Design Guidelines][fxdg] that was written by
members on the .NET team and captures most of the .NET design guidelines. Of
course, design is often subjective so adherence to these guidelines isn't a
simple yes or no answer. The key is that your library shouldn't be a thorn in
the eyes of consumers who are used to idiomatic .NET libraries. For example, it
should follow standard naming conventions such as Pascal-cased methods and using
an Async suffix. This is especially important to consumers who need to use your
types in their own public APIs. Thus, consumers will generally prefer libraries
that preserve the idiomatic experience of .NET over libraries that don't.

## You have documentation

Some libraries do not offer any form of documentation while some provide some
samples in their GitHub repo or code completion documentation (via the
triple-slash XML comments). In order to make it easy for consumers to figure out
how to get started, you should provide samples in your repo or even consider
having a web site with a getting started tutorial. For consumers that want to
use your types in their public APIs it is often important that you also document
your APIs. Otherwise, your lack of documentation becomes their lack of
documentation, which deter some consumers.

## You have automated tests

Ideally, you have high coverage of your code, but the most important thing is to
have tests at all. Many projects still do not have any form of testing which
most consumers will find concerning. Not having any tests makes it hard to
believe you'll be able to provide sufficient behavior compatibility over time,
which in turn also limits your ability to accept contributions.

At the very minimum, consumers would expect to see some end-to-end testing for
the key scenarios of your library.

[sn]: https://docs.microsoft.com/en-us/dotnet/standard/assembly/strong-named#what-makes-a-strong-named-assembly
[sn-commit]: https://docs.microsoft.com/en-us/dotnet/standard/library-guidance/strong-naming
[sem-ver]: https://semver.org/
[breaking-changes]: https://github.com/dotnet/runtime/blob/master/docs/coding-guidelines/breaking-change-rules.md#source-and-binary-compatibility-changes
[behavioral-breaks]: https://github.com/dotnet/runtime/blob/master/docs/coding-guidelines/breaking-change-rules.md#behavioral-changes
[breaking-change-process]: https://github.com/dotnet/runtime/blob/master/docs/project/breaking-change-process.md
[fxdg]: https://www.amazon.com/dp/0135896460
