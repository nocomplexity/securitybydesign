---
title: Least privilege
short_title: Least privilege
---


## Principle


Every user, service and process operates with the feasible minimum permissions needed to do its job. No more and for no longer than necessary.

## Objective

Reduces unauthorised access, limits blast radius, blocks lateral movement and prevents permission creep.

## Checklist

### Define minimum viable permissions for each role/service

- List key roles (user, support, admin) and key services (API, provisioning, updates).
- For each, define allowed actions (read/write/admin) and resources (APIs, tables, buckets).

### Use unique identities (no shared admin)

- One identity per service (service account/workload identity) , applicable to both web services and services running on a device.
- No shared admin users/keys; separate dev/test/prod identities.

### Default-deny and explicit allow lists

- Deny by default; allow only required API methods, data access and network paths.
- Restrict service-to-service access to specific interfaces.

### Time-bound admin access (JIT) + attribution

- Named accounts + MFA for admins (all human users if possible).
- Temporary elevation for privileged actions; break-glass is separate and monitored.

### Automate privilege hygiene

- Monthly (or at each release), flag broad privileges and unused permissions/tokens; remove or time-limit exceptions.

## Minimum evidence

- Access model exists: for example, a short table “Role/Service, allowed actions, resources” stored in repo/wiki and kept current.
- No shared admin:  IAM inventory shows unique service identities; no reused production admin keys.
- Deny works: automated negative tests prove unauthorised API/data access is blocked for each critical service.
- Admins are accountable: logs show who performed privileged actions; elevation expires automatically.
- Creep is controlled: recurring report flags unused/broad permissions; removals/exceptions are tracked, with owner and expiry.

## Release gate

- [ ] Unique service identities; no shared production admin keys
- [ ] Default-deny posture; only required paths enabled
- [ ] Admin access uses MFA and is time bound and logged
- [ ] Automated authorisation tests run verifying correct access restrictions for critical privileged functions
- [ ] Privilege review run; exceptions owned and time limited
