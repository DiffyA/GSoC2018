---
layout: post
title:  "Architecture"
date:   2018-06-09
---
Hey!

In this post, we will take a closer look at the first revision of Syn's architecture. We will take a look at the modules and their dependencies.

In the following image, Syn's internal modules are displayed. It is important to take into account that in these first depictions of the system architecture, a difference is made between the features and modules included in the MVP version of Syn, as well as those thought of for future versions. It is pretty much safe to say that this initial architecture plan is prone to changes as development is carried out.

![Syn architecture modules](https://raw.githubusercontent.com/DiffyA/GSoC2018/master/docs/_assets/architecture_modules.png)

As can be observed from the previous image, the following modules make up the Syn system:

+ **registration:** this module will be responsible for allowing user registration as well as user login. Both functionalities will be present in the online and offline modes of operation. Questioning the necessity of a "registration" functionality while offline makes perfect sense. The reason for this is the following: the act of registering a user basically boils down to creating cryptographic content used for both online and offline encryption. This cryptographic content is entirely dependent on the uuid/passphrase combination chosen by a user when it registers, so in this case, registration is not necessarily linked to creating authentication details and storing them in an online database for future lookup and permission access like in most login systems, but rather registration is associated to the creation of cryptographic material dependent on a user's credentials. Following this scheme, this method would allow a user to fully benefit from the functionality that Syn offers while in offline mode (except online synchronization across devices, which is pretty self explanatory if the user is in offline mode). In future versions, this module will be expanded (or a new one altogether will be created) in charge of dealing with offline to online data migration, merging, and sync'ing. 

+ **generator:** the responsibilities of this module are pretty straight forward. This module is in charge of generating the different types of data needed within the system. For example, it is in charge of generating passwords in different ways. The MVP version of Syn comprises password generation following two approaches: a *deterministic* approach and a *non-deterministic* approach. The difference between the two being that a password made following the deterministic approach will be created using a random seed value. The "randomness" of the generator is obtained from the seed, so presenting the same seed to the generator will allow to obtain the same password all the time. On the other hand, the non-deterministic approach uses random data obtained from the device to create the password, so there is no "seed" we can use in order to retrieve the password again.

+ **data:** this module is in charge of defining the classes used to represent the data structures used throughout the Syn system. Examples of these data structures are the "Password" and "Note" entities, which are the basic units of information that will be stored in the system. As of now, both the "Password" and "Note" entities are general enough to fit into the same structure, aptly named "Entry". In short, this module is in charge of encapsulating the data used within the system.

+ **auth:** this module still has a lot of work to undergo. It will be in charge of all authentication processes when online mode is engaged. This is the reason why authentication is not simply coupled with the registration module. This is due to registration being an offline/online capable module while authentication is only really necessary in online mode, when validating the user account to 3rd party providers is necessary. Given that it is a module which relies entirely on an online connection, it does not fit into the scope of the MVP.

+ **syn:** this module is the main entry point of the Syn app. As of now, it simply encompasses the code that makes the app compatible with the terminal. In other words, it provides the CLI.

Apart from this initial revision of the system architecture, a high-level dependency diagram has also been drafted. Again, MVP versions and future versions have been tagged, and of course, this dependency diagram is subject to change.

![Syn architecture dependencies] (https://raw.githubusercontent.com/DiffyA/GSoC2018/master/docs/_assets/architecture_dependencies.png)

So far, the following dependencies have been identified:

+ **Soledad:** this should be no surprise. Soledad is the main driving force behind the Syn app, and the foundation on which it is built on. As of now, Soledad is a direct dependency of the **entry** and **registration** modules. Soledad is necessary in the **entry** module because data entries are wrapped in one of Soledad's internal Document classes. With regards to the **registration** module, given that one of the responsibilities of that module is to create the cryptographic material, and Soledad is the component which knows exactly how to do this, direct communication between the two is necessary.
+ **Bonafide:** this system will be used in the future with the **auth** module given that it handles online authentication with 3rd party providers.
+ **Click:** Click is a Python library used to create command line interfaces for applications. It is a dependency of the **syn** module given that this module is our entry point to the Syn system. This library will allow us to execute commands directly through the commandline, have Syn process them, and give us the result.

I hope all of that made sense! See you in the next one!

-V.
