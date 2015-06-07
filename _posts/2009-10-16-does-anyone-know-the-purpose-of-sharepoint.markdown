---
layout: post
title: Does anyone know the purpose of Sharepoint?
date: 2009-10-16 23:18:11.000000000 -08:00
---
Now I'm not trying to argue that Sharepoint has no purpose, but when I see highly (and I mean highly) customized implementations of Sharepoint, I sometimes wonder if we've gone a bit too far trying to make the square peg fit the round hole.

Looking at Sharepoint as a complete product, out of the box it provides several very nice features such as a document repository, news portal, etc. Many clients use it to host internet facing web sites and portals, and it seems to work reasonably well as long as Sharepoint is used as the content management and delivery mechanism. Sometimes, however, I see only a portion of it used, like the content management side just to store some lists and forms, while a completely custom website is built to deliver the content. Now it gets all hokey because Sharepoint doesn't provide a very good API for accessing this content in a way that would be necessary for a webiste. 

So the solution? Have events fire on Sharepoint that would copy the data from a list into another database so it can be served! So now you have this really monster Sharepoint CMS, and all it's doing is storing some arbitrary lists and managing rights. As a result, now the company has to maintain a set of custom features to just synchronize the data with another DB. Then you have to have consultants to manage these custom features, and tasks that used to be simple like deployment and backup/restore now require a huge list of steps to execute and if anything goes wrong you'll end up researching the problem on Google because there's no way anyone other than the Sharepoint product team that could debug any of its error messages.

The real solution then? I'm not sure, but for any large CMS package like Sharepoint, I'd recommend not to use it unless you use it as its designers intended, to manage and deliver content all in one system. You want a system to manage just content and not deliver it? There are simpler products for that, don't just throw Sharepoint at it because it was free with your MS licenses.
