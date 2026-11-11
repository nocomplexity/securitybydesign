---
title: Checklist for User-centric design
short_title: Checklist:User-centric design
---

Security mechanisms must be usable and understandable even for everyday users. This start at the architecture phase.

## Objective

Ensure that security controls and secure defaults are usable, understandable and aligned with real user workflows, so that security outcomes do not depend on expert behaviour.

## Checklist

### Design secure defaults that require minimal user action

- Ship with the safest practical settings enabled (e.g. least exposure, secure communications, logging enabled).
- Avoid optional security for critical protections; make insecure modes explicit and gated.

### Make set-up and onboarding safe and simple

- Provide a guided first-run experience with clear security steps (e.g. change initial credentials, pair securely, enable updates).
- Minimise manual configuration for sensitive settings; prefer automated secure provisioning.

### Use clear, actionable security messaging

- Write user-facing warnings and error messages that explain what happened, the impact and what to do next.
- Avoid vague messages (‘failed’) or blame-the-user language; include remediation steps.

### Provide least-friction access management

- Prefer role-based access and simple permission models that match user roles (e.g. admin, operator, viewer).
- Support safer auth options where feasible (e.g. MFA for admins, device identity, short-lived tokens).
- Remove/limit insecure recovery paths; make account recovery robust.

### Validate usability to prevent insecure workarounds

- Run quick usability checks on key security flows (onboarding, password reset, admin tasks, updates).
- Use support tickets and pseudonymised telemetry (where appropriate) to detect confusion and misconfiguration trends.

## Minimum evidence

- Secure defaults list (what ships enabled and disabled, with rationale)
- Onboarding / security set-up guide (short, step-by-step, including credentials and update steps)
- Roles/permissions model (roles defined + what each can do)
- User-facing security message catalogue (examples of warnings / error messages and text outlining required remediation measures)
- Usability validation notes (at least three to five users or internal proxies) covering the top security flows, plus resulting fixes

## Release gate

- [ ] Secure defaults reviewed and validated (no critical protections disabled by default)
- [ ] No default admin credentials active; onboarding enforces safe initial set-up
- [ ] Admin/security-critical actions have clear UX (confirmations, guidance and safe recovery paths)
- [ ] Roles and permissions match real user workflows; privilege escalation is explicit and auditable
- [ ] Security warnings / error messages are actionable (what, why, how to fix) and documented
- [ ] Basic usability check completed for key security flows (onboarding, admin tasks, recovery, updates); issues tracked/fixed or accepted with owner + date
