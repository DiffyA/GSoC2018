---
layout: post
title:  "Weekly goals"
date:   2018-06-10
---
Starting from this post, I will try to keep track of weekly goals regarding the progress of Syn. 

I will do a small recap of what has been accomplished so far to set a starting point from now on. The following goals have been accomplished:

+ The existing relevant codebase has been studied. Although this is an ongoing goal, familiarity with different components such as Soledad and Bonafide has been achieved.
+ Development environment has been set up and is fully functional. This goal included looking into Python's virtualenv tool for managing different Python environments in the same machine.
+ The blog environment has been created using Github Pages and Jekyll as a static site building engine. The build process has been completely automated using these tools, allowing to publish blog posts such as this one without much hassle.
+ An initial version of the features of Syn has been identified, as well as identifying a subset of those features that will make up the MVP.
+ An initial architecture design has been proposed and evaluated by mentors.
+ The Twisted framework for event-driven systems has been studied. Again, this is an ongoing goal.

The goals for the following week are as follows:

+ **Start implementing the Syn MVP:** Implementation at this stage will be done according to the MVP feature set as well as the specified architecture. 
+ **Implement tests for Syn:** The methodology to follow will be that of *Test-driven development*, or TDD for short. This means that as, technically, all of the code implemented for Syn should be backed up by the proper tests.
+ **Look into Python packaging:** The expected result from this goal is to be able to execute *pip install syn* and obtain something from Python's package manager. This goal relies heavily on implementation being done, and as I have no prior experience with packaging, it might be one of the more difficult goals. 
+ **Define Syn's API:** Some work has already been done in this regard when the architecture was designed, but a final API has not been specified yet. One of the goals will be to specify a final API, at least for the MVP version of Syn.

Stay tuned for updates on these goals!

-V.
