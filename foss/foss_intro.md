---
title: Open Source Security
short_title: Open Source Security
---

## Introduction

In a Security by Design approach, the selection of foundational technologies is a deliberate architectural decision made at the outset of any project, rather than a tactical choice after deployment. A core principle is to **prefer open solutions—specifically Free and Open Source Software (FOSS)—over closed-source alternatives** wherever feasible. This preference stems directly from the need for verifiable trust, transparency, and long-term resilience.

:::{important}
Open source code can be inspected, audited, and improved by anyone. 
:::

This public scrutiny dramatically reduces the likelihood of undetected vulnerabilities, hidden backdoors, or vendor-specific weaknesses that cannot be independently verified. Security researchers, enterprise teams, and individual contributors worldwide can—and regularly do—examine the same codebase, leading to faster identification and remediation of issues. In contrast, closed-source solutions require users to trust the vendor's internal processes and integrity without any means of independent confirmation. This "black-box" model introduces inherent supply-chain risk, particularly in environments where regulatory compliance, data sovereignty, or zero-trust principles demand demonstrable assurance.

Security by Design demands that architects actively favour open, auditable solutions as the default position. However, it also requires disciplined judgement: openness must serve security, not become an end in itself. Not all open source software is inherently secure; poor maintenance, abandoned projects, or overly complex codebases can introduce risks of their own. Therefore, a balanced approach is needed—one that combines the transparency of FOSS with rigorous selection criteria focused on code quality, community health, operational sustainability, and active security maintenance.

By integrating these principles, organisations can build systems that are both inherently more trustworthy and practically maintainable throughout their entire lifecycle. Open source security, when approached strategically, becomes a cornerstone of verifiable, resilient, and future-proof architecture.

## Learning Objectives

By the end of this section, you will be able to:

- Explain why Security by Design favours Free and Open Source Software (FOSS) over closed-source alternatives where feasible
- Compare the transparency and trust models of open source versus closed-source solutions
- Identify the security advantages of public code scrutiny, including faster vulnerability discovery and remediation
- Recognise the risks associated with closed-source "black-box" dependencies, including supply-chain and vendor lock-in concerns
- Evaluate when openness serves security objectives and when it may introduce its own risks (e.g., abandoned or poorly maintained projects)
- Apply a disciplined checklist to assess the security, quality, and sustainability of open source components
- Describe practical strategies for building verifiable trust in software dependencies without assuming that "open" automatically means "secure"

## Sections


:::{toc}
:context: children
:::
