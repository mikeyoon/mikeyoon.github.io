---
layout: post
title: Common problems when scaling a website
date: 2014-01-15 01:01:01.000000000 -08:00
---
It's a problem every web startup wants to have: too many users using their web application, bringing the server to its knees. Here are some common causes from my experience, each of which I go into more detail below.

* Too many requests made per page
* One or more database calls made for every request
* Too many calls made to your cache stores per request
* Push Notifications
* Template rendering

<!--more-->

###Too Many Requests

The first problem should be pretty straightforward. If you're making more than a few requests to serve your highly-used pages, then you probably have a lot of room for improvement. Static assets should be combined (I like require.js for JS), and AJAX requests should be consolidated if possible. It is quite common for more complex websites to have expensive onrequest filters and handlers, so it's important to keep requests down to a minimum. Just because you can load everything asynchronously and provide a responsive UI doesn't mean it takes no server resources. In fact, it probably takes more.

Some sites will even send non-CDN hosted static assets through their controller pipeline, potentially causing extra service calls to be made. If your site is requiring authentication checks (for example) for static assets, think very carefully about those costs and make sure it's worth it. These requests can pile up amazingly fast. There's a reason why node.js sample layouts often have a public folder for storing static assets.

###Database calls every request

If you have the number of requests under control, then one of the most common problems is making one or more database calls on every request. This is understandable when you are rushing to to get the site live, but it will be a problem down the road. Any site that does this will have a difficult time scaling without spending large amounts of money on hardware. All necessary data should be cached when a user logs in or at some other appropriate time. Storing that data in a cache server like Memcached or Redis will make it a lot less expensive to scale your site. 

###Abusing the cache servers

As I just mentioned, it's definitely much better to hit cache servers more than database servers, but that doesn't mean using the cache servers is free. I've seen memcached servers be abused because on each request the app wanted to lookup how many credits the user had, their session data, recent searches, etc. One easy option is to store all this data inside of one key to limit it to one call to the cache server. If you must make a lot of calls, then you can also keep that data in the local memory cache as well. If it needs to be updated in realtime (like when the user just got a new friend and the count increased), then you could integrate a bus to push those notifications to the web servers so they can clear their local cache.

###Handling push notifications

Depending on the site, there may or may not be the need to push data to the user periodically. An extreme example would be something like Facebook, which has to push a new post to the client when a friend posts something. The easiest way to handle this is by doing AJAX longpolling, as that fits into existing architectures with almost no effort. The problem is that long polls can take a lot of server resources (each timeout/response means a new request from the client). Websockets can be much more efficient because multiple responses does not require any overhead. The cost is extra development time to handle the socket states, but soemtimes it's worth it.

###Rendering HTML is expensive

Template rendering performance isn't something that most developers will take into consideration, but if you have _very_ popular website, then it's possible to make it a bottleneck. It can be surprisingly expensive to render those fancy HTML templates, and as a result you should make use of caching. Ideally, your web framework would help with this, but that isn't always the case. More advanced frameworks (i.e. ASP.NET) can be configured to cache the static portions of a view, leaving only the variable data to render each request. If your system doesn't provide this feature, you can look into a reverse proxy system like Varnish to cache the content going out to the client. I've seen some sites leave out the variable data to make it easy to cache and have AJAX fill in the rest later. The problem is that it probably takes more total resources, albeit you can use different servers for serving the AJAX request.

That's all the low-hanging fruit I can think of right now. There are an endless list of reasons a site could be slow (like using Rails ;)), so you should definitely invest in profiling tools to help out. I really like services such as New Relic, since it's incredibly valuable to see metrics on live traffic. This is especially true for startups, in my opinion, as you're probably least likely to have spent the time building accurate load tests.
