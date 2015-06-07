---
layout: post
title: PygramETL and MS SQL Server
date: 2011-11-14 23:05:57.000000000 -08:00
---
I recently started playing around with [pygrametl](http://pygrametl.org "pygrametl") to see what it's like to deal with datawarehouses, and found some rough spots in getting it going with MSSQL. Pygrametl supports any database driver as long as it's PEP 249 compliant, or a JDBC driver if you are using Jython. First I decided to try the Microsoft JDBC driver under Jython, but that didn't work because getParameterMetaData() generated invalid SQL for some odd reason. Next I moved onto adodbapi, which is a part of pywin32, and fortunately that worked well. 

The unfortunate problem with this combination was that I was using CPython, and it was pretty slow. Since ADO doesn't support threading (I feel like most DB drivers don't), I couldn't spin off threads to speed things up, so I settled on trying to get it to run under IronPython, which supports adodbapi, for the most part. One thing that got me was that adodbapi wouldn't handle "None" properly when creating empty parameters under IronPython, which is what PygramETL sets everything to by default. The error I'd get is similar to this:

	[Microsoft][ODBC SQL Server Driver]Invalid use of default parameter

which was pretty baffling. Through a lot of debugging and trial and error I worked around that by setting the defaults to DBNull.Value, which seemed to work reasonably well, but I did have to add platform checks to the PygramETL source, which will be a slightly annoying thing to maintain going forward. At least it's about 30-40% faster than CPython so it'll be worth it.

Overall I'm pretty impressed with the tool. It's pretty easy to maintain and is a lot easier to source control than SSIS packages. The developers are still maintaining it, and are planning on adding threading, so the performance will be hugely improved once they can get that out the door.
