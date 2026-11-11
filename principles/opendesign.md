---
title: Open design
short_title: Open design
---


## Principle


Systems should not depend on secrecy of design or hidden behaviour for protection. Security controls should remain effective even if an adversary understands how the system operates.

## Objective

Ensure that the product’s security does not depend on the secrecy of its design or implementation details (‘security through obscurity’). Open design makes security properties reviewable, testable and maintainable.

## Checklist

### Document security-relevant design decisions

- Keep a short security design note covering trust boundaries, auth model, cryptographic usage, update mechanism, logging/audit approach and secure defaults.
- Record the rationale for ‘why this control/protocol/library’.

### Prefer open standards and proven building blocks

- Use standard, widely reviewed protocols and libraries (e.g. TLS, OAuth/OIDC, JWT with clear constraints, signed updates).
- Avoid proprietary crypto, “home-grown” protocols, and undocumented encodings for security-critical flows.

### Make interfaces and security expectations explicit

- Document APIs, authentication/authorisation requirements, error handling, rate limits and supported configurations.
- Provide a secure configuration guide (outlining what must be enabled and disabled in production).

### Enable review and disclosure

- Establish a vulnerability disclosure channel (security contact, intake process, basic triage/response targets).
- Encourage internal peer review for security-sensitive changes (to auth, updates, crypto, boundary-crossing interfaces).

### Maintain transparency of components and changes

- Maintain, publish and sign an SBOM (at least for customer-facing builds) and track third-party components.
- Keep a security changelog / release notes for security-impacting fixes and default-setting changes.

## Minimum evidence

- Security design note (1–3 pages), covering boundaries, auth, crypto choices, update path, logging, secure defaults
- API + security requirements documentation (authn/authz, rate limits, data-handling expectations)
- Secure configuration guide (production hardening defaults, required settings)
- Vulnerability disclosure process (public email / web page or customer-facing channel; triage ownership defined)
- SBOM (exported file or tool output) + dependency -tracking approach
- Security-sensitive PR review rule (CODEOWNERS file, repo policy or checklist evidence)

## Release gate

- [ ] Security design note updated for any architectural/security-relevant change (auth, crypto, update path, trust boundaries)
- [ ] No custom/proprietary crypto or undocumented security-critical protocols introduced
- [ ] API/security documentation updated (auth requirements, scopes/roles, rate limits, secure defaults)
- [ ] Secure configuration guide updated; defaults validated for new/exposed interfaces
- [ ] SBOM generated for the release and stored/published per policy
- [ ] Vulnerability disclosure channel tested (contact works) and ownership/triage defined
