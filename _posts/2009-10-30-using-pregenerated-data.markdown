---
layout: post
title: Using pregenerated data
date: 2009-10-30 23:16:11.000000000 -08:00
---
Something I came across while doing some consulting work. It was a simple problem, calculating how many weeks an employee worked (a week only needs to have one day of work to count). The catch was that a work week starts on Thursday, and there is no guarantee an employee works every week, so there can be gaps. So with that restriction, most developers like myself might be inclined to write a custom program to iteration through the days worked and start tabulating it, perhaps incrementing the count on each Thursday or Friday. There are a few edge cases that can really be annoying to handle with a linear loop, like if an employee works on Wednesday, takes a break and resumes 13 days from then, a Tuesday, how would you count that last Wednesday as a work week?  There are a couple others, but suffice to say what would normally be thought of as a really quick and simple program just become a little more complicated than it should be.

A nice little trick is to pregenerate a table with every day in the given time period, let's say four years or so. Assign each day a week, with each Friday would increment the week by 1. Then there will be a table like this:

1/1/1,1
1/2/1,1
1/3/1,2
1/4/1,2

and so forth. Then you can join this table against all the days worked for an employee, and you can easily see what weeks each day worked belongs to. Then group that up and it's easy to count the number of weeks. And since it's simple you can be relatively sure it's accurate. So anytime you find yourself iterating through some data and doing some weird calculation to do a count, think if there's a way to approach it with pregenerated data. One other more famous example I know of is for password cracking of the NTLM hashes of old. Someone had pregenerated all combinations of hashes for passwords containing only numbers and letters of a maximum length, making it really quick to figure out what a password is given a hash. And there you have the reason for password complexity rules.
