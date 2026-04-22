---
title: Security Logging
short_title: Security Logging
---

## Security Logging

Security logging is the practice of recording events within a system that are relevant to security, such as authentication attempts, access to sensitive resources, configuration changes, and system errors. These logs provide a traceable record of activity across applications, infrastructure, and services.

Effective security logging is vital to a Security by Design approach because it ensures visibility into how a system is used and how it may be abused. By capturing meaningful and well-structured log data, organisations can detect suspicious behaviour, investigate incidents, and respond in a timely manner.

In addition, security logging supports accountability and continuous improvement. It enables teams to validate security controls, identify weaknesses, and refine defences over time. Without adequate logging, even well-designed systems may fail to detect or respond to security incidents, undermining the overall effectiveness of their security posture.


## Essentials 

Always implement a dedicated audit logging system separate from operational logs. Audit logs track security-relevant events for:

- **Compliance**: SOC 2, GDPR data access tracking
- **Forensics**: Security incident investigation
- **Monitoring**: Real-time security alerting (via log aggregation)

### Key Principles

| Principle | Implementation |
|-----------|----------------|
| **No PII** | Only IDs logged, never names/emails |
| **No Secrets** | Tokens, keys, passwords never logged |
| **Trace Correlation** | trace_id links to request logs |
| **Structured Format** | JSON for machine parsing |
| **Immutable** | Events cannot be modified after creation |



## Example Event Schema for Audit logging


| Field | Type | Description |
|-------|------|-------------|
| `timestamp` | string | ISO 8601 UTC timestamp |
| `event_id` | string | Unique event identifier (evt_xxxx) |
| `event_type` | string | Event type (see Section 4) |
| `event_category` | string | Category grouping |
| `severity` | string | info, warning, error, critical |
| `actor` | object | Who performed the action |
| `resource` | object | What was accessed/modified |
| `action` | string | create, read, update, delete, access, validate |
| `outcome` | string | success, failure, denied, error |
| `details` | object | Additional context (varies by event) |
| `trace_id` | string | Request correlation ID |
| `service` | string | Service that generated the event |


## Typical Audit Event Types

### Authentication Events

| Event Type | Severity | Description |
|------------|----------|-------------|
| `AUTH_SUCCESS` | info | JWT token issued successfully |
| `AUTH_FAILURE` | warning | Token generation failed |
| `AUTH_TOKEN_EXPIRED` | info | Token validation - expired |
| `AUTH_TOKEN_INVALID` | warning | Token validation - invalid signature |

### mTLS Events

| Event Type | Severity | Description |
|------------|----------|-------------|
| `MTLS_SUCCESS` | info | mTLS certificate validated |
| `MTLS_FAILURE` | warning | mTLS certificate invalid |
| `MTLS_CN_MISMATCH` | warning | Certificate CN != JWT subject |

### Authorization Events (RBAC)

| Event Type | Severity | Description |
|------------|----------|-------------|
| `AUTHZ_SUCCESS` | info | Permission granted |
| `AUTHZ_FAILURE` | warning | Permission denied (403) |

### Security Events

| Event Type | Severity | Description |
|------------|----------|-------------|
| `RATE_LIMIT_EXCEEDED` | warning | Rate limit triggered |

### Data Events

| Event Type | Severity | Description |
|------------|----------|-------------|
| `TELEMETRY_CREATED` | info | New telemetry event ingested |
| `TELEMETRY_DUPLICATE` | info | Duplicate event (idempotent) |
| `TELEMETRY_CONFLICT` | warning | Same ID, different payload |
| `CONTACTS_ACCESSED` | info | Contacts data retrieved |


### OAuth Events

| Event Type | Severity | Description |
|------------|----------|-------------|
| `OAUTH_INITIATED` | info | OAuth flow started |
| `OAUTH_COMPLETED` | info | OAuth tokens received |
| `OAUTH_REVOKED` | info | OAuth tokens revoked |
| `OAUTH_FAILURE` | warning | OAuth flow failed |

### System Events

| Event Type | Severity | Description |
|------------|----------|-------------|
| `SERVICE_STARTED` | info | Service started |
| `SERVICE_STOPPED` | info | Service stopped |
| `CONFIG_CHANGED` | warning | Configuration modified |


## Compliance

When doing security by design good, security, and audit logging directly meet compliance requirements.

### SOC 2 Type II



| Control | Audit Support |
|---------|---------------|
| CC6.1 | Authentication events logged |
| CC6.2 | Authorization failures logged |
| CC7.2 | Security incidents traceable |
| CC8.1 | Changes logged (CONFIG_CHANGED) |

### GDPR

| Requirement | Implementation |
|-------------|----------------|
| Data Access Tracking | CONTACTS_ACCESSED events |
| No PII in Logs | Only IDs, never names/emails |
| Right to Access | trace_id enables request tracing |

### Log Retention

Recommended retention policies:

| Environment | Retention | Rationale |
|-------------|-----------|-----------|
| Development | 7 days | Debugging |
| Staging | 30 days | Testing |
| Production | 90 days | Compliance (SOC 2) |
| Archive | 1 year | Legal/regulatory |


## Best Practices for Security By Desing


- Always include trace_id for request correlation
- Use convenience methods when available
- Log at appropriate severity levels
- Keep details minimal but useful
- Use a standard logging format. E.g. `syslog` format. 
- Never ever try to reinvent good mechanism for audit and security logging. Just reuse good principles and implementation from secure operating systems, like OpenBSD.


Mind Never ever:

- Log tokens, passwords, or secrets
- Log PII (names, emails, phone numbers)
- Log request/response bodies
- Log sensitive business data

Key Security Considerations:

1. **Access Control**: Restrict audit log access to security team
2. **Integrity**: Use append-only storage if possible
3. **Encryption**: Encrypt audit logs at rest
4. **Backup**: Maintain secure backups of audit logs
5. **Log rotation**

