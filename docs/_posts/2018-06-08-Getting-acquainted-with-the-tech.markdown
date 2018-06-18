---
layout: post
title:  "Getting acquainted with the tech"
date:   2018-06-08
---
Hello! It's been a while. The week has been a little busy and I've got quite a backlog.

I wanted to take some time to talk about the actual technology being developed at LEAP and my role in the project. 

LEAP is formed of a group of people interested in making online communications secure, using open source software as their main tool. They have multiple ongoing projects, and one of the most notable is [Bitmask](https://bitmask.net/), an application supporting encrypted internet and email. 

During my time with LEAP, I will not be touching much of that particular application. Rather, my interest lays within other components they have developed. One such component is [Soledad](https://leap.se/soledad). Soledad is a software system that allows for the synchronization of data across devices. It does so via a client-server architecture, where the different devices use the client to upload data that wants to be shared to the server, and the server allows clients to obtain the shared information by authenticating themselves. The interesting part is that all of this is done with algorithms that result in cryptographically secure data, meaning that even though the data is stored on a server, it is unfeasible for anybody but the intended user to make any sense of it since it is all encrypted before being stored. Only the real user is able to read the data after it authenticates itself. 

On the other hand, we have a software component called [Bonafide](https://leap.se/en/docs/design/bonafide). Although this system might not have as much functionality as Soledad, it still plays a crucial role in the big picture. Bonafide is the component in charge of providing user authentication. I won't get into too much detail, but it does so by making use of valid certificates and authentication tokens. 

To summarize these two components, we can say that Soledad throws the heavy punches; it is in charge of providing persistent and synchronized storage across different devices, while Bonafide is the component responsible for making sure a user is who it states it is.

Having contextualized LEAP's projects and the software components that the password manager will rely on, we can start to discuss its creation. My goal is to leverage these two systems in order to create... **Syn: a password manager which prioritizes data confidentiality and availability above all**. However, Syn will be different than many existing managers (KeePass, LastPass, etc.), because it will have two very important characteristics: **native synchronization across devices**, and **cryptographically secure data storage**. These two aspects will be explained in further detail in an upcoming post, where I will compare Syn to existing managers as well as describe its features.


-V.
