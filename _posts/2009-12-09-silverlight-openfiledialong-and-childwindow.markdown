---
layout: post
title: Silverlight OpenFileDialong and ChildWindow
date: 2009-12-09 23:14:08.000000000 -08:00
---
In Silverlight 3, MS added requirements that dialogs be called only in response to a user initiated action. Any attempts to call OpenFileDialog.ShowDialog(), for example, in a Load event would trigger this error:

"Dialogs must be user-initiated"

Simple enough to deal with, and most likely your code only needs to call dialogs in response to click events, but I noticed there is a problem with ChildWindows and dialogs.

Let's say I have a Page that contains a ChildWindow called fileWindow, and in the fileWindow is an OpenFileDialog and a browse button. Clicking the browse button calls ShowDialog and does what you'd expect. The first time this is done, it all works fine. The problem happens when the fileWindow is closed, and then opened again from the Page. On the second ShowDialog, I will get the above error after I select a file or cancel. It's a bit odd since you'd expect that error to popup before the ShowDialog executes to prevent it from happening, but in this case it will occur after. No idea what is going on underneath, but it is a bit annoying to workaround. Don't know if it's a bug in Silverlight or in my code as of yet.
