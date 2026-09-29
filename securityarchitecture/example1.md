---
title: Security Architecture Example
short_title: Security Architecture Example
---

Example: SkyLink Security Architecture

> **Data Flow Diagrams and Security Controls for the SkyLink Connected Aircraft Platform**



## Document Information

| Attribute | Value |
|-----------|-------|
| **Document Owner** | SkyLink Platform Team |
| **Classification** | Internal |
| **Document Version** | 1.0 |
| **Last Review Date** | December 2025 |
| **Next Review Date** | June 2026 |
| **Related Documents** | tbd|

---


## Overview

### Purpose

This document describes the security architecture of the SkyLink platform using Data Flow Diagrams (DFD) to illustrate:

- How data moves through the system
- Where trust boundaries exist
- What security controls are applied at each boundary
- How data is classified and protected

### Scope

This architecture covers:

- API Gateway (authentication, routing, rate limiting)
- Telemetry Service (aircraft data ingestion)
- Weather Service (external API integration)
- Contacts Service (Google OAuth integration)
- PostgreSQL Database (persistent storage)
- External integrations (WeatherAPI, Google People API)

### Audience

- Security Engineers (threat modeling, penetration testing)
- Developers (secure implementation guidance)
- Auditors (compliance verification)
- Operations (deployment and monitoring)

---

## System Context (Level 0)

### Context Diagram

:::{image} ../images/example_contextdiagram.png
:alt: Example context diagram
:align: center
:::


### External Actors

| Actor | Description | Authentication | Data Exchanged |
|-------|-------------|----------------|----------------|
| **Aircraft Systems** | Onboard avionics and telemetry systems | mTLS + JWT RS256 | Telemetry data, weather requests |
| **WeatherAPI** | Third-party weather data provider | API Key (outbound) | Weather conditions, air quality |
| **Google People API** | Google contact synchronization | OAuth 2.0 (outbound) | Contact names, emails |
| **Admin Operators** | Platform administrators | TBD (future) | Configuration, monitoring |

---

## Trust Boundaries

### Boundary Definitions

| ID | Boundary | From | To | Risk Level |
|----|----------|------|-----|------------|
| **TB1** | Internet → Gateway | Untrusted (Internet) | DMZ (Gateway) | **CRITICAL** |
| **TB2** | Gateway → Services | DMZ | Internal Services | **MEDIUM** |
| **TB3** | Services → External APIs | Internal | External (Vendors) | **HIGH** |
| **TB4** | Services → Database | Internal | Data Layer | **HIGH** |

### Security Controls per Boundary

#### TB1: Internet → Gateway (CRITICAL)

TRUST BOUNDARY: Internet → API Gateway


| Threat              | Control                              |
|:--------------------|:-------------------------------------|
| Spoofing            | → mTLS (X.509 client certs)          |
| Man-in-the-Middle   | → TLS 1.2+ with strong ciphers       |
| Replay attacks      | → JWT expiry (15 min)                |
| DDoS / Flooding     | → Rate limiting (60 req/min)         |
| Injection           | → Pydantic validation (`extra=forbid`)|
| Information disclosure | → Security headers (OWASP)        |
| Large payloads      | → 64 KB request limit                |

AUTHENTICATION FLOW:                                        
1. TLS handshake (mutual authentication)                   
2. Client certificate validation (CA-signed)               
3. CN extraction from certificate                          
4. JWT token issuance (sub = CN)                          
5. Cross-validation on subsequent requests (CN == sub)    


#### TB2: Gateway → Services (MEDIUM)

TRUST BOUNDARY 2 : Gateway → Internal Services                


ASSUMPTION:
- Gateway has validated all requests              

CONTROLS:                                                   
- Docker bridge network isolation                          
- Services not exposed to Internet                         
- Internal DNS resolution only                             
- Request forwarding via httpx (async)                     

DATA FLOW:                                                  
- Gateway ──[HTTP/JSON]──► Telemetry/Weather/Contacts        

NOTE:
- No authentication between internal services(trusted internal network model)                          


#### TB3: Services → External APIs (HIGH)

:::{table}
:widths: auto
:align: center

| Section | Connection / Item | Details / Configuration |
| :--- | :--- | :--- |
| **Outbound Connections** | Weather Service ──[HTTPS]──► WeatherAPI | • API key in request header<br>• Geohash/coordinates (no raw GPS)<br>• Demo mode fallback (fixtures) |
| | Contacts Service ──[HTTPS]──► Google People API | • OAuth 2.0 bearer token<br>• Minimal scope (contacts.readonly)<br>• Token refresh handling |
| **Controls** | Security & Network | • HTTPS enforced (TLS 1.2+)<br>• API keys not logged<br>• Response validation<br>• Timeout configuration |
:::


#### TB4: Services → Database (HIGH)

:::{table}
:widths: auto
:align: center

| Section | Item | Details / Configuration |
| :--- | :--- | :--- |
| **Connection** | Contacts Service ──[TCP:5432]──► PostgreSQL | • Direct TCP connection |
| **Controls** | Security & Architecture | • Network isolation (Docker bridge)<br>• Credential-based authentication<br>• Connection pooling (SQLAlchemy)<br>• Parameterized queries (no SQL injection) |
| **Data Stored** | Stored Records | • OAuth tokens (AES-256-GCM encrypted)<br>• User identifiers<br>• Token expiration metadata |
| **Data Protection** | Privacy & Encryption | • Encryption at rest (application-level)<br>• No plaintext secrets in database |
:::

---

## Data Flow Diagrams (Level 1)

### Flow 1: Aircraft Authentication


:::{image} ../images/example1_dataflow.png
:alt: Data Flow diagram
:align: center
:::


**Security Controls Applied**:
- [x] mTLS handshake (mutual authentication)
- [x] Certificate validation (CA-signed, not expired)
- [x] CN extraction and binding
- [x] JWT RS256 signing (2048-bit RSA)
- [x] Short token expiry (15 minutes)

### Flow 2: Telemetry Ingestion


:::{image} ../images/example1_dataflow2.png
:alt: Data Flow2 diagram
:align: center
:::


**HTTP Response Codes**:
| Code | Meaning | Scenario |
|------|---------|----------|
| 201 | Created | New event stored |
| 200 | OK | Duplicate event (idempotent) |
| 409 | Conflict | Same event_id, different payload |
| 400 | Bad Request | Validation error |
| 401 | Unauthorized | Invalid/expired JWT |
| 403 | Forbidden | CN ≠ JWT.sub |
| 429 | Too Many Requests | Rate limit exceeded |

### Flow 3: Weather Query


:::{image} ../images/example1_dataflow3.png
:alt: Data Flow3 diagram
:align: center
:::

**Data Protection**:
- GPS coordinates passed as query parameters (not logged)
- API key never exposed to clients
- Demo mode prevents external API calls during testing

### Flow 4: Contacts OAuth


:::{image} ../images/example1_oathflow.png
:alt: Data Flow OATH
:align: center
:::


**OAuth Security**:
- Minimal scope: `contacts.readonly`
- State parameter for CSRF protection
- Tokens encrypted before storage (AES-256-GCM)
- Tokens never logged

---

## Security Controls by Layer

### Control Matrix

| Layer | Control | Implementation |
|-------|---------|----------------|
| **Transport** | TLS 1.2+ | mTLS with strong ciphers |
| **Transport** | Certificate validation | X.509, CA-signed |
| **Network** | Service isolation | Docker bridge network |
| **Application** | Authentication | JWT RS256 |
| **Application** | Authorization | RBAC (5 roles, 7 permissions) |
| **Application** | Cross-validation | CN == JWT sub |
| **Application** | Rate limiting | 60 req/min per identity |
| **Application** | Input validation | Pydantic extra=forbid |
| **Application** | Idempotency | Unique constraint |
| **Application** | Security headers | OWASP set |
| **Data** | PII minimization | GPS rounding (4 dec) |
| **Data** | Token encryption | AES-256-GCM |
| **Data** | No PII in logs | Structured logging |
| **Container** | Non-root user | UID 1000 |
| **Supply Chain** | Dependency scanning | pip-audit, Trivy |
| **Supply Chain** | Image signing | Cosign (keyless) |
| **Supply Chain** | SBOM | CycloneDX |
| **Supply Chain** | Secret detection | Gitleaks |


### Defense in Depth Visualization


:::{image} ../images/example_defense_in_depth.png
:alt: Defense in Depth
:align: center
:::


---

## Data Classification

### Classification Matrix

| Data Type | Classification | At Rest | In Transit | In Logs | Retention |
|-----------|----------------|---------|------------|---------|-----------|
| Aircraft UUID | Internal | Plaintext | TLS | Allowed | Unlimited |
| Telemetry (speed, alt) | Confidential | Plaintext | TLS | trace_id only | 90 days |
| GPS Position | **PII** | Rounded (4 dec) | TLS | **Never** | 90 days |
| Google Contacts | **PII** | Not stored | TLS | **Never** | Session only |
| OAuth Tokens | **Restricted** | AES-256-GCM | TLS | **Never** | Until revoked |
| JWT Tokens | Restricted | N/A (memory) | TLS | **Never** | 15 min |
| mTLS Certificates | Restricted | File (0600) | TLS | **Never** | 1 year |
| API Keys | **Restricted** | Env var | TLS | **Never** | Until rotated |

### Data Handling Rules


| Data Classification | Examples | Logging | Storage | Transmission | Other Rules |
|---|---|---|---|---|---|
| **INTERNAL DATA** | Aircraft UUID, trace_id | ✓ Can be logged | ✓ Can be stored plaintext | ✓ Can be transmitted | — |
| **CONFIDENTIAL DATA** | Telemetry | ✗ Cannot be logged (only trace_id) | ✓ Can be stored | ✓ Must be encrypted in transit (TLS) | — |
| **PII DATA** | GPS, Contacts | ✗ Never logged | ⚠ GPS must be rounded (4 decimals = ~11m accuracy)<br>⚠ Contacts are read-only, not persisted | ✓ Must be encrypted in transit (TLS) | — |
| **RESTRICTED DATA** | Tokens, Keys, Certs | ✗ Never logged | ✓ Must be encrypted at rest (AES-256-GCM) | ✓ Must be encrypted in transit (TLS) | ✗ Never in source code<br>✓ Environment variables or secrets manager |


---

## Attack Surface Analysis

### Attack Surface Map

| Surface | Exposure | Risk Level | Attack Vectors | Mitigations |
|---------|----------|------------|----------------|-------------|
| **API Gateway :8000** | Internet | **CRITICAL** | DDoS, injection, auth bypass | mTLS, JWT, rate limit, validation |
| **Internal Services** | Docker network | **MEDIUM** | Lateral movement | Network isolation, no auth needed |
| **PostgreSQL :5432** | Docker network | **HIGH** | SQL injection, data theft | Credentials, parameterized queries |
| **Container Registry** | Internet | **HIGH** | Image tampering | Cosign signing, Trivy scanning |
| **CI/CD Pipeline** | GitHub/GitLab | **HIGH** | Secret theft, code injection | Gitleaks, protected branches |
| **External APIs** | Outbound | **MEDIUM** | Data leakage | HTTPS, minimal data sharing |

### Exposed Endpoints

| Endpoint | Authentication | Rate Limited | Input Validation | Risk |
|----------|----------------|--------------|------------------|------|
| `GET /health` | None | No | N/A | LOW |
| `GET /metrics` | None | No | N/A | LOW |
| `POST /auth/token` | mTLS | Yes | Pydantic | MEDIUM |
| `POST /telemetry/ingest` | mTLS + JWT | Yes | Pydantic strict | HIGH |
| `GET /weather/current` | JWT | Yes | Query params | MEDIUM |
| `GET /contacts/` | JWT | Yes | Query params | MEDIUM |


## Cryptographic Inventory

### Algorithms and Key Sizes

| Purpose | Algorithm | Key Size | Rotation Period | Storage |
|---------|-----------|----------|-----------------|---------|
| JWT Signing | RS256 (RSA-SHA256) | 2048-bit | 90 days | Env var (PRIVATE_KEY_PEM) |
| JWT Verification | RS256 | 2048-bit | 90 days | Env var (PUBLIC_KEY_PEM) |
| Token Encryption | AES-256-GCM | 256-bit | 90 days | Env var (ENCRYPTION_KEY) |
| mTLS CA | RSA/X.509 | 2048-bit | 1 year | File (certs/ca/ca.crt) |
| mTLS Server | RSA/X.509 | 2048-bit | 1 year | File (certs/server/) |
| mTLS Client | RSA/X.509 | 2048-bit | 1 year | File (certs/clients/) |
| Image Signing | ECDSA (Sigstore) | P-256 | Keyless (per-build) | GitHub OIDC |


+++{"no-pdf": true}

### Key Management


:::{image} ../images/example_keymanagement.png
:alt: Key Management
:align: center
:::

+++ 
% End of part that will not be shown in PDF - figure will not fit correctly! 



## Network Security

### Network Topology


:::{image} ../images/example_networktopology.png
:alt: Network Topology
:align: center
:::


### Network Policies

| Service | Allowed Inbound | Allowed Outbound |
|---------|-----------------|------------------|
| gateway | Internet:8000 | telemetry, weather, contacts |
| telemetry | gateway | None |
| weather | gateway | WeatherAPI (HTTPS) |
| contacts | gateway | Google APIs (HTTPS), db:5432 |
| db | contacts | None |

### Kubernetes Network Policies

For production Kubernetes deployments, network policies enforce zero-trust networking:

```yaml
# Default: deny all traffic
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: skylink-default-deny
spec:
  podSelector: {}
  policyTypes:
    - Ingress
    - Egress
```

**Kubernetes Network Policy Matrix**:

| Policy | From | To | Ports | Purpose |
|--------|------|-----|-------|---------|
| `gateway-ingress` | ingress-nginx | gateway | 8000 | External access |
| `gateway-egress` | gateway | internal services | 8001-8003 | Service routing |
| `internal-ingress` | gateway | telemetry/weather/contacts | 8001-8003 | Internal traffic |
| `internal-egress` | internal services | external APIs | 443 | API calls |
| `prometheus-scrape` | monitoring namespace | all pods | 8000-8003 | Metrics collection |



### Security Headers

```http
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
Cache-Control: no-store, no-cache, must-revalidate, max-age=0
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Embedder-Policy: require-corp
Referrer-Policy: no-referrer
Permissions-Policy: geolocation=(), microphone=(), camera=()
```

### JWT Claims

```json
{
  "sub": "aircraft_id (from mTLS CN)",
  "aud": "skylink",
  "iat": 1734600000,
  "exp": 1734600900,
  "role": "aircraft_standard"
}
```

### RBAC Roles

| Role | Description | Key Permissions |
|------|-------------|-----------------|
| `aircraft_standard` | Default aircraft | weather:read, telemetry:write |
| `aircraft_premium` | Premium aircraft | + contacts:read |
| `ground_control` | Ground control | weather:read, contacts:read, telemetry:read |
| `maintenance` | Maintenance | telemetry:read/write, config:read |
| `admin` | Administrator | All permissions |



### Rate Limits

| Scope | Limit | Window |
|-------|-------|--------|
| Per aircraft_id | 60 requests | 1 minute |
| Global | 10 requests | 1 second |

---

