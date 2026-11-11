---
title: Checklist for Configuration and change management
short_title: Checklist:Configuration and change management
---

Secure operation requires configurations to be controlled, consistent and auditable. Baseline hardening standards should be defined and applied through repeatable mechanisms such as templates and infrastructure as code to reduce drift.

## Objective

Prevent security regressions and outages by ensuring that system configuration is controlled, reviewable and reproducible and that changes are assessed for risk before reaching production. The priority is to eliminate ‘silent drift’, tighten defaults and make rollbacks reliable.

## Checklist

### Version and review configuration (treat it as code)

- Store environment config, infrastructure templates and policies in version control.
- Require peer review for changes affecting exposure, identity/access, secrets, networking, logging and updates.

### Harden defaults and prevent drift

- Establish secure baseline configurations (deny by default where possible).
- Use automated checks to detect drift between desired and actual config (at least for critical settings).

### Separate environments and limit privilege

- Strict separation for dev/test/prod (accounts, projects, networks, credentials).
- Restrict who can deploy to prod; remove direct manual changes where feasible.

### Implement change gating and rollback

- Use CI/CD gates for config and infra changes (linting, policy checks, approval).
- Require a rollback plan for each production change (including config-only changes).
- Keep break-glass changes rare, logged and post-reviewed.

### Track and assess security-impacting changes

- Tag changes that affect external exposure, authentication/authorisation, crypto, update mechanisms, data classification or third-party integrations.
- Ensure that a lightweight threat/risk review is triggered when these tags apply.

## Minimum evidence

- Config versioned: repo location(s) for IaC, config, policies; PR review history
- Baseline config: documented secure defaults and key settings (one page)
- Drift detection: a scheduled job/report or tool output for critical config items
- Deployment controls: proof that prod changes go through CI/CD (or change tickets) and are attributable
- Rollback proof: last rollback test result or documented rollback procedure
- Change log: record of security-impacting changes and associated approvals

## Release gate

- [ ] Production config/IaC changes are versioned and peer-reviewed (no untracked manual edits)
- [ ] Secure baseline defaults applied; new components inherit baseline (network, IAM, logging)
- [ ] Dev/test/prod separated (accounts, projects, credentials); least privilege enforced for deployers
- [ ] CI/CD gates applied to config changes (policy checks, linting); approvals recorded
- [ ] Rollback plan exists for this release; rollback procedure tested recently or validated
- [ ] Security-impacting changes tagged and reviewed (exposure, IAM, secrets, crypto, updates, integrations)
- [ ] Exceptions (emergency changes) logged and post-reviewed, with owner and expiry date
