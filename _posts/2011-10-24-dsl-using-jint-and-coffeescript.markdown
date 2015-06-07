---
layout: post
title: DSL using Jint and Coffeescript
date: 2011-10-24 23:08:02.000000000 -08:00
---
Currently I'm involved in a project to create a DSL (domain specific language) for one of our internal processes that could be updated in realtime. Our original idea was to use Boo for that, as it is a well-supported .NET language, but because it has to be compiled into an assembly that made updating code in realtime difficult. This is because creating assemblies will leak memory unless you create them in a separate appdomain, but hosting them in another appdomain can lead to a whole other set of complications regarding integration.

Fortunately, I came across a pretty useful tool called [Jint](http://jint.codeplex.com/ "Jint"), which is a javascript interpreter for .NET. What's great about it is because it's an interpreter it doesn't have the memory-leak issue because there are no assemblies generated. This combined with Coffeescript allowed us to create a DSL that was manageable for our business users. Overall, it was surprisingly simple to get going once I got past a small learning curve that I'll explain.

The biggest downside for us is that since we're using javascript we don't get the benefits of a compiler, such as validating variable references and such, so all our scripts need to be tested extensively. It is much easier to integrate, however, as it easily allows you to expose functions and variables without any fuss. Unfortunately, there are many rough edges in the library including, but not limited to all the following:

- functions with params at the end are tough to integrate. You have to create a clr Array in the javascript code, which can be cumbersome.
- Referencing enums using the full classname is unreliable, I instead just expose each enum value as a parameter to the engine using a loop.
- You can't create an Array easily, because Jint doesn't interpret the square brackets in the "new" call.
- For some reason, calling .Run(script) vs CallFunction(script) results in different error handling. When using .Run, you can get a somewhat useful stack trace, while CallFunction results in nothing at all when your code fails. Haven't had a chance to see why, but you can easily call a function using .Run by just putting a function call as your entire script.

It's also not particularly well-documented, but the authors do seem to respond to comments in a timely manner. The performance is surprisingly good for an interpreter, in my opinion, so I'll be using it for sure. If you have a DSL to implement that doesn't require updating the code without restarting then I'd probably stick to something like Boo, since compiled .NET code is much nicer to work with.
