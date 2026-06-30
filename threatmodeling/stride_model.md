---
title: STRIDE Threat Model
short_title: STRIDE Threat Model
---


**STRIDE Threat Model**


| **Threat**                 | **Threat Definition**                                                                              |
|----------------------------|----------------------------------------------------------------------------------------------------|
| **Spoofing**               | Pretending to be something or someone other than oneself                                           |
| **Tampering**              | Unauthorised modification of data on disk, in network transit, in memory, or in other locations    |
| **Repudiation**            | Denying responsibility for an action or event (whether the claim is true or false)                 |
| **Information Disclosure** | Unauthorised access to or exposure of sensitive or confidential information                        |
| **Denial of Service**      | Making a service unavailable by exhausting or depleting required resources                         |
| **Elevation of Privilege** | Gaining higher access rights or permissions than originally authorised                             |

:::{note} 
- This table is commonly used as the foundation for threat modelling for systems, processes, software libraries, SaaS services, etc.

- STRIDE is a mature threat-modeling method that is used at large worldwide. For more information check [Open Security Reference Architecture](https://nocomplexity.com/documents/securityarchitecture/introduction.html).

:::

## Recommended Threat Modeling Steps

**Yes, it's mostly correct and significantly improved** — it's now a solid, usable guide. 

However, it's not *quite* perfect yet. There are some **formatting issues**, a few **minor inaccuracies**, and some **flow/organisation** problems that reduce clarity.

### Specific Issues & Recommendations:

1. **Formatting & Structure Problems**
   - The bullet list under **Step 2** breaks (some lines aren't properly bulleted).
   - The DFD explanation appears suddenly in the middle of Step 2 — it feels dumped in.
   - The STRIDE table is good but has a small alignment issue in the header/columns.

2. **Minor Technical Inaccuracies**
   - Data stores in DFDs are typically represented by **two parallel horizontal lines** or a cylinder, not just “lines above and below their names”.
   - “Interactors” is correct terminology, but commonly called **External Entities**.
   - The sentence “Everything except the trust boundaries... are considered elements” is awkwardly phrased and not very useful — it can be removed or simplified.

3. **Flow & Conciseness**
   - Some content (especially the DFD rules) could be better separated as a **tip/subsection** rather than mixed into the main flow.
   - Minor repetition between general modeling advice and STRIDE-specific guidance.

---


## Recommended Threat Modeling Steps

**Step 1: Define Scope and Objectives**
- Clarify the purpose of the threat model (e.g., compliance, design review, risk assessment).
- Define security requirements and success criteria.
- Identify stakeholders and their responsibilities.

**Step 2: Model the System (Decompose the Application)**
- Create an **application/system diagram** (e.g., Data Flow Diagram, architecture diagram, or even a simple sketch).
- Document high-level system information, including:
  - System name
  - System description and architecture overview
  - Types and sensitivity of data handled (e.g., PII, financial, health)
  - Scope and boundaries
  - External interactions, third parties, and trust boundaries
  - Primary workflows and use cases
- Identify assets (what needs protection) and entry/exit points.

**Tip for Step 2 – Using Data Flow Diagrams (DFD):**
When applying STRIDE, create a simple DFD:
- **Processes**: Circles or rounded rectangles
- **Data stores**: Two parallel horizontal lines or cylinders
- **Data flows**: Directed arrows (including network flows)
- **External entities / Interactors**: Rectangles
- **Trust boundaries**: Dashed lines

**Rules of thumb for a good DFD:**
- Every data store should have at least one input and one output.
- Only processes can read from or write to data stores.
- Keep the model simple — combine similar elements within the same trust boundary.

**Step 3: Identify Threats**
- Use a structured methodology such as **STRIDE** (or LINDDUN, PASTA, Attack Trees).
- Brainstorm threats for each element in the model (processes, data flows, data stores, external entities).
- Consider attacker profiles, motivations, and capabilities.
- Leverage threat intelligence, OWASP Top 10, CVEs, and past incidents.

**STRIDE Threat Categories:**

| Threat | Property Violated     | Definition |
|--------|-----------------------|----------|
| **S**  | Spoofing              | Pretending to be someone/something else |
| **T**  | Tampering             | Unauthorised modification of data |
| **R**  | Repudiation           | Denying performing an action |
| **I**  | Information Disclosure| Exposing information to unauthorised parties |
| **D**  | Denial of Service     | Making a system unavailable |
| **E**  | Elevation of Privilege| Gaining higher access rights |

**Step 4: Assess and Prioritise Risks**
- Evaluate likelihood and business impact of each threat.
- Calculate risk levels (qualitative or quantitative).
- Prioritise based on risk rating and business context.

**Step 5: Define Mitigations and Countermeasures**
- Propose security controls for high-priority threats (authentication, encryption, input validation, monitoring, etc.).
- Assign owners, timelines, and effort estimates.
- Document residual risk.

**Step 6: Validate, Review, and Iterate**
- Review with stakeholders, architects, and security experts.
- Validate assumptions against the actual implementation.
- Update the model during design changes or major releases.
- Maintain findings in a threat model report or register.
```

