# EXL Architecture Principles

These principles are the **primary input** to architecture generation and the **first checklist** during ARB review. Every solution proposed for EXL or its clients must demonstrate adherence to each principle, or document an explicit, time-boxed exception.

Each numbered heading identifies the principle. Entries use the TOGAF-style fields **Principle (name), Statement, Rationale and Implications**. ARB review questions and deployment evidence are EXL-specific supporting guidance. The numbering and existing requirements are retained.

---

## 1. Simplicity over complexity

**Principle:** Simplicity over complexity

### Statement

Choose the simplest solution that meets agreed requirements; introduce features and complexity only where a clear benefit or evidenced constraint justifies them.

### Rationale

Unnecessary complexity increases delivery time, defects, security exposure and operating costs. Focusing on agreed outcomes keeps the solution understandable and maintainable, including when AI makes additional code and features easy to generate.

### Implications

Choose the simplest design that meets the agreed functional requirements, security obligations and measurable quality requirements. Features and technologies must earn their place through a clear business benefit or an evidenced constraint. Faster code generation does not justify expanding scope or increasing architectural complexity.

- Trace each proposed feature and major component to an agreed requirement, mandatory control or measurable outcome. Remove or defer additions without a clear justification and accountable business owner.
- Prefer fewer components, dependencies, deployment units and operational steps where they meet the need. Introduce distribution, orchestration, abstraction or AI only when a simpler approach is demonstrably insufficient.
- Size the design for evidenced demand and credible growth assumptions. Avoid speculative scale, unused flexibility and features added merely because a tool makes them easy to generate.
- Compare the proposed design with a simpler alternative. Explain why the extra complexity is necessary and assess its delivery, testing, security, operational and maintenance costs.
- Apply the same scope control, design review and testing to AI-assisted or vibe-coded changes as to manually written changes. Generated features, dependencies and services must not bypass requirements or architecture review.
- Simplicity does not justify omitting required security, reliability, accessibility or other controls. Meet those requirements with the least complexity that is fit for purpose.

### ARB review questions

What is the simplest solution that would meet this use case? Which features or components could we remove without losing an agreed outcome? What evidence justifies each remaining source of complexity?

## 2. Reuse before buy before build

**Principle:** Reuse before buy before build

### Statement

Reuse existing capabilities first, evaluate third-party options next, and build only where those options do not meet the need.

### Rationale

Reusing proven capabilities reduces duplication and delivery effort. Selective procurement can meet common needs while engineering effort focuses on the capabilities that differentiate EXL and its clients.

### Implications

Check for reusable components  (EXLerate accelerators, internal common libraries, approved OSS) before leveraging or creating new code.  New components must justify why an existing capability does not fit. Evaluate 3rd party open source or licensed software before building.  We do not need to reinvent the wheel and should focus development on where we diffentiate.   3rd party software does introduce some licensing and cost considerations when deploying in a client environment.

## 3. Cloud-agnostic by design, cloud-native by implementation

**Principle:** Cloud-agnostic by design, cloud-native by implementation

### Statement

Keep business logic portable across clouds while using provider-native managed services for implementation.

### Rationale

Portable designs support reuse across client environments and reduce the cost of changing providers. Managed services can simplify delivery and operations without unnecessarily coupling business logic to a provider.

### Implications

Designs must express logic in portable terms (containers, OCI images, OpenAPI, SQL/ANSI, Parquet, OpenTelemetry) so that workloads can move between AWS, Azure, and GCP.  This prevents cloud and vendor lockin, promotes reusability of assets and expands our client reach. Use provider-native managed services for *implementation* (e.g. EKS/Fargate vs AKS/Container Apps vs GKE) but do not let provider-specific abstractions leak into business logic.

## 4. Data sovereignty and residency first

**Principle:** Data sovereignty and residency first

### Statement

Determine data classification and residency obligations before choosing where or how data is processed and stored.

### Rationale

Early decisions about data handling reduce the risk of unauthorized processing and costly redesign, and preserve client control over sensitive information.

### Implications

Identify the data classification (Public / Internal / Client-Confidential / Regulated / PHI / PII / PCI) **before** selecting a region, service, or persistence layer. Regulated data stays in the client-designated region and tenant. Default to in-region keys (KMS / Key Vault / Cloud KMS) with customer-managed keys (CMK) for anything above Internal.

## 5. Identity is the new perimeter

**Principle:** Identity is the new perimeter

### Statement

Authenticate human and workload access through managed identities and controlled credential lifecycles.

### Rationale

Explicit identity and access controls reduce reliance on network location and limit exposure from leaked, shared or long-lived credentials.

### Implications

- All human access through SSO + MFA via EXL IdP.
- All workload-to-workload access through workload identity (IAM Roles, Managed Identities, Workload Identity Federation). **No static long-lived credentials in code, config, or pipelines.**
- All third-party calls through short-lived OIDC-federated tokens or rotated secrets brokered by the central secrets manager.

## 6. Zero trust networking

**Principle:** Zero trust networking

### Statement

Treat network access as untrusted by default and explicitly control, authenticate and encrypt communication.

### Rationale

Network boundaries alone do not establish trust. Controlled ingress and protected internal communication reduce unauthorized access and lateral movement.

### Implications

Private endpoints by default. Public ingress only via vetted edges (CloudFront / Front Door / Cloud CDN + WAF). East-west traffic authenticated and encrypted (mTLS or service mesh). No flat VPCs across environments; no NAT-only data plane.

## 7. Everything as code

**Principle:** Everything as code

### Statement

Define infrastructure, policy, pipelines and configuration as controlled code rather than manual production changes.

### Rationale

Repeatable, reviewable changes reduce configuration drift, improve traceability and make recovery and environment reproduction more reliable.

### Implications

Infra: Terraform (preferred) or provider-native IaC (CloudFormation/Bicep/Deployment Manager) — **never** click-ops in production. Policy: OPA / Cloud-native policy. Pipelines: declarative. Configuration: managed through Parameter Store / App Configuration / Secret Manager with version pinning.

## 8. Observability is a feature, not an afterthought

**Principle:** Observability is a feature, not an afterthought

### Statement

Include operational visibility and measurable service objectives in every service design and delivery.

### Rationale

Teams need evidence to detect failures, understand user impact and diagnose problems. Building visibility in from the outset reduces blind spots and recovery effort.

### Implications

Every service ships:

- Structured logs (JSON, correlation IDs)
- Metrics (USE / RED) emitted via OpenTelemetry
- Distributed traces propagated across all hops
- A defined SLO with an error budget and an alerting policy tied to it

## 9. FinOps from day one

**Principle:** FinOps from day one

### Statement

Make cost attribution, unit economics and spending controls part of the architecture from the start.

### Rationale

Visible costs support informed trade-offs, accountability and early correction of waste before it becomes embedded in the solution.

### Implications

Designs must include a tagging strategy (`exl:client`, `exl:project`, `exl:cost-center`, `exl:environment`), a unit-cost target (cost per transaction / per record / per inference), and a kill-switch for non-prod environments outside business hours.

## 10. Resilience by design

**Principle:** Resilience by design

### Statement

Design for stated availability and recovery objectives, and verify recovery through planned exercises.

### Rationale

Failures are unavoidable. Explicit objectives and rehearsed recovery reduce disruption and make resilience commitments assessable.

### Implications

State recovery objectives explicitly: **RTO**, **RPO**, **availability target**. Multi-AZ is the floor; multi-region is required for Tier-1. Chaos and DR drills are scheduled, not aspirational.

## 11. Security shifts left

**Principle:** Security shifts left

### Statement

Integrate security analysis and automated checks into design, development and release.

### Rationale

Finding weaknesses early reduces remediation effort and prevents avoidable exposure from reaching production.

### Implications

Threat-model at design time (STRIDE). SAST, SCA, IaC scan, container scan, and DAST gate the pipeline. Secrets scanning is a pre-receive hook. SBOM produced for every release.

## 12. AI/ML and agentic systems are governed

**Principle:** AI/ML and agentic systems are governed

### Statement

Apply explicit governance, evaluation and oversight to AI, ML and agentic workloads throughout their lifecycle.

### Rationale

Probabilistic outputs and agent actions introduce risks that require evidence, traceability and accountable human oversight.

### Implications

GenAI and agentic workloads follow `standards/agentic-best-practices.md`: evaluation-driven design, human-in-the-loop checkpoints for sensitive actions, model registry, prompt and tool-use logging, PII redaction at the prompt boundary, and routing through EXLerate where available.

## 13. Accessibility and inclusivity

**Principle:** Accessibility and inclusivity

### Statement

Design interfaces to be accessible and inclusive from the outset.

### Rationale

Accessible services enable more people to use the solution effectively and reduce the cost and disruption of retrofitting accessibility.

### Implications

Client-facing UIs meet **WCAG 2.1 AA** minimum. Internal-facing UIs meet WCAG 2.1 A. Color contrast, keyboard navigation, screen reader semantics, and localization are design inputs, not retrofits.

## 14. Sustainability

**Principle:** Sustainability

### Statement

Use infrastructure and compute efficiently and account for environmental impact in architecture decisions.

### Rationale

Efficient capacity and resource choices reduce waste and environmental impact while supporting cost-effective operation.

### Implications

Right-size compute. Prefer serverless/spot/managed services that share infrastructure. Choose low-carbon regions when client SLA permits. Track and report carbon proxies (energy, instance-hours) alongside cost.

## 15. Documentation as a deliverable

**Principle:** Documentation as a deliverable

### Statement

Deliver and maintain architecture and operational documentation as part of the solution.

### Rationale

Documented designs, decisions and procedures enable consistent review, effective handover and informed maintenance.

### Implications

Every solution produces:

- An IEEE 1016 SDD (see `templates/ieee-1016-sdd-template.md`)
- A C4 diagram (Context, Container; Component where useful)
- An operational runbook
- A decision log (ADRs) for non-obvious choices

## 16. Exception process is explicit

**Principle:** Exception process is explicit

### Statement

Make deviations from architecture principles explicit, time-boxed and subject to ARB approval.

### Rationale

Visible exceptions allow necessary trade-offs without creating undocumented policy drift or leaving risks without owners.

### Implications

Any deviation from these principles must be:

1. Documented in the SDD's Decisions section with rationale.
2. Time-boxed with a remediation plan and owner.
3. Approved by the Architecture Review Board

## 17. Limit unnecessary technology variety

**Principle:** Limit unnecessary technology variety

### Statement

Limit technology variety to what the requirements justify, preferring existing approved capabilities where they fit.

### Rationale

Each additional technology creates integration, security, support and skills costs. A coherent technology portfolio improves reuse and maintainability while allowing justified additions.

### Implications

Prefer existing approved technologies and established EXL or client patterns when they meet the requirements. Every additional language, framework, database, infrastructure service or orchestration layer must have a clear benefit that outweighs the added integration, security, support, skills and operating costs. Being on an approved list does not, by itself, justify adding a technology to a solution.

- Compare proposed additions with capabilities already available in the solution and the approved technology portfolio.
- Document the requirement or constraint that existing options cannot reasonably meet, the alternatives considered and the expected benefit of the addition.
- Identify ownership, support needs and lifecycle costs. Avoid multiple tools serving the same purpose without an explicit justification.
- A justified new technology remains possible; anything absent from the approved lists requires an ARB exception under principle 16.

### ARB review questions

What existing capability could meet this need? Why is another technology necessary? What would be lost if it were removed?

## 18. Protect intellectual assets

**Principle:** Protect intellectual assets

### Statement

Protect intellectual assets throughout their lifecycle, including when code, containers, prompts and skills are deployed into client environments.

### Rationale

Intellectual assets embody EXL, client and third-party investment and obligations. Explicit ownership and deployment protections reduce unauthorized disclosure, copying, modification and reuse; the design must also account for exposure to infrastructure administrators.

### Implications

Identify and protect EXL, client and third-party intellectual assets throughout design, development, delivery and operation. These include source code, container images, proprietary algorithms, business rules, prompts, skills, datasets, models, architecture documents and reusable accelerators. Reuse must respect ownership, confidentiality, licensing and contractual restrictions.

- Record ownership, permitted use and restrictions for intellectual assets used or produced by the solution. Resolve unclear rights with the appropriate owner before reuse or distribution.
- Restrict access and sharing to authorized users, projects and clients. Do not transfer proprietary assets between client environments without explicit authorization.
- Before sending assets to an AI coding assistant, model provider or other external tool, verify that the tool and its data handling are approved for those assets, including retention and training use.
- Review third-party and AI-assisted contributions for provenance, licensing obligations and inadvertent inclusion of proprietary material before release.
- Include relevant controls for repositories, artifacts, logs, prompts and exports; confidentiality must survive handoffs and deployment.

#### Protection when deployed in a client environment

The SDD must explain how EXL intellectual assets remain protected when deployed into infrastructure operated by a client or its suppliers. Cover code, container images and layers, prompts, skills, configuration, temporary files, backups and diagnostic outputs.

- Encrypt intellectual assets at rest and in transit, including repositories, image registries, deployment packages, storage and backups. Document the encryption boundary and any point at which assets become readable for execution. Container packaging alone is not an IP protection control.
- Define who controls encryption keys, who can decrypt assets and how access is granted, audited, rotated and revoked. Do not embed keys or secrets in code, images, prompts or skills. Distinguish protection of EXL-owned assets from client data requirements and agree key custody with the client and asset owner.
- Deploy only the artifacts required to operate the solution. Exclude development repositories, build credentials, unnecessary source files and proprietary material not needed at runtime. Restrict registry pulls, image exports, filesystem access, interactive execution and debugging to explicitly authorized identities.
- Protect prompts and skills from unauthorized retrieval through APIs, user interfaces, logs, traces, error messages and support exports. Apply access controls to their storage and runtime delivery; do not treat instructions to the model as a confidentiality boundary.
- Verify deployment artifact provenance and integrity, restrict modification and retain access and deployment audit records. Define protection during support, upgrades, backups, export and offboarding, including removal of deployed copies and revocation of credentials where contractually permitted.
- Explicitly assess client administrator and host access. Encryption at rest and in transit does not by itself prevent a privileged infrastructure operator from accessing assets once decrypted for execution. Where protection from that operator is required, document a validated deployment boundary and approved technical controls that meet it, or obtain an explicit ARB exception and asset-owner acceptance of the residual exposure before deployment. Any additional technology must follow the approved technology and exception process.

**Deployment evidence:** Asset inventory and ownership; client deployment and trust-boundary diagram; encryption and key-custody design; access matrix; artifact integrity controls; tests of unauthorized retrieval/export; runtime and administrator exposure assessment; support and offboarding procedures; and any approved exceptions.

### ARB review questions

Whose intellectual assets does this solution use or create? Where could they be disclosed or reused? What evidence shows that access, external processing and distribution are authorized? Can client administrators extract code, containers, prompts or skills, and how is that exposure controlled or explicitly accepted?

---

## How this document is used by the skills

- `arb-generate-architecture` reads each principle and uses it as a constraint when proposing a design.
- `arb-review-architecture` walks this list as the first review pass and produces a row in the recommendations table per principle.
