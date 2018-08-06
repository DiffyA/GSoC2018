---
layout: post
title:  "Architecture Update"
date:   2018-08-06
---

Hey all! We're in the final stretch of GSoC! Just one more week and this three month adventure will have finished.

Given that up to this point, there will be no more changes or additions to code functionality until after GSoC (right now I am focused on getting the documentation up to spiff and improving test coverage), I thought it would be a good time to update the design diagrams. As was to be expected, there have been slight changes to the architecture and module diagrams that I created almost two months ago.

Without further ado, here is the updated architecture diagram:

![Syn architecture modules](https://raw.githubusercontent.com/DiffyA/GSoC2018/master/docs/_assets/architecture_modules_v0.0.21.png)

One of the differences between this version of the document and the previous one is that I include the method and function names that can be found in each module, so a developer could easily contextualize what is going on. Also, I have specified the **DataManager** and **Entry** classes within the *data* module. The *syn_utils* module has also been added.

Similarly, there's been a couple of updates to the dependency diagram:

![Syn architecture dependencies](https://raw.githubusercontent.com/DiffyA/GSoC2018/master/docs/_assets/architecture_dependencies_v0.0.21.png)

More dependencies have been included as well as taking into account the new additions from the architecture diagram, like the new classes and modules.

That's all for now!

-V.

