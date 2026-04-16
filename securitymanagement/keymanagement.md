---
title: Key Management Template
short_title: Key Management Template
---


Effective key management is a cornerstone when practising Security by Design. 

Rather than creating ad hoc processes, use this structured checklist to ensure cryptographic keys are managed consistently, securely, and in line with best practice.

These controls should be reviewed at least quarterly, and more frequently where risk, regulatory requirements, or system criticality demand it. This checklist also supports alignment with common security standards and audit requirements.

---


## Key Inventory and Classification

Ensure all cryptographic assets are known, classified, and accountable.
```
[ ] All keys are recorded in a central, maintained inventory  
[ ] Each key has a clearly defined purpose and usage scope  
[ ] Ownership and custodianship are formally assigned  
[ ] Keys are classified based on sensitivity and impact  
[ ] Unused or orphaned keys are identified and removed  
```
---

## Access Control and Least Privilege
Limit access strictly to what is necessary, and ensure it is auditable.

```
[ ] Access is restricted to authorised personnel only (least privilege principle)  
[ ] Strong authentication mechanisms are enforced for key access  
[ ] Access is logged and monitored centrally  
[ ] Access reviews are conducted regularly (at least quarterly)  
[ ] No unauthorised or anomalous access has been detected  
```
---

## Key Lifecycle Management (Rotation and Revocation)
Keys must be actively managed throughout their lifecycle to reduce risk.

```
[ ] All keys are within their defined rotation period  
[ ] Automated rotation is implemented where feasible  
[ ] Key revocation procedures are defined and tested  
[ ] Compromised or suspected keys can be revoked immediately  
[ ] Retired keys are securely destroyed and cannot be recovered  
```
---

## Secure Storage and Protection
Keys must be protected against disclosure, loss, and misuse.

```
[ ] Keys are stored in approved secure locations (e.g. HSM, vault)  
[ ] Encryption at rest is enforced using strong, approved algorithms  
[ ] Keys are never stored in plaintext or embedded in source code  
[ ] Backup and recovery procedures are defined and tested  
[ ] Access to backups is equally restricted and monitored  
```
---

## Monitoring, Logging, and Detection
Ensure visibility and rapid detection of misuse or compromise.

```
[ ] Key usage is logged and monitored in real time where possible  
[ ] Alerts are configured for suspicious or anomalous activity  
[ ] Logs are protected against tampering and retained appropriately  
[ ] Integration with SIEM or monitoring tools is in place  
```
---

## Documentation, Governance, and Training
Processes must be maintainable, auditable, and understood.

```
[ ] Key management procedures are documented and kept up to date  
[ ] Emergency and incident response procedures are defined and accessible  
[ ] Roles and responsibilities are clearly documented  
[ ] Personnel receive regular training on key management practices  
[ ] Compliance requirements are mapped and periodically reviewed  
```
---

## Continuous Improvement
Security by Design is an ongoing process.

```
[ ] Lessons learned from incidents and audits are incorporated  
[ ] Controls are reviewed and improved regularly  
[ ] New risks and threats are assessed and mitigated  

```