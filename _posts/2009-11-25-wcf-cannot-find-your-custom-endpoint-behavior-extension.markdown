---
layout: post
title: WCF cannot find your custom endpoint behavior extension
date: 2009-11-25 23:15:05.000000000 -08:00
---
Ran into this bug recently. You can find a report on it here. Basically, if you create a custom endpoint extension, like to say support faultexceptions in Silverlight 3, the type attribute of the extension must match EXACTLY the fully qualified assembly name. So for instance if you had something like this:

    <extensions>
        <behaviorExtensions>
            <add name="silverlightFaults"
                type="Sample.SilverlightFaultBehavior,
                Sample,
                Version=1.0.0.0,
                Culture=neutral,
                PublicKeyToken=null" />
        </behaviorExtensions>
    </extensions>

You might think that the above would work in any normal case, but in fact it will fail because WCF or some related loader is matching the extension by string, instead of type. Since the type string is formatted like it is with line breaks, it wouldn't match. So every character counts, and that's why it must fully match the qualified assembly name. You can get the fully qualified assembly name by calling AssemblyQualifiedName on the type of the class. Here's what it would have to look like to work in the above example (truncated because it's too long for the format here):

    <extensions>
        <behaviorExtensions>
            <add name="silverlightFaults"
                type="Sample..., Sample..., Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        </behaviorExtensions>
    </extensions>

Good news is that this will be fixed in .NET 4.0.
