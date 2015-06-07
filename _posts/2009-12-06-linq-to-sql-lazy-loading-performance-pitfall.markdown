---
layout: post
title: LINQ to SQL Lazy Loading Performance Pitfall
date: 2009-12-06 23:14:38.000000000 -08:00
---
With the advent of Linq-to-SQL some time ago, I was quite happy with having some sort of standard ORM for .NET, albeit very basic. After using it for some time, and seeing a lot of other people use it, I noticed one big performance gotcha that can get you if you don't take the time to understand what is going on under the hood, and that is traversing related tables after a query. Assume tables Foo1 and Foo2, both sharing a key FooID. LINQ makes it really easy to fetch related data, either as from the Foo1 entity, like Foo1.Foo2, or from the opposite, Foo2.Foo1. The problem is in a query like this:

    using (db = conn)
    {
        List results = new List();
        var a = db.Foo1.Where(p => p.Field = something);
        foreach (var temp in a)
        {
            var result = new { ID = temp.FooID, Field1 = temp.Foo2.Field1 };
            results.add(result);
        }
        return results;
    }

People new to LINQ or who aren't thinking everything through might think that will just be 1 SQL statement, but infact it will be N + 1 statements, where N is the number of rows in Foo1. If a reference is made to a related entity, and that entity has not been filled in, then LINQ will automatically make a SQL call for it.  Basically it's a lazyload, which I'm sure users of hibernate and the like will understand. Often this problem goes unnoticed until it ends up in a QA environment, since you'd never notice any performance issues if the SQL server and the .NET application are running on the same box.

To get around it, you could write set ObjectTrackingEnabled to false and write something to get the related data in a fixed number of queries and then fill in the related tables yourself. Something like:

    using (db = conn)
    {
        db.ObjectTrackingEnabled = false;
        List results = new List();
        var a = db.Foo1.Where(p => p.Field = something).ToList();
        var related = db.Foo2.Where(p => a.Select(q => q.FooID).Contains(p.FooID)).ToDictionary(p => p.FooID); //Probably won't work like this exactly
        foreach (var temp in a)
        {
            var result = new { ID = temp.FooID, related[temp.FooID].Field1 };
            results.add(result);
        }
        return results;
    }
  
Not ideal for sure, but probably better than making way too many calls to the DB server. There are other better ways of handling this case, but I just want get people to think about what LINQ-to-SQL is doing underneath.
