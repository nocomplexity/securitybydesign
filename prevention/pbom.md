---
title: Open Software Supply Chain Attack Reference Checklist
short_title: Checklist:Supply Chain Attacks
---

A Software Bill of Materials (SBOM) provides a vital ‘ingredients list’ for an application, but it captures only a static view of the final artefact. Mastering Security by Design requires visibility further upstream—into the manufacturing process itself. This is the role of the Pipeline Bill of Materials (PBOM).

The PBOM extends beyond component listing to document the entire ‘factory floor’ of software delivery. 
## Why it matters for prevention

- **Contextual integrity:** An SBOM may list a secure library; a PBOM demonstrates that it was sourced from a trusted origin, verified by defined controls, and built within a hardened environment.

- **Beyond dependencies:** The PBOM captures the security posture of the delivery pipeline itself, including environment configuration, build logic, and approval identities.

- **Attack surface visibility:** By recording the *process*, the PBOM exposes opportunities for compromise within the build pipeline—even when source code remains unchanged.

Adopting the PBOM framework shifts organisations from reactive vulnerability management to proactive assurance. Referencing community standards at [pbom.dev](https://pbom.dev) establishes a transparent, verifiable baseline that spans the entire software lifecycle—not merely the code.


:::{tip}
**Apply the OSC&R framework to minimise supply chain risk**

[Open Software Supply Chain Attack Reference](https://pbom.dev/)
:::
