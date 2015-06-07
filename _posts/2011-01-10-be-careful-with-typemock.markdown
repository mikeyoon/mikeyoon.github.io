---
layout: post
title: Be Careful with Typemock
date: 2011-01-10 23:12:18.000000000 -08:00
---
We use Typemock at our organization for doing mocks in our tests. The reason is simple, like many other projects, there are deficiencies in the design (i.e. static classes) that traditional mock frameworks like Moq can't handle. For those of you fortunate enough to be starting a new codebase, think hard about whether to use Typemock. Here are some pitfalls to Typemock:

1. It is slow. Just running tmockrunner alone on a test project will slow it down by upwards of 40%, even if you aren't mocking anything. The runner is necessary because it is intercepting all of your method calls (and a bunch of other things I'm sure) and doing some voodoo to make this all work.
2. It can have really weird issues. I'm just speculating on why, but when you are mocking static classes, the cleanup and remocking of these classes can be inconsistent and you might get weird errors that you just can't explain easily, or at all. Regardless, you will at some point end up pulling out your hair wondering why a test will fail when running in a batch, and succeed when running by itself.

Its greatest benefit is can also be its greatest weakness. You can mock just about anything you can possibly think of. Static classes? No problem. Swap out the next instantiation of a given type, also no problem. This allows you to carry on with possibly poor design decisions without any warning.

All in all, there's obviously a huge market for Typemock, given that many projects aren't all designed with full blown IoC and such, but still need to be tested effectively. Even those that are will still find certain edge cases where it would just be better to mock the static class, but those certainly should be the exception and not the rule. Consider placing all those tests in a separate project so tmockrunner is only needed there.
