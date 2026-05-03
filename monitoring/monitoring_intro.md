---
title: Security Monitoring
short_title: Security Monitoring
---


## Introduction

In a Security by Design approach, security monitoring is not an afterthought or a bolt-on activity introduced after deployment. Rather, it is a core architectural capability that must be designed into systems from the outset. While prevention strives to stop attacks before they occur, security monitoring assumes that some threats will inevitably bypass even the best preventative controls. The question is not *if* a breach might occur, but *when*—and how quickly you will know about it.

Security monitoring is the practice of continuously observing, collecting, and analysing security-relevant data from across your systems to detect potential incidents, unusual behaviour, or policy violations. It transforms raw telemetry—logs, metrics, network flows, and user activity—into actionable intelligence that enables security teams to distinguish between routine operations and malicious activity.

Crucially, monitoring is not synonymous with logging. Logging is the recording of events; monitoring is the active interpretation of those events to identify anomalies, trigger alerts, and inform response. Without monitoring, logs are little more than forensic artefacts for post-breach investigation. With monitoring, they become a real‑time sensor network that provides visibility, situational awareness, and early warning.

In Security by Design, effective monitoring requires deliberate architectural decisions: what to monitor, where to collect data, how to protect the monitoring pipeline from tampering, and how to ensure alerts are actionable rather than overwhelming. When designed correctly, security monitoring not only detects attacks but also validates that preventative controls are working as intended and provides evidence for compliance, audit, and continuous improvement.

## Learning Objectives

By the end of this section, you will be able to:

- Define security monitoring and distinguish it from logging and alerting
- Explain why security monitoring must be designed into systems from the outset, not added after deployment
- Recognise the limitations of prevention and why monitoring is essential for detecting inevitable breaches
- Identify the types of telemetry (logs, metrics, network flows, user activity) that support effective monitoring
- Describe the key architectural decisions required to implement security monitoring in a Security by Design context
- Explain how to protect the monitoring pipeline from tampering or disabling by attackers
- Distinguish between actionable alerts and noisy, low‑fidelity signals
- Apply a structured approach to designing, deploying, and operating security monitoring for any system

## Sections

Learn more about security monitoring as a core Security by Design capability in the following sections:


:::{toc}
:context: children
:::