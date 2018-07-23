---
layout: post
title:  "Twisted induced absence"
date:   2018-07-23
---

Hello everyone, it's been a while. Sorry about that. Just wanted to post an update on how everything is going and the changes that have been taking place ever since ![Syn was released on Pypi](https://pypi.org/project/pysyn).

In earlier posts, I had mentioned that there was a fundamental problem with Syn's design which stemmed from calling the Twisted reactor multiple times, and individually, for each method that did something with Deferreds. So basically, any method that used the underlying Soledad engine (which *only* returns Deferreds), was pretty much doomed from the bottom up. At least from a *Twisted* way of looking at things. Of course, it's not as bad as I put it, I mean... Syn works... right? Yes, it does, it could just be a lot prettier.

So throughout this absence, I dedicated my time to refactoring all of the code that dealt with any and all Deferreds, and changed the underlying architecture quite a bit. Syn is no longer a single process. Rather, Syn now spawns a background process, or a daemon, which is tasked with deploying a local HTTP server with a single reactor. Once this reactor is in place, the core Syn process and the daemon communicate via an HTTP API and pass Deferreds and their results back and forth. So far, that part is working perfectly well, and we can now execute more than one Deferred-dependent method at a time without crashing the reactor.

Now we have to deal with all of the other baggage. Now that we are dealing with multiple processes, and especially asynchronous processes, there is a great deal of things we have to look out for. Currently, I am looking into passing exceptions from the daemon to the parent process so the Syn core knows how to react if there is ever a problem with the HTTP server. This is proving to be a big challenge in and of itself. Another thing I am working on is refactoring the list of tests I once posted about, the ones that could only be run manually because they each ran `reactor.run()` and crashed if this method was called more than once in the same process. Technically, this shouldn't be a problem anymore, but it's not so simple. I am currently looking into this, and thinking about using a testing framework called *Trial* which is supposed to help with testing in a Deferred environment.

Other than that, I also have a lot of documentation to update regarding this new architecture change, especially the design documents.

I've definitely got my work cut out for me, and I'll try my best to keep this blog updated as time passes. All in all, I think it's safe to say that *Twisted* is a really nice name for this networking framework.

-V.

