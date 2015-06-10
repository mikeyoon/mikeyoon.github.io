---
layout: post
title: Serializing MongoDB in Node
date: 2011-12-14 23:04:31.000000000 -08:00
---
Perhaps because I'm relatively new to the Javascript world, dealing with serialization has been tough for me. I needed to serialize objects from Mongoose into Redis for caching, and it was proving difficult for a couple reasons.

1. Dates aren't serialized properly to JSON because the specification doesn't account for dates.
2. Mongoose documents are usually wrapped around a Model, and you don't want to serialize the whole model.

So this code would be a problem

~~~javascript
JSON.parse(JSON.stringify(model)).datetime.getMonth();
~~~

because datetime would come back as a string. Mongoose models already handle JSON serialization and only serialize the document, so at least that portion is seamless. Unfortunately, deserializing back requires a bit of legwork. 

Mongoose Model inherits from Document, which is the raw data, and you can reconstruct the model given the document using the model constructor. It isn't exactly what I hoped for, but it at least gets the job done. I'm doing this instead now

~~~  
return new Post(JSON.parse(json)); //Post is a mongoose Model
~~~

which will rehydrate the model from the json as I would expect, dates and all. I don't know what kind of performance implications this will have when load tested, but I don't have high hopes for it. 

Apparently the inheritance setup is very important for handling serialization, something I've taken for granted in OO languages, which are usually very good at serializing only what you need back and forth efficiently.
