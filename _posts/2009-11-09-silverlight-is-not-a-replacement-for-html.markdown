---
layout: post
title: Silverlight is not a replacement for HTML
date: 2009-11-09 23:15:28.000000000 -08:00
---
I've mentioned about Sharepoint being used for odd purposes, such as a pure data store instead of as a full CMS + delivery. Now I've seen another case, where Silverlight was used instead of a standard ASP.Net website. Certainly, there are sites that are 100% Flash and are very effective, but I don't think any of those sites resemble standard web pages. The best tool to build a website is using HTML or equivalent markup, so why do it in Flash/Silverlight? Taking something like Silverlight and constraining it to the principles of standard web design will end up being the worst of both worlds. Now the UI behaves differently than the user expects, but looks like a webpage so the user is thrown off. You get to deal with everything being an asynchronous call, which can be powerful, but not in this case. All that while reaping none of the benefits. Then there are potential cross site issues, restrictions on what libraries Silverlight can reference, and plenty of other problems. All this leads to is something that does not behave as well as a regular webpage and is not as easy to maintain.

There are things Silverlight and Flash are good at. Shoehorning them into other problem domains (which generally seems all too common in consulting) just feels like a huge waste of time. The problem of building a webpage is pretty well handled at this point, why not try looking into creating sites that use some innovative layouts and interaction so all that time fiddling with XAML doesn't go to waste?

I think people tend to get too attached to the tools and lose focus on solving the problem.
