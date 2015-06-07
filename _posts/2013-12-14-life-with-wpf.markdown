---
layout: post
title: Life with WPF
date: 2013-12-14 21:58:15.000000000 -08:00
---
Coming from a web background (ASP.NET), tackling WPF was a lot more of a learning curve than I expected. I am glad to have this experience, and look forward to doing some Android development with Xamarin. Here are some notable things I've learned over this past year of writing a WPF app.

1. Lots of state. Web usually is sort of the opposite, although heavy JS apps are getting close. It's interesting have to manage so many bits of information.

1. XAML Styles don't inherit like in CSS, so you end up having to specify a lot of styles (which are like CSS classes) for each type of element. Also, elements can't reference multiple styles, which means even more style definitions. Maybe it'd be possible to make a domain specific language like SASS or LESS that would compile to XAML. Probably not enough of a market to warrant the effort though.

1. Web browsers are amazingly optimized for a lot of operations, and it is disappointing having to write a lot of code to do it in a native application (i.e. writing DirectX, etc.). They are also quite stable, all things considered (except for Flash). It can be really nice having that sandbox.

1. Graphics drivers are garbage (I'm looking at you nVidia "Optimus"). I thought browser-specific quirks were annoying, but having random crashes and occasional memory leaks due to bad drivers is way worse.

1. Databinding is really nice (although I don't like dependency properties all that much). I see why a lot of JS frameworks have adopted similar patterns. Unfortunately, I think MS dropped the ball by not providing any decent MVVM framework. There are a lot of choose from out there, and it's kind of daunting for new developers since the choice isn't usually that easy.

WPF is not bad for a native application framework, but what annoys me a lot is how MS can't seem to stick to a single one for very long. First there was WinForms (which was pretty awful, I admit), then WPF/Silverlight, and now there's Windows RT/Store. OS X has been around longer than .NET, and they're still using Cocoa.

I haven't looked much at the Windows Store API, but the XAML version doesn't seem to be different enough to warrant practically abandoning WPF (and Windows 7). I can definitely see the value in toolkits like QT, as they tend to be more predictable. If you are ok with the compromises of a cross-platform toolkit, I'd probably recommend that for a desktop application.
