---
title: Example Threat Model with STRIDE
short_title:  STRIDE Example
---

An example of a **STRIDE-based threat analysis** for the SkyLink Connected Aircraft Platform.

---

## Document Information

| Attribute | Value |
|-----------|-------|
| **Service Owner** | SkyLink Platform Team |
| **Data Classification** | Confidential (PII, telemetry, credentials) |
| **Highest Risk Impact** | MAXIMUM |
| **Document Version** | 1.0 |
| **Last Review Date** | December 2025 |
| **Next Review Date** | June 2026 |

---

+++{"no-pdf": true}

## Table of Contents

:::{toc}
:context: page
:::

+++
%PDF conversion issue towards PDF, so leave ToC for this page out of the PDF version.

## Service Description

### Overview

SkyLink is a connected aircraft services platform providing real-time telemetry ingestion, weather data, and contact synchronization for commercial aviation. The platform follows Security by Design principles with multi-layer authentication, defense in depth, and privacy by design.

### Functional Decomposition

The service provides the following capabilities:

- **Telemetry Ingestion**: Real-time reception of aircraft operational data (speed, altitude, fuel, GPS position)
- **Weather Services**: Geolocation-based weather and air quality data using external APIs
- **Contact Synchronization**: Google Contacts retrieval via OAuth 2.0 for crew communication

### Technical Architecture


:::{image} ../images/ti_example.png
:alt: TI Example
:align: center
:::


### Component Responsibilities

| Component | Responsibility | Security Controls |
|-----------|---------------|-------------------|
| **API Gateway** | Authentication, authorization, routing, rate limiting | mTLS, JWT RS256, RBAC, OWASP headers |
| **Telemetry Service** | Aircraft data ingestion | Idempotency, input validation |
| **Weather Service** | External API proxy | Demo mode fallback |
| **Contacts Service** | OAuth flow, Google API | Token encryption (AES-256-GCM) |
| **PostgreSQL** | Data persistence | Network isolation, credentials |

---

## Data Dictionary

List of data stored or in transit within the service.

| Data Type | Classification | Storage | Notes |
|-----------|----------------|---------|-------|
| **Aircraft UUID** | Internal | Memory/Transit | Technical identifier |
| **Telemetry Data** (speed, altitude, fuel, engine temp) | Confidential | Memory | Reveals flight patterns and aircraft state |
| **GPS Position** | Confidential (PII) | Memory | Sensitive location data - **rounded to 4 decimals (~11m)** to minimize precision |
| **Google Contacts** | Confidential (PII) | Transit only | Read-only via Google People API, not persisted |
| **Google OAuth Tokens** | Restricted | PostgreSQL | **AES-256-GCM encrypted** at rest |
| **JWT Tokens** | Restricted | Transit | RS256 signed, 15-minute expiry |
| **mTLS Certificates** | Restricted | Filesystem | X.509, CA-signed, stored securely |
| **Logs** | Restricted | Filesystem/STDOUT | **No PII** - only trace_id for correlation |
| **Network Metadata** (IP, User-Agent) | Internal | Memory | Pseudonymized if exported for analysis |

### Data Flow Classification

| Flow | Encryption | Authentication | Authorization |
|------|------------|----------------|---------------|
| Aircraft → Gateway | mTLS (TLS 1.2+) | X.509 certificate | JWT claims |
| Gateway → Services | Internal network | None (trusted) | N/A |
| Services → External APIs | HTTPS | API keys / OAuth | Scope-limited |
| Services → Database | Internal network | Credentials | Role-based |

---

## Threat Actors

### External Threat Actors

| Actor | Motivation | Capability | Target |
|-------|------------|------------|--------|
| **Nation-State** | Espionage, disruption | HIGH | Flight data, infrastructure |
| **Cybercriminals** | Financial gain | MEDIUM | Credentials, PII |
| **Hacktivists** | Ideology, publicity | LOW-MEDIUM | Service availability |
| **Competitors** | Industrial espionage | MEDIUM | Proprietary data |

### Internal Threat Actors

| Actor | Motivation | Capability | Target |
|-------|------------|------------|--------|
| **Malicious Insider** | Financial, revenge | HIGH | All data, credentials |
| **Compromised Account** | Exploited by external | VARIES | Depends on privileges |
| **Negligent Employee** | None (accidental) | LOW | Misconfigurations |

### Supply Chain Threats

| Vector | Risk | Mitigation |
|--------|------|------------|
| **Compromised Dependencies** | Backdoors, vulnerabilities | SCA (pip-audit), SBOM |
| **Container Image Tampering** | Malicious code injection | Image signing (Cosign) |
| **CI/CD Pipeline Compromise** | Unauthorized deployments | Protected branches, secrets management |

---

## STRIDE Analysis

### Spoofing (Identity)


| ID | Threat | Impact | Likelihood | Mitigation | Status |
|----|--------|--------|------------|------------|--------|
| S1 | Aircraft identity spoofing | MAXIMUM | Medium | mTLS with X.509 certificates, CN validation | ✅ |
| S2 | User identity spoofing | HIGH | Medium | JWT RS256 + mTLS cross-validation (CN == sub) | ✅ |
| S3 | Compromised Certificate Authority | MAXIMUM | Low | CA isolation, HSM storage (recommended) | ⚠️ |
| S4 | Stolen aircraft private key | HIGH | Low | HSM storage on aircraft, certificate rotation | 📝 |
| S5 | JWT token theft | HIGH | Medium | Short expiry (15 min), HTTPS only | ✅ |

**Symbol legend:**
- ✅ Implemented
- ⚠️ Partial
- 📝 Documented


### Tampering (Integrity)



| ID | Threat | Impact | Likelihood | Mitigation | Status |
|----|--------|--------|------------|------------|--------|
| T1 | Telemetry data modification in transit | MAXIMUM | Medium | mTLS integrity, TLS 1.2+ | ✅ |
| T2 | JWT token modification | HIGH | Low | RS256 signature verification | ✅ |
| T3 | Database tampering | HIGH | Low | Access controls, network isolation | ✅ |
| T4 | Supply chain attack (malicious dependency) | MAXIMUM | Medium | SBOM, SCA, image signing, Gitleaks | ✅ |
| T5 | Log tampering | MEDIUM | Low | Centralized logging (recommended) | ⚠️ |
| T6 | Configuration tampering | HIGH | Low | Environment variables, protected branches | ✅ |

**Symbol legend:**
- ✅ Implemented
- ⚠️ Partial


### Repudiation (Non-Repudiation)


| ID | Threat | Impact | Likelihood | Mitigation | Status |
|----|--------|--------|------------|------------|--------|
| R1 | Denied authentication attempts | MEDIUM | Medium | Audit logging | ❌ |
| R2 | Denied data access | MEDIUM | Medium | Audit logging | ❌ |
| R3 | Deleted or modified logs | HIGH | Low | Immutable log storage | ❌ |
| R4 | Timestamp manipulation | MEDIUM | Low | Server-side timestamps | ✅ |

**Symbol legend:**
- ✅ Implemented
- ❌ Not Implemented


### Information Disclosure (Confidentiality)

| ID | Threat | Impact | Likelihood | Mitigation | Status |
|----|--------|--------|------------|------------|--------|
| I1 | OAuth token leak (logs, CI, exposed variables) | MAXIMUM | Medium | AES-256-GCM encryption, no logging | ✅ |
| I2 | PII in logs | HIGH | Medium | Structured logging, PII filtering | ✅ |
| I3 | GPS precision leak (tracking) | HIGH | Medium | 4-decimal rounding (~11m accuracy) | ✅ |
| I4 | Verbose error messages | MEDIUM | Medium | Generic error responses | ✅ |
| I5 | Secrets in repository | MAXIMUM | Low | Gitleaks scanning, .gitignore | ✅ |
| I6 | Excessive OAuth scope | HIGH | Low | Minimal scope (contacts.readonly) | ✅ |
| I7 | External API data leak (WeatherAPI) | MEDIUM | Low | Geohash/rounding for location | ✅ |

**Symbol legend:**
- ✅ Implemented


### Denial of Service (Availability)


| ID | Threat | Impact | Likelihood | Mitigation | Status |
|----|--------|--------|------------|------------|--------|
| D1 | API flood / DDoS | HIGH | High | Rate limiting (60/min per identity) | ✅ |
| D2 | Large payload attack | MEDIUM | Medium | 64KB payload limit | ✅ |
| D3 | Telemetry storm (fleet event flood) | MEDIUM | Medium | Idempotency, rate limiting | ✅ |
| D4 | External service outage (Weather/Google) | MEDIUM | Medium | Demo mode fallback | ✅ |
| D5 | Database exhaustion | MEDIUM | Low | Connection pooling, limits | ⚠️ |
| D6 | CI/CD pipeline failure | HIGH | Medium | Rollback capability | ⚠️ |

**Symbol legend:**
- ✅ Implemented
- ⚠️ Partial


### Elevation of Privilege (Authorization)


| ID | Threat | Impact | Likelihood | Mitigation | Status |
|----|--------|--------|------------|------------|--------|
| E1 | JWT claim manipulation | HIGH | Low | RS256 signature verification | ✅ |
| E2 | Cross-aircraft data access | HIGH | Medium | JWT subject validation, aircraft_id binding | ✅ |
| E3 | Container escape | MAXIMUM | Low | Non-root containers (UID 1000) | ✅ |
| E4 | RBAC bypass | HIGH | Medium | N/A - RBAC not implemented | ❌ |
| E5 | Service-to-service impersonation | MEDIUM | Low | Internal network isolation | ✅ |

**Symbol legend:**
- ✅ Implemented
- ❌ Not Implemented


## Risk Matrix

### Risk Calculation

**Risk = Impact × Likelihood**

|                  | Low Impact | Medium  | High     | Maximum  |
|------------------|------------|---------|----------|----------|
| **Likely**       | MEDIUM     | HIGH    | CRITICAL | CRITICAL |
| **Possible**     | LOW        | MEDIUM  | HIGH     | CRITICAL |
| **Unlikely**     | LOW        | LOW     | MEDIUM   | HIGH     |
| **Rare**         | ACCEPT     | LOW     | LOW      | MEDIUM   |


### Current Risk Profile

| Risk Level | Count | Examples |
|------------|-------|----------|
| **CRITICAL** | 0 | - |
| **HIGH** | 3 | Audit logging gaps, RBAC missing, CA compromise |
| **MEDIUM** | 5 | Log tampering, external service outage |
| **LOW** | 8 | Various mitigated threats |
| **ACCEPTED** | 2 | Rare/low impact scenarios |

---

## Threat Scenarios

Detailed threat scenarios with business impact analysis.

### Scenario 1: Data Leak via OAuth Token Exposure

| Attribute | Value |
|-----------|-------|
| **Impact** | MAXIMUM (Reputation), MEDIUM (Productivity), € (Financial) |
| **CIA** | Confidentiality |
| **Attack Vector** | Tokens exposed in CI logs, GitLab variables, or verbose logging |
| **Consequence** | Extraction of user contacts, potential GDPR violation |

**Mitigations Implemented**:
- AES-256-GCM encryption for stored tokens
- No token logging (structured logging without PII)
- GitLab/GitHub secrets management
- Gitleaks scanning in CI

### Scenario 2: Aircraft Spoofing via mTLS Bypass

| Attribute | Value |
|-----------|-------|
| **Impact** | HIGH (Reputation), HIGH (Productivity), €€ (Financial) |
| **CIA** | Integrity |
| **Attack Vector** | Weak mTLS validation, compromised CA, or stolen private key |
| **Consequence** | Falsified telemetry injection, erroneous alerts, costly investigations |

**Mitigations Implemented**:
- mTLS with X.509 certificates
- CN ↔ JWT subject cross-validation
- TLS 1.2+ with strong cipher suites
- Certificate generation scripts for proper PKI

### Scenario 3: Supply Chain Attack

| Attribute | Value |
|-----------|-------|
| **Impact** | MAXIMUM (Reputation), MAXIMUM (Productivity), €€€ (Financial) |
| **CIA** | Integrity |
| **Attack Vector** | Compromised PyPI dependency, malicious container image |
| **Consequence** | Backdoor in production, potential remote aircraft impact |

**Mitigations Implemented**:
- pip-audit for dependency scanning
- Trivy for container scanning
- Cosign for image signing
- SBOM generation (CycloneDX)
- Gitleaks for secret detection

### Scenario 4: DDoS / API Flood

| Attribute | Value |
|-----------|-------|
| **Impact** | HIGH (Reputation), HIGH (Productivity), € (Financial) |
| **CIA** | Availability |
| **Attack Vector** | Unprotected gateway, missing rate limits |
| **Consequence** | Service unavailability during flight operations |

**Mitigations Implemented**:
- Rate limiting: 60 requests/minute per aircraft_id
- Global rate limit: 10 requests/second
- Payload size limit: 64KB
- Prometheus metrics for monitoring

### Scenario 5: Replay Attack on Telemetry

| Attribute | Value |
|-----------|-------|
| **Impact** | MEDIUM (Reputation), LOW (Productivity), € (Financial) |
| **CIA** | Integrity |
| **Attack Vector** | Replay of captured telemetry events |
| **Consequence** | Duplicate events, time series pollution |

**Mitigations Implemented**:
- Idempotency via unique (aircraft_id, event_id) constraint
- Response codes: 201 (new), 200 (duplicate), 409 (conflict)
- Server-side timestamps

---

## Recommendations Summary

### Implemented Controls


| Priority | Recommendation | Status |
|----------|---------------|--------|
| **MAXIMUM** | mTLS for aircraft authentication | ✅ |
| **MAXIMUM** | OAuth with least privilege (contacts.readonly) | ✅ |
| **HIGH** | Data minimization (GPS rounding, no contact persistence) | ✅ |
| **HIGH** | JWT RS256 + rate limiting | ✅ |
| **HIGH** | Logs without PII, tracing, metrics | ✅ |
| **HIGH** | Supply chain security (SBOM, SCA, image signing) | ✅ |

**Symbol legend:**
- ✅ Implemented



### Pending Controls


| Priority | Recommendation | Status | Planned |
|----------|---------------|--------|---------|
| **MAXIMUM** | Secrets in KMS/Vault | ⚠️ | Phase 4 |
| **HIGH** | Audit logging | ❌ | Phase 3 |
| **HIGH** | Security monitoring & alerting | ❌ | Phase 3 |
| **MEDIUM** | RBAC authorization | ❌ | Phase 4 |
| **MEDIUM** | Key rotation automation | ❌ | Phase 3 |

**Symbol legend:**
- ⚠️ Partial
- ❌ Not Implemented



## Gap Analysis

### Current State vs Target State

| Control Area | Current | Target | Gap |
|--------------|---------|--------|-----|
| **Authentication** | mTLS + JWT RS256 | Same | None |
| **Authorization** | Identity-based only | RBAC | Missing roles/permissions |
| **Audit Logging** | None | Full audit trail | Not implemented |
| **Monitoring** | Basic Prometheus | Grafana dashboards + alerts | Dashboards missing |
| **Key Management** | Manual | Automated rotation | Scripts needed |
| **Secrets Management** | Environment variables | KMS/Vault | Integration needed |
| **Incident Response** | None | Documented playbook | Not documented |

### Remediation Roadmap

| Phase | Focus | Timeline |
|-------|-------|----------|
| **Phase 2** | Threat Model + Security Architecture | Current |
| **Phase 3** | Monitoring, Key Management, Audit Logging | Next |
| **Phase 4** | RBAC, Security Tests, Kubernetes | Following |
| **Phase 5** | Compliance, Vault Integration | Future |

---

## Review History

| Date | Version | Reviewer | Changes |
|------|---------|----------|---------|
| December 2025 | 1.0 | Initial | Initial threat model based on STRIDE analysis |

---

## Appendix A: Security Controls Matrix

| Control | STRIDE Coverage | Implementation |
|---------|-----------------|----------------|
| mTLS | S, T, I | See config file (give a link as evidence) |
| JWT RS256 | S, T, E | See config file (give a link as evidence) |
| Rate Limiting | D | See config file or code (give a link as evidence)` |
| Input Validation | T, E | See code file and design (give link as evidence) |
| Security Headers | I | See code file and design (give link as evidence) |
| Token Encryption | I | See code file and design (give link as evidence)|
| GPS Rounding | I | See code file and design (give link as evidence) |
| Idempotency | T, R | Telemetry service |
| Container Security | E | Dockerfile (non-root) |
| Supply Chain | T | CI/CD pipeline - see config in repo |

---

