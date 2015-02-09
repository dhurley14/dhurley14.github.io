---
layout: post
title: "Extracting iPhone SMS Backups"
date: 2015-02-09 13:09:40 -0500
comments: true
categories: 
---

##Background
A few weeks ago my girlfriend had the idea of going back and seeing our first text messages to each other.
Obviously we send each other quite a few messages a day, so just scrolling all the way back in time on the iPhone
was out of the question.  I still thought this was a really neat idea and remembered during the winter of 2013 I had bought ["Violent Python"](http://www.amazon.com/Violent-Python-Cookbook-Penetration-Engineers/dp/1597499579/) by TJ O'Connor.  
In this book, Mr. O'Connor presents code to delve into the iPhone's SMS backups that are stored on the computer.  
I did this exercise back in 2013 and forgot about the code up until now.  This post will detail the issues I ran into 
while trying to extract the data.

##Running Violent Python code

By running the violent python code for [extracting sms messages](https://github.com/shadow-box/Violent-Python-Examples/blob/master/Chapter-3/7-iphoneMessages.py) I was able to see the database tables but not access them.
I was getting an error message when I tried to open the tables up using the sqlite terminal application.  The error
that kept coming through was 

```Bash
devin$ sqlite3 3d0d7e5fb2ce288813306e4d4636395e047a3d28
SQLite version 3.6.12
Enter ".help" for instructions
Enter SQL statements terminated with a ";"
sqlite> .table
Error: file is encrypted or is not a database
sqlite>
```

After quite a bit of googling around trying to figure out how to decrypt the sms database, I found out that the reason 
I was getting this error was due to the fact that I was running an older version of sqlite3, and that the database wasn't
actually encrypted.  I couldn't quite find any reliable ways of upgrading sqlite3 so I just upgraded my Python installation 
to 2.7.9 (I was previously running 2.7.3, oops :).

This successfully displayed the 'message' database that the iPhone uses to store historical texts.  To extract them I ran this
[script](https://github.com/toffer/iphone-sms-backup/blob/master/sms-backup.py) which is more robust than the example script presented
in Violent Python.  After doing so I was left with all of my messages from my girlfriend (after passing in the appropriate parameters 
when I ran the script in the terminal, see usage [here](https://github.com/toffer/iphone-sms-backup/blob/master/README.md))  


##Conclusions
Unfortunately my very first texts to my girlfriend were not in the database.  I am not fully sure at what point the iPhone begins
to remove the oldest messages from its 'message' database but I would be surprised that our conversation history had reached a point
where the iPhone decided it needed to remove that first month of conversations.

I wrote this post to help people out as it seemed there were many people running into this issue 'db encryption' issue while
trying to extract sms data from the iPhone's backup database.  Hopefully this helps out for this wishing to do the same
in the future!  I will continue to try to uncover why some of my backup messages are missing.  Until next time!
