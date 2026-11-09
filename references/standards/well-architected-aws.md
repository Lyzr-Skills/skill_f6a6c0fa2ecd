# AWS Well-Architected Framework — Review Lens for EXL

Use this as the AWS-specific lens during architecture generation and review. Each pillar contains the high-level questions and the EXL-specific guardrails that go beyond the public framework.

> Reference: https://aws.amazon.com/architecture/well-architected/ (Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, Sustainability)

---

## Pillar 1 — Operational Excellence

**Key design questions**
- How is the workload modeled, monitored, and improved over time?
- What runbooks exist? Are they tested?
- How are deployments performed, and how are failures detected and rolled back?

**EXL guardrails**
- CI/CD via AWS CodePipeline/CodeBuild or GitHub Actions with OIDC into AWS (no static IAM keys).
- CloudWatch + OpenTelemetry; central log aggregation in the EXL Observability account.
- Game days / failure injection (FIS) scheduled at least twice per year for Tier-1.
- All operational state (alarms, dashboards, runbooks) defined as code.

---

## Pillar 2 — Security

**Key design questions**
- How is identity managed for both people and machines?
- How are workloads isolated? Data protected in transit and at rest?
- How do you detect, respond to, and recover from security events?

**EXL guardrails**
- IAM Identity Center (SSO) for human access; IAM Roles for service identity; **no IAM users for workloads**.
- All S3 buckets: block public access, default SSE-KMS with CMK, versioning + Object Lock for regulated data.
- VPCs: private subnets default; NAT only for egress where needed; PrivateLink/VPC endpoints for AWS service traffic.
- AWS Config + Security Hub + GuardDuty + Macie enabled organization-wide.
- Secrets in AWS Secrets Manager with automatic rotation; **never** in environment variables for prod.
- KMS keys: CMK with key policies, automatic rotation; multi-region keys only when business need is documented.

---

## Pillar 3 — Reliability

**Key design questions**
- How do you design for failure of any single component?
- How do you recover, and how quickly?
- How is capacity managed?

**EXL guardrails**
- Compute: spread across ≥3 AZs for Tier-1/2; ASGs with health checks; ECS/EKS with PDBs.
- Data: Aurora/RDS Multi-AZ for OLTP; cross-region read replicas for Tier-1; S3 Cross-Region Replication for regulated content.
- DR: AWS Backup with documented RTO/RPO and **tested** restore at least twice per year.
- Quotas: Service Quotas reviewed before launch; alarms on 80% usage.

---

## Pillar 4 — Performance Efficiency

**Key design questions**
- Have you selected the right resource types and sizes?
- How is performance monitored? How does the architecture evolve as load changes?

**EXL guardrails**
- Right-size with Compute Optimizer recommendations; reassess quarterly.
- Choose Graviton (ARM) where the workload supports it.
- Use managed services (Aurora Serverless v2, DynamoDB on-demand, Lambda, Fargate) before self-managed.
- Edge caching for read-heavy public traffic (CloudFront + appropriate TTL strategy).

---

## Pillar 5 — Cost Optimization

**Key design questions**
- How is spend visible at the workload level?
- How are unused or oversized resources reclaimed?
- How are pricing models (RI / Savings Plans / Spot) leveraged?

**EXL guardrails**
- Cost Allocation Tags enforced: `exl:client`, `exl:project`, `exl:cost-center`, `exl:environment`.
- Savings Plans / RIs reviewed quarterly by FinOps.
- Non-prod auto-shutdown nights/weekends (Instance Scheduler / EventBridge).
- Budgets with alerts at 50/80/100% of approved monthly spend.

---

## Pillar 6 — Sustainability

**Key design questions**
- How do you minimize carbon per unit of work?
- Are you using shared, efficient infrastructure (managed/serverless)?

**EXL guardrails**
- Prefer regions with low published carbon intensity when SLA/residency allows.
- Prefer Graviton and serverless for shared-tenant efficiency.
- Track Customer Carbon Footprint Tool reports per account in monthly cloud review.

---

## How this document is used by the skills
- `arb-generate-architecture` walks the six pillars and ensures every design produced includes a section per pillar.
- `arb-review-architecture` produces one recommendation row per pillar (Pass / Concern / Gap) when reviewing AWS workloads.
