---
layout: post
title: My Experience with Go
date: 2015-06-07 01:01:01.000000000 -08:00
---
Over the past few months, I've been working on a hobby project, which I've uncreatively
called [MyDailyStuff](https://www.mydailystuff.com). It's written with Go on the backend
and Typescript on the frontend (which I'll talk about in another post). Code for the site
is available at my github profile.

I'll start with the summary of how I feel about Go. Basically, I'm glad it's gaining in
popularity because I think it has a good chance of taking a lot of marketshare from
NodeJS, which shouldn't be used nearly as much as it is. This isn't to say that Go is
a perfect language, far from that. There have been numerous posts about the pros and cons
of Go on Hackernews and the like, so I'll try to keep this focused on my experience
building my application.

MyDailyStuff is a simple monolithic architecture. There's a REST API in Go and a TypeScript
frontend single-page application. The datastore is ElasticSearch. Coming from a C# and JS
background, the complete lack of OO design was jarring, but not too limiting for my usecase.
Martini is a very capable microframework, very comparable to express. Based on what I've
read online though, martini is full of "magic" and isn't very idiomatic, and is also on
the slower side. For my usecase here I didn't notice any problems.

ElasticSearch wasn't too hard to integrate with, but I did have to heavily modify
the elastigo source to do the queries I needed. I think with Go only having a year or
so of major growth, a lot of the libraries are pretty nascent. Unfortunatley, there are
probably a lot of missing/inadequate libraries for various tools with large APIs like ElasticSearch.
My modifications still probably only covered a small fraction of the API, and I'll have to
modify it some more to do highlighting.

Testing was relatively simple with Ginkgo, although I'm not a huge fan of mocha-style
frameworks. I do like BDD-style testing, which is why I'm using Ginkgo, but I wish they
would be more like mspec and not require you do the entire AAA in the "It" function. 
I tried out mocking with [Testify](https://github.com/stretchr/testify/tree/master/mock), 
which requires you code to an interface. It is a bit awkward and tedious that you have to 
create an entire mock struct, but overall not too bad.

Overall, I'd recommend Go for most applications where NodeJS/Express is being considered,
unless you need to do view isomorphism. Due to its simlicity, I can't recommend it for 
more advanced applications like data analysis of unstructured data. I'll probably end up
using Go again in the future for other similar projects.

<!---
Like most langauges/frameworks/etc, Go has focused on a few key aspects, sometimes at the
expense of others. The build and deployment story is very good. You can compile your entire
app to a single executable quickly can be a huge timesaver. Think about how much time you
have to spend doing boring devops work keeping your dependencies and such up to date. With
Go, all you need do is this:

* Spin up a container with your preferred distribution
* Deploy the executable as a service
* Run it

This also makes Go very suitable for local apps and utilities, since you don't have to make
sure the user is running NodeJS >= v0.12.0 and < v0.12.2 or whatever.

I do wish Go as a language was more flexible. The lack of Generics and a richer type
system can be a real drag, as some operations become really tedious. 
-->