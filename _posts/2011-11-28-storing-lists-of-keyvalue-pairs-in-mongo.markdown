---
layout: post
title: Storing lists of key/value pairs in mongo
date: 2011-11-29 23:05:15.000000000 -08:00
---
Don't keep them as arrays if you can help it. Imagine if you have a list of key/value pairs, perhaps to store extra attributes about an entity. You might decide on something like this:

```javascript
{
    id: 1,
    attributes: [
        { name: 'key', value: 'value' },
        { name: 'key2', value: 'value2' }
    ]
}
```

This will be fine if you never ever plan on querying against the data inside of the attributes field. If you do need to query it, maybe in map/reduce, you'll end up spending a lot of CPU time iterating through the collection. You might want to consider saving yourself the trouble and storing it as an embedded object.

```javascript
{
    id: 1,
    attributes: {
      key: 'value',
      key2: 'value2'
    }
}
```

Then when you iterate over the collection you can do something like:

```javascript
if (this.attributes.key) doSomething();
```
