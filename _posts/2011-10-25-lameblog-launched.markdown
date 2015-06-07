---
layout: post
title: LameBlog Launched
date: 2011-10-25 23:07:25.000000000 -08:00
---
I've decided that I'll start blogging into LameBlog now (link to code at the bottom). Hopefully I don't end up regretting it :). It was a fun NodeJS project, and I'll probably continue making small refinements and additions. Here's what I've learned so far:

1. Javascript is a powerful language for sure, but if it wasn't used all over the web I probably wouldn't bother with it in the backend. It has too many design decisions that make it tougher than it needs to be when working on large projects (i.e. no static typing, prototypical inheritance, no proper namespaces/packaging).
2. As an addendum to #1, because of those design choices, you get no code intellisense, which makes it a bitch to work with when starting out.
3. Node is a really slim framework, and while it's great to work in a platform that doesn't have so many abstractions, it's nice to have something that has all those robust features that you come to appreciate, like security, validation, etc. This will probably change once node matures.
4. Because node is relatively new, there is a lot of development into what will be the most popular design patterns. If you don't already have a good grasp of web frameworks today, you could easily end up making really terrible code.
5. Unit testing is currently quite tedious to do, but at least there are some good frameworks in progress, such as Vows and Jasmine.

On the flip side, some of the things I loved about node:

1. It's really slim in terms of memory usage, and surprisingly fast for javascript.
2. Since it's javascript, it can leverage a whole bunch of existing code.
3. The community around it is developing a lot of cool design patterns.
4. It interfaces really well with other javascript interfaces, such as Mongo/Couch, REST JSON APIs, etc.

I'm going to definitely be keeping node in my toolbelt, especially since it can bridge the gap in making a universal web language (although we'll see what Google's Dart does in the next few years), and it has its uses for sure.
