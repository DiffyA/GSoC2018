---
layout: post
title:  "Features of Syn"
date:   2018-06-09
---
It is time to take a look at the origins of Syn and the features I wish to implement during my time at LEAP.

First of all, let's backtrack a little and remember the two main characteristics that Syn will strive to accomplish. They are: **native synchronization across devices** and **cryptographically secure data storage.** 

These two characteristics are achieved by using Soledad as the engine that powers Syn's storage functionalities. But how exactly does this make Syn stand out against modern password managers of today? 

This analysis starts with my favorite password management app out there: KeePass. I have been an avid user of KeePass for over three years, and it has met all of my desires when it comes to secure data storage. Some of the things I like best about it are:

+ **Completely functional while offline:** there are many password managers out there that only work with an active internet connection. I find this to be pretty limiting in a lot of cases. Not only that, but it can be unnecessarily unsafe. Why does the manager need to connect to the internet? Why can't it store the information locally? Is my credit card PIN being stored in some database on the other side of the world? 
Thinking logically, of course there are benefits of having a manager that connects to the internet. I can think of at least one benefit: persistent backups stored on another system. If my hard drive gets destroyed, I can just restore all my passwords since my manager was creating backups and storing them all on the cloud. This brings both relief... and *terror*. I don't think there's much need of pointing out all the things that could go wrong when storing sensitive information in the cloud.
Luckily, KeePass is able to provide all of its functionality without an internet connection. In fact, KeePass is intended to be used without being online. I find this to be a wonderful advantage, and if the need ever arises, plugins can be installed to allow KeePass to connect to the internet.

+ **Possibility to sync devices:** KeePass also offers the possibility to sync data with other devices. However, it is not a native feature and it can only be obtained via plug-ins. This makes the whole process a bit clunky, since the actual feature is developed by a third party and the synchronization depends on storing database files on file sharing websites, like Google Drive. Hmm... I wonder if anything could go wrong here? Even with these drawbacks, if you take the proper safety precautions to encrypt your data files, the data should be safe when using these third-party features. The point still stands, however, that by using these non-native synchronization features, you are placing a great deal of trust in third party services and file sharing websites which are, more often than not, not open source and thus not open to public security audits. 

+ **On-the-fly password generation:** definitely one of my favorite things about KeePass is its password generation. Most sites that require a password nowadays generally ask for passwords that meet certain requirements, such as alphanumeric characters only, a minimum length, no special symbols, etc. With KeePass, one can specify the restrictions  and it will give you a password that meets them. 

These are three of the strong qualities that KeePass has, and I believe they can be analyzed in depth and implemented in Syn. For instance, another feature that I will strive for Syn to have is the ability to be fully operational while offline, but also have the ability to sync with other devices in a secure way when an internet connection is present. This might prove to be very difficult because some of the underlying mechanisms of Soledad must be rethought. Syn will differ from KeePass in this aspect because it will provide data synchronization natively, while KeePass depends on third party plug-ins to achieve it. Another important feature is the password generation, which is also inspired by KeePass. The goal here is for Syn to provide an easy to use password generation tool which allows for quick integration with the system's clipboard. 

To sum this post up, the following list presents the desired features of Syn:

+ **Offline functionality:** Syn should remain completely functional when offline. The only feature it won't be able to have when in offline mode is the data synchronization across devices. Even then, it will be able to synchronize all changes made during offline mode when it an internet connection is established. 
+ **Data storage:** Syn should be able to store passwords and arbitrary data.
+ **Data synchronization across devices:** making use of the underlying Soledad system, Syn should be able to synchronize data across devices.
+ **Data grouping:** data entries should be able to be filtered and ordered by a number of parameters, such as by category or device with which it was uploaded.
+ **Synchronization granularity (stretch goal):** this feature is about being able to synchronize data entries with certain devices. For example, a use case might be that a user wants to store a password which only his PC and tablet should be able to access, but not his phone. This would allow entries to be tagged by device and enable/disable syncing of data entries. This is deemed a stretch goal because it will be implemented once all other goals and features are in place, and if time and resources allows.
+ **Password generation:** this feature will allow Syn to produce random passwords that adhere to a defined set of rules. It will also allow the password to be directly copied to the clipboard. 

However, an initial version (or *MVP*) will focus on the following subset of features:

+ **Offline functionality:** at this stage, Syn will be able to function while offline. Online synchronization will not be implemented.
+ **Data storage:** Syn must be able to store both passwords and regular data entries.
+ **Password generation:** a basic password generation feature will be put in place.

The complete version of Syn is intended to have its own easy to use GUI, while the MVP is meant to be used via a command line interface. The CLI will act as a way of interfacing with the system, and will allow the user to perform actions such as registering a new user, logging in, creating a new password, etc. These functionalities will be explained further in the following post, which talks about the architecture of the system and the API exposed by Syn. 

-V.
