---
layout: post
title: First crack at supporting Xamarin with MVVM
date: 2013-12-30 17:45:02.000000000 -08:00
---
Recently I've added in the foundations for supporting an Android version of an MVVM/WPF application using Xamarin. The process wasn't too bad and it only took a few days. Most of the time was spent replacing incompatible libraries with versions that were compatible.

Fortunately, a lot of open source authors are providing PCL versions of their libraries or supporting Mono outright. This means I can just simply reference those versions in the specific platform projects. So I was able to continue using [Autofac](https://code.google.com/p/autofac/), [protobuf-net](https://code.google.com/p/protobuf-net/), and many others. 

I did have to switch out [Membus](https://github.com/flq/MemBus) with [TinyMessenger](https://github.com/grumpydev/TinyMessenger), because Membus used the dynamic features of .NET. That was a time consuming process as those libraries work a little differently. I might make a fork of Membus to not use those features if I have time. I also had to abstract out the logging service since the WPF app uses Log4NET, which isn't mobile-compatible. I also had to abstract out any file access and implement them in platform-specific services.

The biggest part that I have yet to address is how to handle MVVM. I would prefer not to convert everything to a framework like MVVMCross. Currently we use Caliburn.Micro, which was relatively easy to use. I made a copy of our viewmodel csproj file and used the appropriate ifdefs to keep Caliburn out of the Android version. In the meantime, I simply reimplemented all the necessary Caliburn base classes and interfaces in the Android version of the project so at least the Activities can listen for property changes. I don't think it'll be too hard to make something reasonably useful for doing view-binding later.

If you're planning on supporting iOS, you're probably in for some extra work, as that platform is more restrictive. This is because Xamarin has to cross compile into the native ARM code for iOS, so some features of .NET are unavailable.

Overall, I'm pretty impressed by how well Xamarin works as of right now. There are some annoying bugs in the VS addin, mainly editing the Android project settings often results in an error loop that you can't escape without killing the application. You can kind of work around it by opening up the project in Xamarin Studio, but ideally I'd never have to use it. As for the runtime, I hope it performs well enough, but it looks like it should be reasonably capable.
