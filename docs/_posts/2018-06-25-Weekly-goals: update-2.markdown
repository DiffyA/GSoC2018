---
layout: post
title:  "Weekly goals: MVP completed!"
date:   2018-06-25
---
Hello! Time for another update on how progress is going.

Regarding the goals that were being discussed last week:

+ **Implementation of the Syn MVP:** Implementation of the Syn MVP is pretty much done! Some screenshots of Syn can be seen below:

![Syn MVP basic usage](https://raw.githubusercontent.com/DiffyA/GSoC2018/master/docs/_assets/Syn_MVP_1.png)

We can see how Syn can be used through the CLI thanks to the Click library. Calling the `syn` command brings up that prompt, showing us a brief description of the program as well as the commands that can be called within it.

![Syn create new entry and list existing entries](https://raw.githubusercontent.com/DiffyA/GSoC2018/master/docs/_assets/Syn_MVP_2.png)
 
Here, we can see what it looks like to create a new entry within Syn with `syn new-entry`. Afterwards, we call `syn list` to get the names of the entries that exist within the database. 

![Syn get entry details](https://raw.githubusercontent.com/DiffyA/GSoC2018/master/docs/_assets/Syn_MVP_3.png)

To finalize a typical use within Syn, we can use `syn get` to obtain the details of a specific entry. This will give us the dates of creation and modification of the entries, as well as copying the password to the system clipboard. (Actually, this feature is still yet to be implemented, but it is pretty easy to do).

+ **Implement tests for Syn:** Tests are yet to be completed for all modules as well as CLI interface.

+ **Look into Python packaging:** Packaging has been looked into. A first release of Syn version 0.1.0 will be made to the Python Package Index as soon as refactoring has been done to the existing MVP codebase, as well as some dependency issues with the main Soledad engine have been fixed. The issue with Soledad is that a commit has been made to the Soledad repo that is necessary for Syn to work, however, the Soledad Pypi entry has not been updated to accomodate this commit and thus Syn will be broken on machines which have not manually updated the Syn libraries.

To keep this post short, I will upload this upcoming week's goals to another entry.

Thanks for reading!

-V.
