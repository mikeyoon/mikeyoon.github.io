---
layout: post
title: Performance of NHiberate eager loading
date: 2011-09-11 23:10:12.000000000 -08:00
---
Performance of NHiberate eager loading
I was doing some profiling of a complex NHibernate query in our application and found it was doing a lot of N+1 stuff. What I found is that having mappings that would eager load references would cause an extra select for each result I returned. For example, we have a model called Performer, and it has this line in the mappings class (we use Fluent NHibernate):

```csharp
References(x => x.ContentItem).Nullable().Not.LazyLoad().Cascade.None();
```

When I had a query that returned many performers, the profiler would show an extra select statement to get that content item. On the bright side, at least it’s executed all server side. This is the default behavior, but you can change it by setting the fetch type.

```csharp
References(x => x.ContentItem).Nullable().Not.LazyLoad().Cascade.None().Fetch.Join();
```

Or you can lazy load everything by default, and join fetch in your queries when needed. Don’t join fetch against collection mappings though, otherwise you’ll get a cartesian product. I’d leave collection mappings as the default fetch-select for single results, and for multiple results I’d use a preload query that loads all the related items into the session cache so they can be lazyloaded without database calls.
