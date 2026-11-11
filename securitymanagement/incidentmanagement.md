---
title: Checklist for Incident response and recovery
short_title: Checklist:Incident response and recovery
---


Developers must be prepared to respond quickly and effectively to security incidents that affect their products in the field, including vulnerabilities, compromised code, malicious updates and misuse of product functionality.

## Objective

Detect, contain and recover from security incidents quickly while limiting customer impact and preventing recurrence. The priority is a clear playbook, defined ownership, fast triage and reliable restore/rollback, supported by the minimum logging and backup evidence needed to act decisively.

## Checklist

### Define roles, escalation and decision authority

- Name an incident lead and backups. Define escalation thresholds, when senior management should be informed and who can approve customer communications, access elevation and emergency changes.
- Maintain an on-call/escalation contact list (internal + key suppliers).

### Create a minimal incident runbook

- Steps: detect, triage, contain, eradicate, recover, lessons learned.
- Include checklists for common scenarios (account takeover, leaked API key, ransomware/supply-chain alert, compromised device fleet).

### Prepare containment controls

- Make sure that it is possible to revoke/rotate credentials and keys quickly.
- Make sure that it is possible to disable accounts, block IPs, quarantine services/devices and roll back releases / config changes.
- Ensure that break-glass access exists, is logged and is time bound.

### Ensure recovery capability

- Backups are automated, tested and protected (immutability where feasible).
- Restore procedures are documented for critical systems (data, configs, secrets, device firmware if applicable).
- Define RTO/RPO targets appropriate to the business.

### Exercise and improve

- Run a lightweight tabletop exercise (30–60 minutes) quarterly on one realistic scenario.
- Record and review incidents, near misses and exercise findings. Track actions to closure and update risk assessments, threat models, runbooks, detections and controls as appropriate.

## Minimum evidence

- Defined roles: incident-response contact list + roles (owner, backups and escalation paths established).
- Runbooks: one-page incident runbook and at least one scenario checklist.
- Containment proof: documented steps and permissions to revoke keys, disable accounts and roll back deployments.
- Backup/restore proof: backup configuration and one recent restore test result (or evidence of a successful restore).
- Post-incident, near-miss or tabletop notes showing findings, actions and resulting improvements.

## Release gate

- [ ] IR roles and escalation contacts are current
- [ ] Incident runbook exists and covers detect/triage/contain/recover with at least one common scenario
- [ ] Containment actions are ready
- [ ] Backups enabled for critical data/config; restore procedure documented and tested recently
- [ ] Post-incident, near-miss or tabletop notes exist and are updated
