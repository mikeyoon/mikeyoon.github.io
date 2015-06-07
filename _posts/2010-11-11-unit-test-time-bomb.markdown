---
layout: post
title: Unit test time bomb
date: 2010-11-11 23:13:05.000000000 -08:00
---
Fixed a fun unit test today. Apparently a year ago someone wrote a couple tests that checked for results that occurred within seven days from the current date. Normally it would return nothing as there were no rows during this time and there was an assertion to check that it was empty. The dummy data was either too far into the future or already past. However, one year from that day, which happened to be today, two rows happened to come into range of the seven day query, and the test mysteriously failed on our build server at exactly 12:30 PM. 

The lead started looking into the build server logs, and found that the last checkin was only a configuration change, which was puzzling. Further down were frontend changes, also completely unrelated. I decided to debug the unit test and found that the query was pulling data that happened to be exactly seven days from today, causing it to fail. Lesson of the day, one year into the future isn’t far enough. Either that or make smarter tests.
