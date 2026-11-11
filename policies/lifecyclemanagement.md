---
title: Checklist for Life-cycle management
short_title: Checklist:Life-cycle management
---


## Principle

Security responsibilities extend beyond initial development. Components must be maintained, updated and eventually retired in a controlled manner.

The figure below illustrates the relationships between the product life cycle and Secure by Design principles, and risk management activities.Risk management activities establish the security context by clarifying key considerations, including what needs to be protected, the relevant threats, and the acceptable level of residual risk given the product’s intended purpose and security impact. Secure by Design principles then translate this context into practical security decisions that are applied throughout the product life cycle, from requirements through to end of life.

:::{figure} /images/sbd_and_lifecycle.png
:alt: Security By Design and Life Cycle Management
:align: center

Secure by Design and Life Cycle Management  
*Adapted from the [ENISA Secure by Design and Default Playbook](../notice.md) (CC BY 4.0)*
:::



## Objective

Ensure that security is maintained throughout the product’s full life cycle, from requirements and design through to deployment, maintenance and end of life, so that security does not degrade over time.

## Checklist

### Define support commitments and life-cycle states

- Publish (internally or externally) life-cycle states: active, maintenance, end of sale, end of support, end of life.
- Define minimum support windows and internal guidelines for vulnerability triage and patch release timelines (e.g. critical vulnerabilities triaged in 48 hours; fixes released within X days where feasible).

### Make the product updatable and recoverable

- Implement a secure and transactional update mechanism (authenticated updates, integrity protected, rollback/recovery plan).
- Ensure safe failure modes (do not brick devices; provide fallback/rollback where practical).
- During maintenance or recovery modes, restrict functionality and privileges to the minimum necessary to support update and recovery operations.

### Operate vulnerability and patch management

- Track vulnerabilities from third-party components, internal findings and customer reports.
- Maintain an SBOM (at least for each release) and a dependency update cadence.
- Triage, prioritise and document risk decisions (fix, mitigate, accept with expiry date).

### Monitor, log and handle incidents

- Enable security-relevant logging (auth events, admin actions, update events, boundary-crossing requests).
- Define minimum incident response steps, for instance detection, triage, containment, remediation, customer communication

### Learn from deployed products

-	Collect evidence on how products are deployed and used, such as customer feedback, support cases, vulnerability reports, incident findings and privacy-preserving telemetry where appropriate.
-	Review findings, including the results of root-cause analyses where applicable, and update the product context, risk assessment, threat model and secure defaults as necessary.

### Plan secure decommissioning and disposal

- Provide data erasure mechanisms and guidance (factory reset, key revocation, account deprovisioning).
- Revoke credentials/keys when devices/users/services are deactivated.
- Ensure that end-of-life guidance includes what security updates cease and what customers must do.

## Minimum evidence

- Life-cycle policy: lifecycle states + support window + patch SLAs (1 page).
- Update/release process: secure update approach and rollback/recovery notes
- Vulnerability management log: intake, triage, prioritisation, disposition, owner, dates (ticket board or spreadsheet)
- SBOM: SBOM generated in a machine-readable, widely adopted standard format (e.g. SPDX or CycloneDX) and stored with the release
- Monitoring and logging: security logging baseline + sample logs showing admin/auth/update events
- Field feedback record: summary of relevant operational evidence reviewed, deviations from deployment assumptions identified and resulting actions
- Decommissioning checklist: data wipe/reset, credential/key revocation and customer guidance

## Release gate

- [ ] Support status and life-cycle state for this release are clear (active, maintenance, etc.)
- [ ] Secure update path validated (integrity/authentication checks, rollback/recovery documented)
- [ ] Vulnerability triage completed for known issues (including third-party dependencies); decisions recorded (fix/mitigate/accept with expiry)
- [ ] SBOM generated (or dependency inventory updated) and stored for the release
- [ ] Security logging verified for critical events (authentication, admin actions, updates)
- [ ] Decommissioning actions defined for components introduced/changed (e.g. reset, wipe, key revocation)
- [ ] Any accepted residual security risk has an owner and review/expiry date
