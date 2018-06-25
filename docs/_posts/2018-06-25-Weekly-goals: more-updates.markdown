---
layout: post
title:  "Weekly goals: update"
date:   2018-06-18
---
Hey reader! Time to sit back and kick back for a little bit. Just kidding, let's take a look at the progress we've made last week.
 
We'll go goal by goal, and I'll describe the contributions made regarding each goal.

+ **Start implementing the Syn MVP:** Implementation has begun, and it is going well! As of the following [commit](https://0xacab.org/vdegou/Syn/tree/2b863de8b590df7c23716043177d9d65e50ec4b1), we have the **data**, **registration**, and **generator** modules working with basic functionality. The **data** module holds the **Entry** class used for storing information and meta-information about the data that the user wants to store, and is compatible with Soledad's internal document structure. The **registration** module succesfully allows a user to register and login. The registration mechanism creates the necessary cryptographic content, and the login checks that, in fact, this content exists and allows access to the funcionality of the system. The **generator** module successfully generates passwords with and without seeds, and allows to specify a desired length. An additional functionality was added which is that of random token-id generation for the stored Entries. Development on the **syn** module has only started recently, but already allows for a very basic interaction via the command line. This goal looks pretty good so far.
 
+ **Implement tests for Syn:** Tests have been implemented as time allows this week. So far, some use cases present in the **registration** module have been tested, such as double registration and others. There has been some difficulties thinking of ways on how to test the **generator** module given that it works with RNG and pRNGs, and these mechanisms require substantial statistical knowledge to test properly. However, one of my mentors recommended using [Dropbox's password strength estimator](https://github.com/dwolfhub/zxcvbn-python) to test password strength. I'll have to study this possibility and see if this approach will provide decent test coverage. The **data** module has not been tested as it holds no functionality as of yet, and is simply a wrapper class for underlying information. Lastly, given that CLI development within the **syn** module has only started today, no tests have been implemented although some cases have been identified.

+ **Look into Python packaging:** I have only had time to take a very basic look at Python packaging. So far, we have a *setup.py* file which allows Python's package manager *pip* to recognize Syn as a package, and some work has been done using *setuptools* "develop" mode.

+ **Define Syn's API:** The API of the MVP can be pretty much obtained from the description of the architecture in one of the previous posts, but summing it up, the API of the MVP is:
	- new_user()
	- login()
	- passphrase()
	- entry()
Some other functions may be identified as progress is made.

-V.
