---
layout: post
title: "An opinionated guide to Javascript state management — Part 1: Introduction"
date: 2020-06-06 10:00:00 -07:00
published: true
---
# Summary

This is the first in a series of posts about my philosophy around state management in Javascript User Interface (UI) applications. I want to note that I’m coming from a background having worked on some fairly large frontends, and may have some bias towards patterns that suit that situation. That doesn’t mean I want to push powerful frameworks for all situations. Far from that, I want this series to inform the reader on whether heavier frameworks are the right choice for their project.

In this introduction, I’ll be going over the definition of state management and how it relates to user interfaces. Then I’ll present a rudimentary example and go over its flaws so we can have a better point of reference when going over some frameworks available in the community. In addition to providing a basic summary, I’ll give some of my recommendations on which types of frameworks to consider.

<!--more-->

# User Interfaces

At its core, a UI works by reacting to a series of actions directly or indirectly initiated by a user, such as opening the application, or clicking a button. The logic encapsulating all of these these interactions and responses in software is state management. As developers, the way we choose to model this state has enormous impacts on its maintainability and robustness.

As UIs have grown significantly in complexity over the years, so did the quantity of state. It’s no wonder that so many frameworks have been, and continue to be, developed to accelerate the task of modeling it. I don’t believe any one of these frameworks is inherently superior to the next, as they all have their tradeoffs. This choice does make approaching the problem of state somewhat daunting for beginners, but I hope I can provide some guidance to help on this journey.

If you’re somewhat new to this space, you may feel somewhat overwhelmed by the sheer number of tools and decisions you face. That’s perfectly normal, and with some experience you’ll start recognizing the patterns and develop your own preferences for how you like to see the state modeled in your code. There isn’t any replacement for experience having to build and iterate on state management, however, this series should provide a good starting point if you’re new and give some ideas on what to explore next if you are not.

# A Very Simple Example

{% gist 55968bdf99f8e970eb245766e1649673 %}

In the most simple and vague terms, state has to exist in some object somewhere with some lifecycle. Above, I’ve included a rudimentary implementation that is likely similar to what many developers have made in the beginning of their career. It consists of objects stored in various places in the application that may or may not have a limited lifecycle. Let’s focus on the `state` and `dragDropState` objects.

The global `state` has an unbounded lifecycle (exists until the application is closed) and is accessible by any slice of your application logic. Scoped state `dragDropState` can be managed in multiple ways, but in my example it’s just another global object that gets initialized and torn down based on a container lifecycle.

Later I’m going to go over some popular frameworks. To help get a sense of why these frameworks were made, let’s go through some pros and cons of the above implementation.

## Pros

* Simple, so it’s really easy for a new developer to quickly understand.
* Requires a very limited amount of code, which should result in a small bundle size.

## Cons

* No assistance in change propagation, i.e. after login, we have to manually call render to make sure the view updates.
* No abstraction or indirection. Every component has to know exactly how to modify the state and propagate any changes. Since components are directly changing the state, if it’s not state local to a component, it becomes difficult to track the source of changes.
* Related to above, but since it’s not DRY, making any refactoring to how state is updated or propagated requires updating every component.
* If more than one component needs to read the same state, you’ll need to figure out how to trigger rendering updates in each of them whenever a change happens.

I could probably add several more points to the cons list, but I think you get the idea. In all but the most simple applications, this implementation is too basic and will not be able to handle a lot of common use cases in most applications. When evaluating frameworks and patterns, capability is one of the criteria to you need to consider. Some other criteria could include:

* Learning curve. If you have a large team, or expecting to grow the team, having a lot of patterns with steep learning curves can hinder your velocity.
* Popularity. In an ideal world, this would be a factor, but it’s important to be practical. If the framework has no spread, and has a steep learning curve, it just might just not be worth the risk.
* Simplicity. How much code does it take to do a task? You’ll want to match this to how complex you expect your application to be. If you’re making a web-version of Adobe Premiere, you’ll want tools to match, even if the overhead is high. Most of us probably aren’t working on a UI that involved, so it’s important to find the right balance between simplicity and capability.

With these criteria in mind, let’s go over some popular frameworks.

# Guide to Some Popular Frameworks

![Frameworks](/assets/img/frameworks.png)

I’ve divided the frameworks into a set of categories I came up with. This to give some idea about the spread of tools out there and where they might fit.

## [Flux](https://facebook.github.io/flux/)-Based

Maximum abstraction/indirection, strict enforcement of unidirectional data flow. Extremely flexible, but requires a large amount of boilerplate to do anything because little to nothing is done automatically. You’d want to use this if you are expecting a very complex data flow with uncertain requirements.

Examples: [Redux](https://redux.js.org/), [Ngrx](https://ngrx.io/), [VueX](https://vuex.vuejs.org/) (borderline)

## [MVVM](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/mvvm) (Model-View-ViewModel)-based

A more object-oriented approach that has data and mutation functions in objects (view models). It propagates changes by attaching watchers whenever a component references a view model. Generally, a lot of MVVM frameworks tend to do runtime patching of the view models to take care of the boilerplate around change detection. If you don’t want to be bothered by the internals of how your framework operates and would just like to get going quickly, this could be a good match.

Examples: [Knockout](https://knockoutjs.com/), [MobX](https://mobx.js.org/)

## [Reactive Programming](https://en.wikipedia.org/wiki/Reactive_programming)

Model interactions as streams of information that can be transformed and combined. Potentially very power and can do a lot with relatively small amounts of code, but has a somewhat steep learning curve. Some state management frameworks have adopted some or all of this pattern.

While it isn’t a state management framework I think it’s important to mention because of how popular it’s become. Many frameworks have adopted this pattern to varying extents and, in my opinion, will be essential knowledge going forward.

Examples: [RxJS](https://rxjs.dev/), [Bacon.js](http://baconjs.github.io/), [Kefir.js](https://kefirjs.github.io/kefir/)

## Stream-based

I’m not sure exactly how to categorize these, but in my opinion these are frameworks that do as much as possible with RxJS or a similar library, and then add additional functionality to optimize certain state-management use cases. If you like reactive programming and don’t mind onboarding new folks into that pattern, I think these kinds of frameworks would be a good match.

Examples: [Akita](https://github.com/datorama/akita), [Cycle.js](https://cycle.js.org/)

# Conclusion

There are many more frameworks and patterns I haven’t listed, all trying to solve the same problem with different tradeoffs. At this time, I’d personally try to steer away from redux-style frameworks, since I’m finding a lot of applications don’t benefit from that level of indirection. So you end up paying the price of so much boilerplate without reaping much reward.

This introduction covered the topic of state management and some patterns/frameworks that are out there. In the [next]({% post_url 2020-06-14-An-opinionated-guide-part-2 %}) article, I’ll go over the two types of the state, essential and derived, and some approaches to managing them.
