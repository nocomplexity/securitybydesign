---
title: Checklist for Supply-chain controls
short_title: Checklist:Supply-chain controls
---


Developers should protect product integrity without excessive process overhead, focusing on the points where a compromise would have the largest impact: code repositories, build systems, signing keys and the channels used to distribute updates.

## Objective

Reduce the risk of compromise through third parties (software components, libraries, build tools, CI/CD, contractors, hosting providers and hardware/firmware suppliers) by establishing minimum, repeatable controls that improve visibility, integrity and accountability across what you buy, build and ship.

## Checklist

### Know what you depend on (inventory + SBOM)

- Maintain an inventory of critical suppliers and third-party components, including externally sourced AI/ML models where relevant.
- Generate an SBOM per release (or minimum: dependency manifest snapshot) to improve visibility and response speed.

### Control what enters your codebase

- Pin dependency versions; require review for new dependencies and major upgrades. Pinning and lock files improve build repeatability but do not establish that the pinned component is trustworthy. New and updated dependencies still require appropriate review and security checks.
- Run dependency scanning in CI; block builds on critical- or high where feasible (or require explicit exception).
- Where proportionate, obtain dependencies through approved repositories or controlled registries and apply integrity, malware and policy checks before use.
- For externally sourced AI/ML models, record their source and version, verify their integrity and provenance, and assess relevant security risks before integration.

### Harden the build and release pipeline (integrity of artefacts)

- Restrict CI/CD permissions (least privilege), protect secrets and separate build and deploy roles.
- Sign release artefacts and, where proportionate, generate verifiable provenance describing how and from which inputs they were built.
- For higher-risk products, use isolated and ephemeral build environments, consider restricting build-time network access to approved sources and consider hermetic builds where feasible.

### Set minimum supplier expectations (lightweight due diligence)

- For critical suppliers, require a security contact / VDP, patch timelines and confirmation of secure development practices.
- Use a short questionnaire aligned with a recognised baseline (e.g. OWASP SCVS) rather than bespoke questions.

### Plan for supplier failure

- Identify single points of failure (key providers/components) and define fallbacks (alternate package source, vendor substitution plan, rapid disable/mitigation).
- Ensure that contracts and SLAs cover vulnerability notification and support windows (where possible).

## Minimum evidence

- Supplier + component inventory (top vendors, critical libraries/tools, hosting/build services), including externally sourced AI/ML models where relevant
- SBOM (or dependency inventory) per release, stored with the release artefacts
- CI results showing dependency scanning (and ideally secret scanning) executed on PRs/releases
- Release integrity evidence: artefact signing and restricted CI/CD access (configs/screenshots/logs)
- Supplier baseline checks for critical suppliers (a completed questionnaire or SCVS-aligned checklist)
- Exception log for accepted supply-chain risks (with owners and expiry dates)

## Release gate

- [ ] SBOM (or dependency inventory) generated and stored for this release
- [ ] Dependency scanning executed; critical- or high-severity issues fixed or covered by a documented exception (with owner and expiry date)
- [ ] New dependencies/suppliers reviewed and approved (recorded in PR/ticket)
- [ ] CI/CD and build secrets are protected; build/deploy permissions are least privilege and attributable
- [ ] Release artefacts signed (and provenance captured where feasible / per chosen SLSA  target)
- [ ] Critical suppliers meet baseline expectations (security contact, vulnerability notification, support/patch commitments)
- [ ] Supply-chain risk exceptions recorded, with owner and review/expiry date
