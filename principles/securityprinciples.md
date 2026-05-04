---
title: Crucial and Vital Security by Design Principles
short_title: Security by Design Principles
---

:::{important} 
Security by Design principles do not guarantee security. They are a mandatory aid to thinking, not a replacement for it.
:::

When embracing the Security by design approach you must embrace at least the following principles:

:::{note} Minimise attack surface area
:class: dropdown
Remove unnecessary features, endpoints, and entry points.
:::


:::{note} Establish secure defaults
:class: dropdown
Default configurations should be secure out of the box (e.g., deny by default).
:::


:::{note} Least privilege 
:class: dropdown
Every component and user should have only the minimum privileges necessary to function. (Keep once, not twice.)
:::

:::{note} Separation of duties (and privilege)
:class: dropdown
No single person or process should have excessive authority; split critical functions across multiple actors.
:::


:::{note} Defence in depth 
:class: dropdown
Layer multiple, independent security controls so that failure of one does not lead to system compromise.
:::

:::{note} Fail securely
:class: dropdown
When a system fails, it should default to a closed (deny) state, not an open (allow) state.
:::



:::{note} Complete mediation
:class: dropdown
Every access request must be checked against authorisation rules, without relying on cached decisions.
:::



:::{note} Economy of mechanism (Keep it simple!)
:class: dropdown
Keep security-critical designs as simple and small as possible.

This principle is also known as: 

**Simplicity is better than complexity.**

Security mechanisms should be as simple and small as possible. Complex systems have more vulnerabilities, are harder to verify, and increase the attack surface.


A complicated solution often fails because:

- It's harder to review. No one can fully understand all the interactions.

- It's harder to configure correctly. Admins will make mistakes.

- It's harder to maintain. Fixing one bug often creates two more.

- It often relies on "security by obscurity." The complexity feels secure because it's hard to understand, but that's an illusion.
:::


:::{note} Open design (Avoid security by obscurity)
:class: dropdown
Security must not depend on secrecy of the design or implementation (security by obscurity is explicitly avoided).

This means the architecture, design and software used should be fully transparent. Assume that bad actors have access to the software and crucial documentation. 
:::


:::{note} Zero Trust (Don’t trust services)
:class: dropdown
Never implicitly trust internal or external services; verify everything.
:::


:::{note} Compartmentalisation
:class: dropdown
Isolate components so that a breach in one area does not compromise the whole system.
:::



:::{note} Protect data everywhere
:class: dropdown
Data must be protected at rest (storage), in transit (network) and when 'in-use' , so when processed at CPU level or touched by applications. So critical data should be even protected when applications perform actions on it. So data should always be encrypted. 
:::


:::{note} Design for secure updates
:class: dropdown
Systems must be able to receive and apply security patches safely and reliably.
:::


If you think a principle is not applicable for your situation: **Think again.** Or better write down your motivation and ask for an expert review on your motivation.


## Learn more



These Security by Design principles represent distilled wisdom and long-standing experience from the field. You must always apply them when shaping an architecture or making design decisions.


```{tip} Learn more about security principles
Do not reinvent the wheel by defining your own security principles. Make use of already good defined and battle tested security principles.

In the [Open Security Reference Architecture](https://nocomplexity.com/documents/securityarchitecture/architecture/securityprinciples.html) you can more security principles that can be useful to use or exam.
```

