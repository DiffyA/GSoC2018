---
layout: post
title:  "Syn Released!"
date:   2018-07-12
---

Today is a remarkable day. Today marks the first day since Syn has been released on the ![Python Package Index](https://pypi.org)! 

At this stage of development, Syn is able to accomplish the following tasks:

+ Create a new local database
+ Open a local database, preparing it for read and write operations
+ Close a database to lock it away
+ List the entries stored in an open database
+ Get the contents of an entry and copy the sensitive parts to the system clipboard
+ Add new entries to an open database
+ Delete entries from an open database
+ Generate random passphrases with the possibility to specify a length and a seed

So far, we have a fully functional password manager. Of course there are still many things to be improved and added, but I am quite happy with what has been made up to this point. Development doesn't stop here though, the task I've been undertaking for quite some time now is still ongoing, which is basically to refactor the whole application starting from the architecture level in order to incorporate a *daemon* (or *background*) process. The goal of this daemon is to serve as a centralized process which will hold the Twisted reactor, allowing us to decouple a lot of the Twisted functionality that currently exists within Syn, and also, to only have one reactor running at a time for as long as we want. Currently, when Syn performs some functionality, it instantiates a reactor, does whatever it needs to do, and then closes the reactor. However, this does not follow Twisted philosophy as the reactor object is something that should live indefinitely for the duration of the process. The current task aims to fix this issue, which will also fix things like the automated testing (which currently needs to be executed manually for those tests that cover functions which instantiate a reactor).

Getting back to the release of Syn, it can now be installed as a python package with the following command:

```pip install pysyn```

Information regarding the package can be found at the ![project page](https://pypi.org/project/pysyn)

-V.

