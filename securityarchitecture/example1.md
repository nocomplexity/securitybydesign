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

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           EXTERNAL ACTORS                                │
│                                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐ │
│  │   Aircraft   │  │  WeatherAPI  │  │    Google    │  │    Admin     │ │
│  │   Systems    │  │   (Vendor)   │  │  People API  │  │  Operators   │ │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘ │
│         │                 │                 │                 │          │
└─────────┼─────────────────┼─────────────────┼─────────────────┼──────────┘
          │                 │                 │                 │
          │ mTLS + JWT      │ HTTPS           │ OAuth 2.0       │ TBD
          │ (TLS 1.2+)      │ (API Key)       │ (HTTPS)         │
          │                 │                 │                 │
          ▼                 ▼                 ▼                 ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                          │
│  ══════════════════════ TRUST BOUNDARY 1 ══════════════════════════════ │
│                                                                          │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                      API GATEWAY (:8000)                           │  │
│  │                                                                    │  │
│  │  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────────┐  │  │
│  │  │   mTLS     │ │  JWT RS256 │ │    Rate    │ │    Security    │  │  │
│  │  │ Validation │ │    Auth    │ │  Limiting  │ │    Headers     │  │  │
│  │  └────────────┘ └────────────┘ └────────────┘ └────────────────┘  │  │
│  │  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────────┐  │  │
│  │  │  Payload   │ │  Request   │ │ Prometheus │ │   Structured   │  │  │
│  │  │   Limit    │ │  Routing   │ │  Metrics   │ │    Logging     │  │  │
│  │  └────────────┘ └────────────┘ └────────────┘ └────────────────┘  │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│                                    │                                     │
│  ══════════════════════ TRUST BOUNDARY 2 ══════════════════════════════ │
│                                    │                                     │
│         ┌──────────────────────────┼──────────────────────────┐         │
│         │                          │                          │         │
│         ▼                          ▼                          ▼         │
│  ┌─────────────┐           ┌─────────────┐           ┌─────────────┐   │
│  │  TELEMETRY  │           │   WEATHER   │           │  CONTACTS   │   │
│  │    :8001    │           │    :8002    │           │    :8003    │   │
│  ├─────────────┤           ├─────────────┤           ├─────────────┤   │
│  │ • Idempotency│          │ • Demo mode │           │ • OAuth 2.0 │   │
│  │ • GPS round  │          │ • Fixtures  │           │ • Encryption│   │
│  │ • Validation │          │ • Cache     │           │ • CRUD ops  │   │
│  └─────────────┘           └──────┬──────┘           └──────┬──────┘   │
│                                   │                          │          │
│  ═══════════════════ TRUST BOUNDARY 3 ═══════════════════════│══════   │
│                                   │                          │          │
│                                   ▼                          │          │
│                          ┌─────────────┐                     │          │
│                          │ WeatherAPI  │                     │          │
│                          │  (External) │                     │          │
│                          └─────────────┘                     │          │
│                                                              │          │
│  ═══════════════════ TRUST BOUNDARY 4 ═══════════════════════│══════   │
│                                                              │          │
│                                                              ▼          │
│                                                     ┌─────────────┐    │
│                                                     │ PostgreSQL  │    │
│                                                     │    :5432    │    │
│                                                     ├─────────────┤    │
│                                                     │ • OAuth tok │    │
│                                                     │ • Encrypted │    │
│                                                     └─────────────┘    │
│                                                                          │
│                           SKYLINK PLATFORM                               │
└─────────────────────────────────────────────────────────────────────────┘
```

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

```
┌─────────────────────────────────────────────────────────────┐
│                    TRUST BOUNDARY 1                          │
│               Internet → API Gateway                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  THREATS                    CONTROLS                         │
│  ─────────                  ────────                         │
│  • Spoofing          ──►   mTLS (X.509 client certs)        │
│  • Man-in-Middle     ──►   TLS 1.2+ with strong ciphers     │
│  • Replay attacks    ──►   JWT expiry (15 min)              │
│  • DDoS/Flooding     ──►   Rate limiting (60 req/min)       │
│  • Injection         ──►   Pydantic validation (extra=forbid)│
│  • Info disclosure   ──►   Security headers (OWASP)         │
│  • Large payloads    ──►   64KB request limit               │
│                                                              │
│  AUTHENTICATION FLOW:                                        │
│  1. TLS handshake (mutual authentication)                   │
│  2. Client certificate validation (CA-signed)               │
│  3. CN extraction from certificate                          │
│  4. JWT token issuance (sub = CN)                          │
│  5. Cross-validation on subsequent requests (CN == sub)     │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

#### TB2: Gateway → Services (MEDIUM)

```
┌─────────────────────────────────────────────────────────────┐
│                    TRUST BOUNDARY 2                          │
│               Gateway → Internal Services                    │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ASSUMPTION: Gateway has validated all requests              │
│                                                              │
│  CONTROLS:                                                   │
│  • Docker bridge network isolation                          │
│  • Services not exposed to Internet                         │
│  • Internal DNS resolution only                             │
│  • Request forwarding via httpx (async)                     │
│                                                              │
│  DATA FLOW:                                                  │
│  Gateway ──[HTTP/JSON]──► Telemetry/Weather/Contacts        │
│                                                              │
│  NOTE: No authentication between internal services          │
│  (trusted internal network model)                           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

#### TB3: Services → External APIs (HIGH)

```
┌─────────────────────────────────────────────────────────────┐
│                    TRUST BOUNDARY 3                          │
│               Internal → External APIs                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  OUTBOUND CONNECTIONS:                                       │
│                                                              │
│  Weather Service ──[HTTPS]──► WeatherAPI                    │
│  • API key in request header                                │
│  • Geohash/coordinates (no raw GPS)                         │
│  • Demo mode fallback (fixtures)                            │
│                                                              │
│  Contacts Service ──[HTTPS]──► Google People API            │
│  • OAuth 2.0 bearer token                                   │
│  • Minimal scope (contacts.readonly)                        │
│  • Token refresh handling                                   │
│                                                              │
│  CONTROLS:                                                   │
│  • HTTPS enforced (TLS 1.2+)                               │
│  • API keys not logged                                      │
│  • Response validation                                       │
│  • Timeout configuration                                     │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

#### TB4: Services → Database (HIGH)

```
┌─────────────────────────────────────────────────────────────┐
│                    TRUST BOUNDARY 4                          │
│               Services → PostgreSQL                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  CONNECTION:                                                 │
│  Contacts Service ──[TCP:5432]──► PostgreSQL                │
│                                                              │
│  CONTROLS:                                                   │
│  • Network isolation (Docker bridge)                        │
│  • Credential-based authentication                          │
│  • Connection pooling (SQLAlchemy)                          │
│  • Parameterized queries (no SQL injection)                 │
│                                                              │
│  DATA STORED:                                                │
│  • OAuth tokens (AES-256-GCM encrypted)                     │
│  • User identifiers                                          │
│  • Token expiration metadata                                 │
│                                                              │
│  DATA PROTECTION:                                            │
│  • Encryption at rest (application-level)                   │
│  • No plaintext secrets in database                         │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Data Flow Diagrams (Level 1)

### Flow 1: Aircraft Authentication

```
┌──────────────┐                              ┌──────────────┐
│   AIRCRAFT   │                              │   GATEWAY    │
│   SYSTEM     │                              │   :8000      │
└──────┬───────┘                              └──────┬───────┘
       │                                             │
       │ ══[1] TLS ClientHello ════════════════════►│
       │                                             │
       │ ◄═══════════════════════ [2] ServerHello ══│
       │                          + Server Cert     │
       │                          + CertRequest     │
       │                                             │
       │ ══[3] Client Certificate ═════════════════►│
       │       (X.509, CA-signed)                   │
       │                                             │──┐
       │                                             │  │ [4] Validate cert
       │                                             │  │     - CA signature
       │                                             │  │     - Not expired
       │                                             │◄─┘     - Not revoked
       │                                             │
       │                                             │──┐
       │                                             │  │ [5] Extract CN
       │                                             │◄─┘     (aircraft_id)
       │                                             │
       │ ══[6] POST /auth/token ═══════════════════►│
       │       {"aircraft_id": "AC-12345"}          │
       │                                             │──┐
       │                                             │  │ [7] Validate:
       │                                             │  │     - CN == aircraft_id
       │                                             │  │     - Generate JWT
       │                                             │  │       (sub=aircraft_id)
       │                                             │◄─┘     (exp=15min)
       │                                             │
       │ ◄══════════════════════════════ [8] 200 ═══│
       │       {"access_token": "eyJ...",           │
       │        "token_type": "Bearer",             │
       │        "expires_in": 900}                  │
       │                                             │
       ▼                                             ▼
```

**Security Controls Applied**:
- [x] mTLS handshake (mutual authentication)
- [x] Certificate validation (CA-signed, not expired)
- [x] CN extraction and binding
- [x] JWT RS256 signing (2048-bit RSA)
- [x] Short token expiry (15 minutes)

### Flow 2: Telemetry Ingestion

```
┌──────────────┐              ┌──────────────┐              ┌──────────────┐
│   AIRCRAFT   │              │   GATEWAY    │              │  TELEMETRY   │
│   SYSTEM     │              │   :8000      │              │   :8001      │
└──────┬───────┘              └──────┬───────┘              └──────┬───────┘
       │                             │                             │
       │ ═[1] POST /telemetry/ingest═►                            │
       │     + mTLS (client cert)    │                             │
       │     + Authorization: Bearer │                             │
       │     + X-Trace-Id: abc123    │                             │
       │     {                       │                             │
       │       "aircraft_id": "...", │                             │
       │       "event_id": "...",    │                             │
       │       "ts": "...",          │                             │
       │       "metrics": {...}      │                             │
       │     }                       │                             │
       │                             │──┐                          │
       │                             │  │ [2] Validate JWT         │
       │                             │  │     - Signature (RS256)  │
       │                             │  │     - Expiry             │
       │                             │  │     - Audience           │
       │                             │◄─┘                          │
       │                             │──┐                          │
       │                             │  │ [3] Cross-validate       │
       │                             │  │     CN == JWT.sub        │
       │                             │◄─┘                          │
       │                             │──┐                          │
       │                             │  │ [4] Rate limit check     │
       │                             │  │     (60 req/min/identity)│
       │                             │◄─┘                          │
       │                             │                             │
       │                             │ ════[5] Proxy request══════►│
       │                             │     (internal network)      │
       │                             │                             │──┐
       │                             │                             │  │ [6] Validate payload
       │                             │                             │  │     - Pydantic model
       │                             │                             │  │     - extra="forbid"
       │                             │                             │◄─┘
       │                             │                             │──┐
       │                             │                             │  │ [7] Idempotency check
       │                             │                             │  │     - (aircraft_id, event_id)
       │                             │                             │  │     - UNIQUE constraint
       │                             │                             │◄─┘
       │                             │                             │──┐
       │                             │                             │  │ [8] PII minimization
       │                             │                             │  │     - Round GPS (4 dec)
       │                             │                             │◄─┘
       │                             │                             │──┐
       │                             │                             │  │ [9] Store telemetry
       │                             │                             │◄─┘
       │                             │                             │
       │                             │ ◄════════[10] Response══════│
       │                             │     201 Created / 200 OK /  │
       │                             │     409 Conflict            │
       │                             │                             │
       │ ◄════════════[11] Response══│                             │
       │     + X-Trace-Id: abc123    │                             │
       │     + Security headers      │                             │
       │                             │                             │
       ▼                             ▼                             ▼
```

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

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│   AIRCRAFT   │      │   GATEWAY    │      │   WEATHER    │      │  WeatherAPI  │
│   SYSTEM     │      │   :8000      │      │   :8002      │      │  (External)  │
└──────┬───────┘      └──────┬───────┘      └──────┬───────┘      └──────┬───────┘
       │                     │                     │                     │
       │ ═[1] GET /weather══►│                     │                     │
       │     ?lat=48.85      │                     │                     │
       │     &lon=2.35       │                     │                     │
       │     + Bearer JWT    │                     │                     │
       │                     │──┐                  │                     │
       │                     │  │ [2] Validate JWT │                     │
       │                     │◄─┘                  │                     │
       │                     │──┐                  │                     │
       │                     │  │ [3] Rate limit   │                     │
       │                     │◄─┘                  │                     │
       │                     │                     │                     │
       │                     │ ══[4] Proxy════════►│                     │
       │                     │                     │                     │
       │                     │                     │──┐                  │
       │                     │                     │  │ [5] Check mode   │
       │                     │                     │  │     (demo/live)  │
       │                     │                     │◄─┘                  │
       │                     │                     │                     │
       │                     │                     │    ┌─── IF LIVE ───┐│
       │                     │                     │ ══►│[6] Build req  ││
       │                     │                     │    │  + API key    ││
       │                     │                     │    │  + coords     │├──►
       │                     │                     │    └───────────────┘│
       │                     │                     │                     │
       │                     │                     │ ◄═══[7] Response════│
       │                     │                     │     (weather data)  │
       │                     │                     │                     │
       │                     │                     │    ┌─ IF DEMO ─────┐│
       │                     │                     │    │ Return Paris  ││
       │                     │                     │    │ fixtures      ││
       │                     │                     │    └───────────────┘│
       │                     │                     │                     │
       │                     │ ◄══[8] Response═════│                     │
       │                     │                     │                     │
       │ ◄══[9] Weather data═│                     │                     │
       │     {"location":... │                     │                     │
       │      "current":...} │                     │                     │
       │                     │                     │                     │
       ▼                     ▼                     ▼                     ▼
```

**Data Protection**:
- GPS coordinates passed as query parameters (not logged)
- API key never exposed to clients
- Demo mode prevents external API calls during testing

### Flow 4: Contacts OAuth

```
┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
│     USER     │   │   GATEWAY    │   │   CONTACTS   │   │    GOOGLE    │
│   BROWSER    │   │   :8000      │   │   :8003      │   │  People API  │
└──────┬───────┘   └──────┬───────┘   └──────┬───────┘   └──────┬───────┘
       │                  │                  │                  │
       │ ═[1] GET /oauth/init═►              │                  │
       │                  │ ══[2] Proxy═════►│                  │
       │                  │                  │──┐               │
       │                  │                  │  │ [3] Build auth URL
       │                  │                  │  │  + client_id
       │                  │                  │  │  + redirect_uri
       │                  │                  │  │  + scope (minimal)
       │                  │                  │◄─┘               │
       │                  │ ◄═[4] Redirect══│                  │
       │ ◄═[5] 302 Redirect═│                  │                  │
       │                  │                  │                  │
       │ ════════════════════[6] User consent════════════════════►
       │                  │                  │                  │
       │ ◄═══════════════════[7] Callback + code═════════════════│
       │                  │                  │                  │
       │ ═[8] GET /oauth/callback?code=...═►│                  │
       │                  │ ══[9] Proxy═════►│                  │
       │                  │                  │ ═[10] POST token═►│
       │                  │                  │     (code exchange)│
       │                  │                  │ ◄═[11] Tokens═════│
       │                  │                  │     (access+refresh)
       │                  │                  │──┐               │
       │                  │                  │  │ [12] Encrypt tokens
       │                  │                  │  │      AES-256-GCM
       │                  │                  │◄─┘               │
       │                  │                  │──┐               │
       │                  │                  │  │ [13] Store in DB
       │                  │                  │◄─┘               │
       │                  │ ◄═[14] Success══│                  │
       │ ◄═[15] Success═══│                  │                  │
       │                  │                  │                  │
       ▼                  ▼                  ▼                  ▼
```

**OAuth Security**:
- Minimal scope: `contacts.readonly`
- State parameter for CSRF protection
- Tokens encrypted before storage (AES-256-GCM)
- Tokens never logged

---

## Security Controls by Layer

### Control Matrix

| Layer | Control | Implementation | File | Status |
|-------|---------|----------------|------|--------|
| **Transport** | TLS 1.2+ | mTLS with strong ciphers | `skylink/mtls.py` | :white_check_mark: |
| **Transport** | Certificate validation | X.509, CA-signed | `scripts/generate_*.sh` | :white_check_mark: |
| **Network** | Service isolation | Docker bridge network | `docker-compose.yml` | :white_check_mark: |
| **Application** | Authentication | JWT RS256 | `skylink/auth.py` | :white_check_mark: |
| **Application** | Authorization | RBAC (5 roles, 7 permissions) | `skylink/rbac.py` | :white_check_mark: |
| **Application** | Cross-validation | CN == JWT sub | `skylink/mtls.py` | :white_check_mark: |
| **Application** | Rate limiting | 60 req/min per identity | `skylink/rate_limit.py` | :white_check_mark: |
| **Application** | Input validation | Pydantic extra=forbid | `skylink/models/` | :white_check_mark: |
| **Application** | Idempotency | Unique constraint | `telemetry/` | :white_check_mark: |
| **Application** | Security headers | OWASP set | `skylink/middlewares.py` | :white_check_mark: |
| **Data** | PII minimization | GPS rounding (4 dec) | `skylink/models/` | :white_check_mark: |
| **Data** | Token encryption | AES-256-GCM | `contacts/encryption.py` | :white_check_mark: |
| **Data** | No PII in logs | Structured logging | `skylink/middlewares.py` | :white_check_mark: |
| **Container** | Non-root user | UID 1000 | `Dockerfile.*` | :white_check_mark: |
| **Supply Chain** | Dependency scanning | pip-audit, Trivy | `.github/workflows/ci.yml` | :white_check_mark: |
| **Supply Chain** | Image signing | Cosign (keyless) | `.github/workflows/ci.yml` | :white_check_mark: |
| **Supply Chain** | SBOM | CycloneDX | `.github/workflows/ci.yml` | :white_check_mark: |
| **Supply Chain** | Secret detection | Gitleaks | `.github/workflows/ci.yml` | :white_check_mark: |

### Defense in Depth Visualization

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                          │
│   Layer 1: NETWORK                                                       │
│   ├── Docker bridge isolation                                           │
│   ├── Internal services not exposed                                     │
│   └── Single entry point (Gateway:8000)                                 │
│                                                                          │
│   ┌───────────────────────────────────────────────────────────────────┐ │
│   │                                                                    │ │
│   │   Layer 2: TRANSPORT                                               │ │
│   │   ├── mTLS (mutual TLS)                                           │ │
│   │   ├── TLS 1.2+ with strong ciphers                               │ │
│   │   └── Certificate validation                                       │ │
│   │                                                                    │ │
│   │   ┌───────────────────────────────────────────────────────────┐   │ │
│   │   │                                                            │   │ │
│   │   │   Layer 3: APPLICATION                                     │   │ │
│   │   │   ├── JWT RS256 authentication                             │   │ │
│   │   │   ├── RBAC (5 roles, 7 permissions)                       │   │ │
│   │   │   ├── CN ↔ JWT cross-validation                           │   │ │
│   │   │   ├── Rate limiting (60 req/min)                          │   │ │
│   │   │   ├── Input validation (Pydantic)                         │   │ │
│   │   │   ├── Security headers (OWASP)                            │   │ │
│   │   │   └── Payload limit (64KB)                                │   │ │
│   │   │                                                            │   │ │
│   │   │   ┌───────────────────────────────────────────────────┐   │   │ │
│   │   │   │                                                    │   │   │ │
│   │   │   │   Layer 4: DATA                                    │   │   │ │
│   │   │   │   ├── AES-256-GCM encryption                      │   │   │ │
│   │   │   │   ├── GPS rounding (PII minimization)             │   │   │ │
│   │   │   │   ├── No PII in logs                              │   │   │ │
│   │   │   │   └── Idempotency controls                        │   │   │ │
│   │   │   │                                                    │   │   │ │
│   │   │   └───────────────────────────────────────────────────┘   │   │ │
│   │   │                                                            │   │ │
│   │   └───────────────────────────────────────────────────────────┘   │ │
│   │                                                                    │ │
│   └───────────────────────────────────────────────────────────────────┘ │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

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

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        DATA HANDLING RULES                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  INTERNAL DATA (Aircraft UUID, trace_id)                                │
│  ✓ Can be logged                                                        │
│  ✓ Can be stored plaintext                                             │
│  ✓ Can be transmitted                                                   │
│                                                                          │
│  CONFIDENTIAL DATA (Telemetry)                                          │
│  ✓ Can be stored                                                        │
│  ✗ Cannot be logged (only trace_id)                                    │
│  ✓ Must be encrypted in transit (TLS)                                  │
│                                                                          │
│  PII DATA (GPS, Contacts)                                               │
│  ⚠ GPS must be rounded (4 decimals = ~11m accuracy)                    │
│  ⚠ Contacts are read-only, not persisted                               │
│  ✗ Never logged                                                         │
│  ✓ Must be encrypted in transit (TLS)                                  │
│                                                                          │
│  RESTRICTED DATA (Tokens, Keys, Certs)                                  │
│  ✓ Must be encrypted at rest (AES-256-GCM)                             │
│  ✓ Must be encrypted in transit (TLS)                                  │
│  ✗ Never logged                                                         │
│  ✗ Never in source code                                                │
│  ✓ Environment variables or secrets manager                            │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

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

---

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

### Key Management

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        KEY MANAGEMENT                                    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  DEVELOPMENT ENVIRONMENT                                                │
│  ├── .env file (git-ignored)                                           │
│  ├── Generated keys in /tmp                                            │
│  └── Test certificates in certs/                                        │
│                                                                          │
│  CI/CD ENVIRONMENT                                                       │
│  ├── GitHub Secrets / GitLab CI Variables                              │
│  ├── Protected variables (protected branches only)                      │
│  └── Masked in logs                                                     │
│                                                                          │
│  PRODUCTION (RECOMMENDED)                                                │
│  ├── HashiCorp Vault                                                    │
│  ├── AWS KMS / GCP KMS                                                  │
│  └── HSM for aircraft keys                                              │
│                                                                          │
│  ROTATION SCRIPTS                                                        │
│  ├── scripts/rotate_jwt_keys.sh (planned)                              │
│  ├── scripts/rotate_encryption_key.sh (planned)                        │
│  └── scripts/renew_certificates.sh (planned)                           │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Network Security

### Network Topology

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           INTERNET                                       │
│                              │                                           │
│                              │ TCP:8000 (mTLS)                          │
│                              ▼                                           │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                      DOCKER HOST                                   │  │
│  │                                                                    │  │
│  │  ┌─────────────────────────────────────────────────────────────┐  │  │
│  │  │                  skylink-net (bridge)                        │  │  │
│  │  │                                                              │  │  │
│  │  │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐        │  │  │
│  │  │  │ gateway │  │telemetry│  │ weather │  │contacts │        │  │  │
│  │  │  │  :8000  │  │  :8001  │  │  :8002  │  │  :8003  │        │  │  │
│  │  │  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘        │  │  │
│  │  │       │            │            │            │              │  │  │
│  │  │       └────────────┴────────────┴────────────┘              │  │  │
│  │  │                         │                                    │  │  │
│  │  │                    ┌────▼────┐                              │  │  │
│  │  │                    │   db    │                              │  │  │
│  │  │                    │  :5432  │                              │  │  │
│  │  │                    └─────────┘                              │  │  │
│  │  │                                                              │  │  │
│  │  └─────────────────────────────────────────────────────────────┘  │  │
│  │                                                                    │  │
│  │  EXPOSED PORTS:                                                   │  │
│  │  • 8000 (gateway) → mapped to host                               │  │
│  │                                                                    │  │
│  │  INTERNAL ONLY:                                                   │  │
│  │  • 8001, 8002, 8003, 5432 → not exposed                         │  │
│  │                                                                    │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

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

See [KUBERNETES.md](KUBERNETES.md) for full network policy configuration.

---

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

