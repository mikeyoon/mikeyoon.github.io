---
layout: post
title: First Foray with ElasticSearch
date: 2012-12-12 23:00:11.000000000 -08:00
---
Recently at work I've been tasked with migrating from our hosted Endeca solution to ElasticSearch. We chose ElasticSearch because it's free, is easy to setup, has easy replication, has facet support out of the box, and has good .NET libraries available. The migration is now done for the most part and I'm happy with the results, but it wasn't all smooth sailing.

Biggest issue I had with ElasticSearch was that its faceting was kind of weak. The benefit is that its faceting is really easy to use. You just tell ES what field you want to facet in your query and it will return a facet for it on the fly. Very convenient. The downside is that it's pretty barebones in what you can configure, and what it will return. For example, imagine you have a collection of Events, and it has a field called Category, and it had values like "sports" or "music". If you query the collection and facet Category, you'll get something like this (the syntax isn't exact):

    termFacet: [ {
        category: [{
            term: sports,
            count: 40
        },
        {
            term: music,
            count: 20
        ]
    } ]

Pretty straightforward, but since that's all you get, it's pretty limiting. You can filter the facets, of course, but since all you get are the terms, that's what you have to pass to ES. You can't assign an ID to terms, so if you had a really long facet term, you'd have to submit that whole string, which can be pretty awful depending on your UI. I worked around it by hashing the terms, which isn't perfect and can be a little dangerous if there's a collision, but it worked sort of OK.

Since you can't store IDs, you definitely can't store any other metadata you might want, like display text, so the field you want faceted must be stored in the EXACT way you want displayed. This also means that if you tokenized the field beause you wanted users to search on it, the faceting logic will facet the TOKENIZED output. Following the previous example, if there were a category called "College Basketball", that value would result in two facet terms, "College" and "Basketball", each having their own counts. Worse yet, let's say you wanted to apply a lowercase filter on the field as well since most people don't care for case-sensitive searching, the facet terms will all be lowercased, resulting in "college" and "basketball".

So to workaround all that, I stored multiple copies of the same data in different fields so they could be indexed and filtered differently (i.e. category, categoryFacet). One version would be completely untouched, so it could used for faceting, and the other would be tokenized and lowercased with synonyms, etc.

There were more things I'd come across, but I'll continue that in another post.
