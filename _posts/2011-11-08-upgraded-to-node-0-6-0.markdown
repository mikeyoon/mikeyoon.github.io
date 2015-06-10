---
layout: post
title: Upgraded to Node 0.6.0
date: 2011-11-08 23:06:54.000000000 -08:00
---
Lameblog is now compatible with Node 0.6.0. Mostly a smooth experience, except the time module didn't work in 0.6.0, so I had to switch to use zoneinfo. Unfortunately, the error messaging didn't help much, it just says:

~~~
FATAL ERROR: v8::HandleScope::Close() Local scope has already been closed
~~~

No stack trace, nothing. Maybe there is some debug mode or some such that would help out, but I haven't explored that. Had to just comment out stuff to narrow down what was causing the failure. I can imagine in bigger projects that this would suck pretty hard.
