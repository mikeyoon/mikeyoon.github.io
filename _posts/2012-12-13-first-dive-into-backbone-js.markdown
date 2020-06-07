---
layout: post
title: First dive into Backbone.js
date: 2012-12-13 23:02:30.000000000 -08:00
---
At my company I took it upon myself to start out doing some prototype projects, and the first was a new type of location-based browsing application. I decided to try doing it using [Backbone.js](http://documentcloud.github.com/backbone/ "Backbone.JS"), since it is very javascript heavy. Now I have very little frontend experience, so it wasn't the smoothest experience, but I found it a lot more reasonable reasonable than I expected. Some things I noticed as I worked on it.

<!--more-->

1. [RequireJS](http://requirejs.org/ "RequireJS") (or something like it) is awesome and I'd say totally necessary, in my opinion, for having manageable frontend projects. At my company we store all the JS in large monolithic files, and working with it is an enormous pain.
2. It requires a lot of planning to get everything right without doing lots of refactors. Probably common knowledge among experienced frontend developers, but I found myself putting in container divs and such all the time.
3. There are some choices on how to communicate between views, and I don't know what works best, but I chose to pass around models with bound events. It works, but as the project grew larger I felt less comfortable.
4. It felt easy as the project grew to make hacks that would make maintenance painful. I should probably read up on [javascript patters](http://shichuan.github.com/javascript-patterns/ "").

One thing I would like to incorporate in the future are [Backbone.Relational](https://github.com/PaulUithol/Backbone-relational "Backbone.Relational"), since some of my models have a one-to-many relationship, and I found out halfway that relational would handle that more cleanly.

I still find CSS annoying (yay for twitter bootstrap at least), and the most annoying thing when doing my project was that there's no simple way of vertical aligning a div. Also IE is a pain and I'm glad this a prototype so I just didn't bother really caring. Maybe my views on CSS will change one day, but more likely is that CSS will get enough frameworks around it that it won't be so bad to work with.
