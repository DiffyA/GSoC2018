---
layout: post
title:  "Weekly goals"
date:   2018-06-25
---
As a followup to the update on last week's goals, here are the goals of this week:

+ **Refactor Syn's semantics:** after a meeting with my GSoC tutors, the conclusion was reached that, although the project was heading in the right direction, the terminology used was a bit confusing. Up to now, I was focusing the usage of Syn based on the terms of "users" due to a misunderstanding I had regarding Soledad's semantics. This allowed things such as "registering" and "logging in" while offline, which even though it made sense to me, it is very easy to see how it could confuse someone just looking to use the app casually. 

+ **Refactor usage of Twisted code:** this one is a bit tricky. Basically, Soledad runs on the Twisted networking framework, and given that Syn is built on top of Soledad, Twisted code still has to be handled and used. The Twisted framework runs asynchronously, which requires a drastic change of mind on behalf of the developer when writing code. A developer's best friend when writing asynchronous code is using what is known as *callback functions*. I won't go into detail exactly what they are about here, but let's just say they bring their own complications to the game. As if that weren't enough, Twisted has its own structures used to handle these callbacks internally, which only makes everything even more complicated! As such, my code is riddled with incoherent (although functioning) uses of Twisted's natives, which should be cleaned up. This ties in nicely with the next goal however, so we have that!

+ **Implement a daemon to handle Twisted code:** another thing that came up during the meeting with my tutors was the possibility of decoupling most of the Twisted and Soledad code into a background process, and use the Syn progress as a standalone. This would require the usage of a communications protocol to allow the exchange of information between these two processes (local HTTP calls will probably be put in place), but it would make the code much more readable. 

+ **Tests:** everything must be tested! From the core functionality, to the CLI, to the planned daemonized solution!

+ **Online functionality and synchronization:** This is a big one. Once we have closed all remaining matters in offline Syn, we must tackle another beast: online functionality. This will encompass its own subset of goals and problems, and we will talk about those once we reach them.

That's all for now! See you soon!

-V.
