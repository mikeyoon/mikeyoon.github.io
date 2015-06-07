---
layout: post
title: Automocking and Concrete Dependencies
date: 2011-09-13 23:08:57.000000000 -08:00
---
Recently I was working on streamlining our unit tests by adding automocking for all the dependencies, rather than mocking each one manually with FakeItEasy. The problem I found is that we don't have interfaces on all our dependencies, namely our service layer is all concrete classes that our web controllers reference directly. This causes a problem with automocking since the automocking (StructureMap automocker in our case) library won't automock concrete classes, probably because it has no way of knowing if a concrete class is a dependency or not. I'll probably end up hacking up the automocker code into pieces to get it going, but I think it probably would have been better to just bite the bullet and put interfaces on all of these classes even if we'd never swap them out. What would be even nicer is if .NET had some kind of syntactic sugar that would allow for the autogeneration of interfaces, kind of like empty properties.
