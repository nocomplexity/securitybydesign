---
title: Checklist for Secure coding and verification practices
short_title: Checklist:Secure coding
---

Developers should follow established secure coding standards to prevent common vulnerabilities.

## Objective

Reduce common, high-impact vulnerabilities by eliminating relevant weakness classes through architectural and technology choices, where feasible, and by standardising how remaining risks are addressed in code, review and testing.

:::{tip}
Since most companies outsource software development or use SaaS services, ask for evidence of the secure coding standards your software vendors follow to increase trust in their practices.
:::

## Checklist

### Eliminate vulnerability classes by construction

- Prefer architectural and technology choices that prevent relevant vulnerability classes from arising, such as memory-safe languages for buffer overflows, parameterised data-access APIs for SQL injection and frameworks with context-aware output encoding for cross-site scripting.
- Where elimination is not feasible, identify the remaining vulnerability classes and apply appropriate coding, review, testing and containment controls.

### Adopt a secure coding baseline (language/framework specific)

- Define must-follow rules for your stack (e.g. on input handling, auth checks, error handling, crypto usage, logging).
- Ban unsafe patterns (e.g. string-built SQL, eval-like functions, disabling TLS verification).

### Validate inputs and encode outputs

- Centralise validation (schemas, allow lists, length/range checks).
- Encode output by context (HTML/JSON/SQL); avoid mixing data and commands.

### Use safe dependency and secrets practices

- Pin and regularly update dependencies; remove unused packages.
- Secrets (e.g. API keys, tokens, credentials) must never be stored in source code or committed to source repositories. Secure secret management solutions should be used instead.
- Generate and store an SBOM for each release (or a dependency inventory at minimum).

### Make security-sensitive changes reviewable

- Clearly define and document roles for each stakeholder involved in the CI/CD pipeline (e.g. developer, support, administrator, integrator, CI runner).
- Protect critical branches to prevent direct commits and history rewriting.
- Prohibit force push on protected branches to prevent history rewriting and preserve auditability.
- Require peer review for changes touching authn/authz, boundary-crossing APIs, crypto, update mechanisms or deserialisation and for any new external exposure.
- Use PR checklists and CODEOWNERS files for sensitive areas.
- Apply the same review requirements to AI-generated or AI-modified code as to human-written code.

### Automate detection in CI/CD

- Run SAST and dependency scanning on each PR; fail builds on critical findings.
- Add targeted unit/integration negative tests (unauthorised access denied; injection attempts fail).
- Add basic fuzzing or property-based testing for high-risk parsers/inputs if feasible.
- AI-generated or AI-modified code should go through the same SAST, dependency and secrets checks as human-written code.

## Minimum evidence

- Design evidence: record of relevant vulnerability classes considered and the architectural or technology choices used to eliminate them by construction
- Secure coding standards (one or two pages or a repo file) for your stack and a banned patterns list
- PR checklist / CODEOWNERS file for security-sensitive modules
- CI pipeline evidence: SAST and dependency scan results for the release
- Secrets controls: proof that secrets are not in repo (pre-commit secret scanning results) and secrets manager usage
- Test evidence: at least a small suite of negative tests for critical endpoints/inputs

## Release gate

- [ ] Relevant vulnerability classes have been eliminated by construction where feasible; where not feasible, appropriate preventive and verification controls are in place.
- [ ] A secure coding baseline exists for the stack and is referenced in the repo
- [ ] SAST and dependency scanning run in CI; critical issues fixed or covered by a documented exception (with owner and expiry)
- [ ] Secret scanning enabled; no secrets committed; rotation performed if exposure occurred
- [ ] Security-sensitive changes were peer reviewed (auth/authz, crypto, parsers, external interfaces)
- [ ] Negative tests run on critical endpoints (unauthorised access denied; injection attempts fail)
- [ ] Dependencies reviewed for high/ critical vulns; patch/mitigation plan recorded for any accepted risk
