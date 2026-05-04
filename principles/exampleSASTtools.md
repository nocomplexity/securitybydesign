---
title: Example Applying Security Principles for SAST testing
short_title: Example:Using SAST Tools
---


Defence in depth is a core security principle that relies on multiple, independent security controls. If one control fails, another should detect or mitigate the threat. In Python Static Application Security Testing (SAST), relying on a single scanner creates unnecessary risk and provides incomplete vulnerability coverage.

Relying on one tool places complete trust in its rule set, configuration, and implementation. This weakens your defence, especially given the inherent limitations of static analysis.

## Why a single SAST tool is rarely sufficient

No scanner achieves complete coverage. Tools vary in:

- Data-flow and taint-propagation modelling
- Interpretation of Python features and frameworks
- Detection of insecure patterns
- The trade-off between false positives and false negatives

Every tool has blind spots. Combining multiple scanners significantly reduces the chance of missing critical issues.

## How multiple SAST tools enable defence in depth

Running two or more complementary scanners adds meaningful redundancy within the static-analysis layer.

- **Diverse rule sets and logic** – One tool may catch issues another overlooks. Misconfigurations or outdated rules in one tool are often covered by another.
- **Independent implementations** – Architectural differences (e.g., AST-based vs semantic analysis) minimise shared failure modes. SAST tools are implemented differently to balance accuracy, performance, and developer usability. These differences reduce the risk of systemic blind spots.

## The limits of multiple SAST tools in defence in depth

Multiple SAST tools improve coverage within a single layer (source-code analysis, typically at build or commit time), but they do not constitute full defence in depth. True layered protection requires controls that vary in:

- **Timing** – design, build, test, runtime
- **Perspective** – code, runtime behaviour, configuration, operations

Multiple SAST tools add redundancy within one layer, but true defence in depth requires controls across multiple layers.

## Avoid security by obscurity in SAST tools

A trustworthy SAST scanner should be Free and Open Source Software (FOSS), published under an OSI-approved licence. Open-source security tools allow independent verification of:

- Detection logic
- Vulnerability rules
- Analysis methodology

Python Code Audit is fully open source (GPLv3), enabling continuous peer review, strong security assurances, and zero vendor lock-in.

## Practising real defence in depth for Python applications

To achieve meaningful defence in depth, Python security programmes should include controls across the entire development and deployment lifecycle:

- Threat modelling
- Secure architecture and design reviews
- Static Application Security Testing (SAST)
- Software Composition Analysis (SCA), including SBOM generation and dependency vulnerability checks
- Privacy and data protection risk assessments
- Fuzz testing for critical inputs
- Network and application firewalls (including a WAF)
- Runtime monitoring and secure logging
- Python secure coding standards
- Ongoing security awareness and training

## Summary

Using multiple SAST tools strengthens Python static analysis coverage, but true defence in depth requires layered security controls across design, build, and runtime. [Python Code Audit](https://github.com/nocomplexity/codeaudit) provides transparent, Python-focused SAST capabilities that integrate effectively into a broader application security strategy.

