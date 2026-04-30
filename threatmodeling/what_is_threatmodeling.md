---
title: What is Threat Modeling
short_title: What is Threat Modeling
---

A powerful way to create secure systems is to "think like an attacker" before you write code or make changes to it.

Threat modeling is the process of examining your requirements and design to consider how an attacker might exploit or break into your system, so you can prevent those issues early. For our purposes, we treat "attack modeling" as a synonym, even though some people use the terms differently. Industry terminology differs a lot here, but the important part is what you do, not what you call it.

A major advantage of threat modeling is that you can perform it before the design is decided or the code is written. This makes it especially useful during the early development of a new system.


## Where to Start: Attack? Assets? Design?

There are many ways to approach threat modeling. Different methods may start with:

- The attacker: What are their goals? Capabilities? Typical methods?

+++


- The assets:What needs protecting?

+++



- The system design: How components interact; where trust boundaries lie

From a Security By Design perspective consider all of these.



## How Threat Modeling Is Performed

One approach is to build attack trees, where each tree represents an undesirable event the attacker aims to cause. The tree works backward to show how the event could happen (hopefully, you will show that it cannot happen or is exceedingly unlikely). This can be effective, but it requires expertise in attack methods; expertise many developers do not have.

Other approaches focus on analyzing a specific organization. If your software will be deployed across many organizations, this method becomes less useful.

For our purposes, the next section will focus on a simple and practical method called [STRIDE](stride_model.md).