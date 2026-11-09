# EXL Approved Libraries & Frameworks

Authoritative list of libraries and frameworks approved for use in EXL solutions. Anything not on this list requires an ARB exception with rationale and security review.

> Status legend: **Approved** = use freely · **Recommended** = preferred default · **Restricted** = approved but only for the stated use case · **Deprecated** = remove from new designs · **Prohibited** = do not use.

> Version scope: Supported and deprecated library versions are not tracked here. Existing version-specific security exclusions remain applicable.

> Maintained by: EXL Office of the Chief Architect + AppSec. Update via PR.

---

## Languages & Runtimes

| Language | Status | Notes |
|---|---|---|
| Python | Recommended | Default for data, ML, scripting, agentic |
| Java | Approved | Default for enterprise services |
| TypeScript | Recommended | Default for frontend and Node backends |
| Go | Restricted | Networking, CLI, high-throughput services |
| Rust | Restricted | Networking, CLI, high-throughput services |
| C# / .NET | Restricted | Microsoft-shop client engagements |
| Node.js | Approved | TS runtime, BFF tier |

---

## Backend Frameworks

| Framework | Status | Notes |
|---|---|---|
| FastAPI (Python) | Recommended | REST/async APIs, default Python web framework |
| Spring Boot (Java) | Recommended | Enterprise Java services |
| Express + TypeScript (Node) | Approved | BFF, lightweight services |
| NestJS (Node) | Approved | Structured Node services |
| Django (Python) | Restricted | Internal tools / admin UIs only — not for high-throughput APIs |
| Flask (Python) | Deprecated | Migrate to FastAPI |

---

## Frontend Frameworks

| Framework | Status | Notes |
|---|---|---|
| React | Recommended | Default for SPAs |
| Angular | Approved | Existing Microsoft-shop client codebases |
| Vue | Restricted | Smaller apps |
| Next.js | Restriced | SSR / hybrid apps |
| jQuery | Prohibited | Do not introduce in new code |

---

## Data, Caching & Messaging Libraries

| Library | Status | Notes |
|---|---|---|
| SQLAlchemy | Recommended | Python ORM |
| Pydantic | Recommended | Validation, used heavily by FastAPI and agentic |
| Hibernate | Approved | Java ORM |
| Prisma | Approved | TS/Node ORM |
| Airflow | Recommended | ETL Orchestration and Workflows |
| pandas, polars, pyarrow | Approved | Data wrangling |
| Apache Spark (PySpark) | Approved | Distributed data processing |
| dbt | Recommended | SQL-based transformations |
| Apache Kafka client (confluent-kafka, kafka-python) | Approved | |
| Redis (redis-py, lettuce) | Approved | |
| RabbitMQ (pika, amqp-client) | Approved | |

---

## Agentic & AI Libraries

| Library | Status | Notes |
|---|---|---|
| Anthropic SDK (`anthropic`, `@anthropic-ai/sdk`) |Approved | Default Claude SDK; **enable prompt caching** |
| OpenAI SDK | Approved | When client mandates GPT models |
| LangGraph | Recommended | Graph-orchestrated agentic workflows |
| LangChain | Recommended | Use for simple agent use cases |
| Strands | Approved| Use for simple agent use cases |
| Microsoft Agent Framework | Approved | Use for simple agent use cases |
| Google Agents SDK | Approved| Use for simple agent use cases |
| LlamaIndex | Restricted| Retrieval / indexing |
| Pydantic AI | Approved | Typed agent flows |
| CrewAI | Restricted | Use Langchain /Langgraph |
| Semantic Kernel| Deprecated | Use Langchain or Microsoft Agent Framework |
| Autogen | Deprecated | Use Langchain or Microsoft Agent Framework |
| EXLerate AI Gateway | Recommended | Default platform for all LLM access. Use for Guardails, Model Routing and LLM Traces|
| LiteLLM | Recommended | LLM Gateway for model routing, preferred to leverage EXLerate AI gateway to implement |
| Langfuse | Recommended | LLM traces, observability and evaluations, preferred to leverage EXLerate AI gateway to implement |
| AutoGPT / BabyAGI / generic "agentic frameworks" not listed above | Prohibited | Use  LangGraph/Lanchain instead |

---
## Orchestration

| Library | Status | Notes |
|---|---|---|
| Temporal | Recommended | Cloud agnostic workflows and agent orchestration |
| Step Functions | Approved | AWS specific workflows and agent orchestration |
| Azure Logic Apps | Approved | Azure specific workflows and agent orchestration |
---


## Observability

| Library | Status | Notes |
|---|---|---|
| OpenTelemetry SDK (all langs) | Recommended | Default for traces, metrics, logs |
| Prometheus client | Approved | Metrics scrape |
| structlog (Python) | Approved | Structured logging |
| Logback / Log4j (Java) | Approved | Log4j < 2.17 Prohibited (CVE-2021-44228) |
| pino, winston (Node) | Approved | |

---

## Security & Auth

| Library | Status | Notes |
|---|---|---|
| Authlib, python-jose | Approved | OAuth/OIDC clients |
| Spring Security | Recommended | Java auth |
| Passport.js | Approved | Node auth |
| MSAL (Microsoft Authentication Library) | Approved | Azure AD integration |
| boto3 / botocore | Recommended | AWS SDK Python |
| AWS SDK (JS/TS) | Recommended | |
| Azure SDK for Python / .NET / JS | Recommended | |
| Google Cloud SDK | Recommended | |

---

## Testing

| Library | Status | Notes |
|---|---|---|
| pytest | Recommended | Python |
| JUnit | Recommended | Java |
| Jest, Vitest | Recommended | TS/JS |
| Playwright | Recommended | E2E |

---

## CI/CD

| Library | Status | Notes |
|---|---|---|
| Jenkins | Recommended | Python |
| SonarQube | Recommended | Code Quality Scans |
| Foritfy| Recommended | SAST scans |
| Prisma | Recommended | SCA scans |
| Fossa| Recommended |OSS license compliance |
| LaunchDarkly | Prohibited | 3rd party feature flag management |
| UCI Github| Recommended| EXL Self Hosted Enterprise Source Control |
| Terraform| Recommended| Infrastructure as Code |
| Jenkins | Recommended| EXL Self Hosted Enterprise Source Control |
| Gitlab | Prohibited | Source control, use UCI Github for EXL IP |
| AWS CodeCommit | Prohibited | Source control, use UCI Github for EXL IP |
| AWS CodePipeline | Prohibited | CI/CD Pipelines, use Jenkins |
| AWS CloudFormation | Restricted | Infrastructure as Code, use Terraform |
| Bicep| Restricted | Infrastructure as Code, use Terraform |
| Azure DevOps | Restricted | Source control, use UCI Github for EXL IP |

---


## Globally Prohibited

These are blocked across all EXL solutions:
- `lodash` versions <4.17.21 (prototype pollution)
- `log4j` <2.17 (CVE-2021-44228)
- `pyyaml` <5.4 (CVE-2020-14343 if `load()` used) — must use `safe_load`
- `requests` <2.32 (CVE-2024-35195)
- Any library with an unresolved Critical CVE older than 30 days
- Crypto: MD5, SHA-1 for new signing; DES, 3DES, RC4 outright

---

## How this document is used by the skills
- `arb-generate-architecture` selects libraries **only from this list** when proposing a stack.
- `arb-review-architecture` flags any library in an SDD not listed here as **Major** (or **Blocker** if Prohibited / globally prohibited).
