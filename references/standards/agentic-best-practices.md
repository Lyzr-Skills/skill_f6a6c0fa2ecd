# EXL Agentic Best Practices

Reference for designing, reviewing, and operating GenAI and agentic systems at EXL. Applies whenever a solution uses LLMs, foundation models, RAG, tools, or multi-step autonomous workflows.

> Maintained by: EXL Office of the Chief Architect + Responsible AI Board.

---

## 1. Evaluation-Driven Design (EDD)

**Principle**: build the eval set before the agent. Treat eval results as the unit test of the agentic system.

### Required artifacts before build
- **Eval set** — versioned, in source control, ≥50 representative examples for v0, ≥200 for prod.
- **Metrics** — explicit per use case:
  - **Task success** (binary or rubric, judged by LLM-as-judge with human-validated calibration set)
  - **Faithfulness / groundedness** (RAG)
  - **Safety** (refusal/jailbreak resistance, PII leak rate)
  - **Cost** ($ per task)
  - **Latency** (p50 / p95)
  - **Tool-use correctness** (right tool, right arguments)
- **Target thresholds** — published before training/prompting begins. e.g. *"≥90% task success on calibration set, ≤0.5% PII leak, p95 ≤ 8s, ≤ $0.05/task"*.
- **Eval cadence** — every PR runs the smoke eval (5-10 cases); main runs the full eval; weekly run against drift-detection set.

### Build loop
1. Write the eval first.
2. Build the simplest thing that passes it.
3. Improve prompts / tools / retrieval until thresholds met.
4. Add the failure mode to the eval set when a new bug is found — never fix without an eval.

### Anti-patterns
- "Vibes-based" prompting iteration without metrics.
- Evals only on the happy path.
- LLM-as-judge with no human calibration.
- One eval set that never grows.

---

## 2. Human-in-the-Loop (HITL)

**Principle**: humans approve actions that are irreversible, costly, customer-visible, or regulated. The agent's job is to do the preparation; the human's job is to give the green light.

### When HITL is mandatory at EXL
- Customer communications (email, chat, calls)
- Financial transactions, refunds, credits
- Account creation, account closure, permission changes
- Anything affecting client data classification ≥ Client-Confidential
- Any irreversible operation in production (delete, force-push, drop, send)
- Any output from a model used in a regulated workflow (claims, underwriting, KYC, AML, healthcare adjudication)

### HITL patterns
- **Pre-action approval** — agent stages the action and presents a diff/summary; human approves before execution. Default.
- **Post-action review** — agent acts; human reviews within an SLA; rollback path required. Use only for reversible, low-blast-radius actions.
- **Tiered confidence** — agent auto-executes high-confidence; routes low-confidence to a human queue. Confidence must be calibrated against historical outcomes.
- **Sample-and-review** — agent acts on N; human reviews K of N. For high-volume reversible tasks; ratio set by risk.

### Implementation requirements
- Approval action logged with: reviewer ID, timestamp, agent rationale, tool calls intended, the exact payload that will be submitted.
- Approval UI shows the *exact* outbound payload — not a paraphrase.
- Time-out behavior is fail-safe (default = do nothing, not = auto-approve).
- Bulk approvals (e.g. approving 50 items) require an explicit "I have reviewed each" checkbox; not a single-click.

---

## 3. EXLerate platform alignment

**Principle**: build on the EXL agentic platform unless a documented exception justifies custom build. Reuse compounds; bespoke fragments.

EXLerate provides shared capabilities that every agentic build should attempt to reuse:
- Managed model gateway (multi-model routing, content filters, prompt-injection defenses, regional model placement)
- Eval harness and dashboard
- HITL approval queues and audit trail
- Tool registry (vetted tools with signed manifests)
- Retrieval / RAG building blocks (chunking, embeddings, vector store, evaluators)
- Observability for LLM/tool calls (token usage, cost, latency, eval drift)
- Model registry and prompt version control

### Required SDD section (§11 of IEEE 1016 template)
- Which EXLerate capabilities are reused
- Which are not, and why (the "build" justification)
- Migration plan to fold custom pieces back into EXLerate when capability matures

> If EXLerate-specific capability names, endpoints, or SDK references are needed in this document, update this section with current platform documentation links. Maintained by the EXLerate Platform team.

---

## 4. Safety and guardrails

### Prompt-boundary controls
- **PII redaction** at the prompt boundary — never send Client-Confidential or higher to a model unless the model is in the approved data-residency zone *and* the contract permits.
- **Prompt-injection defenses**: separate untrusted content (tool results, retrieved docs, web pages) from user instructions; treat as data, never as instructions.
- **Output validation**: schema-validate structured outputs; block or quarantine outputs containing PII the model wasn't given.

### Tool-use safety
- **Allow-listed tools only.** Tools are registered, versioned, signed.
- **Tool arg validation** at runtime (schemas, regex, allow-lists).
- **Tool blast-radius limits**: read tools are unrestricted; write tools require HITL above defined thresholds.
- **No "shell" / generic-execution tools** in production agents. If unavoidable, sandboxed and HITL-gated.

### Content safety
- Use cloud-native content filters (Bedrock Guardrails / Azure AI Content Safety / Vertex Safety Filters / Model Armor) for every public-facing or customer-facing agent.
- Refusal-quality is part of the eval set, not an afterthought.

---

## 5. Observability for agents

Every production agent emits:
- **Trace** spanning user input → prompt → tool calls → model output → action → result. Use OpenTelemetry with GenAI semantic conventions.
- **Token usage and cost** per turn and per session.
- **Tool-call telemetry**: which tool, args (with PII redacted), success/failure, latency.
- **Eval drift**: nightly run against the drift set; alert when any metric falls below the drift threshold published in the agent's eval targets (§1).
- **Cache metrics** (prompt-cache hit rate) where prompt caching is used, visible in dashboards.
- **Budget telemetry**: steps, tool calls and spend per task against the limits set in §8.

Dashboards required per agent:
- Cost-per-task trend
- Task success-rate trend
- HITL approval queue depth + age
- Refusal / safety-filter trigger rate
- Latency p50 / p95 / p99

---

## 6. Model and prompt governance

- **Model registry**: every model in use is registered (vendor, version, region, data-residency, owner, intended use cases, restrictions).
- **Prompt registry**: production prompts are versioned alongside code. No production traffic on un-versioned prompts.
- **Model change-control**: switching model version is a deploy event with eval re-run. Document the eval-result delta in the deployment record.
- **Default to the latest approved model** in the model registry unless a client-specific contract pins an older version. Record the pinned version and the reason in the registry.
- **Enable prompt caching where the provider supports it** (for example Anthropic `cache_control`, Bedrock prompt caching) for the system prompt and stable context blocks.

---

## 7. Responsible AI

- No facial identification of individuals without Responsible AI Board approval (across AWS Rekognition, Azure AI Vision Face, GCP Vision API).
- High-stakes decisions (credit, insurance, healthcare, employment) require model cards, bias evaluation, contestability path, and HITL.
- Disclose AI involvement to end users in customer-facing surfaces.
- Provide an explainability surface for any automated decision a human can challenge.

---

## 8. Agent autonomy, identity and resilience

*Added after comparison with the AWS Well-Architected Agentic AI Lens, Amazon Bedrock AgentCore guidance, LangGraph production practice and the OWASP Top 10 for Agentic Applications. Pending ratification by the Office of the Chief Architect and the Responsible AI Board.*

### 8.1 Autonomy tiers
**Principle**: classify every agent by autonomy and apply controls in proportion. Prefer a fixed workflow over an autonomous agent where the task allows; raise autonomy only when task complexity requires it.

| Tier | Behaviour | Minimum controls |
|---|---|---|
| T0 Observer | Reads and summarises; takes no action | Evals, tracing, PII redaction |
| T1 Assistant | Drafts or recommends; a human acts | T0 plus output validation and disclosure to users |
| T2 Autonomous | Calls tools and acts within a bounded scope | T1 plus §2 HITL triggers, §4 tool controls, §8.2 to §8.5 |
| T3 Orchestrator | Directs other agents or delegates authority | T2 plus handoff contracts, inter-agent authentication and cascade limits |

The SDD states the tier of each agent and the reasoning behind it.

### 8.2 Scope and decomposition
- Each agent has a single declared purpose, an explicit scope, stated limits and a named owner.
- Prompts, tool catalogues, role definitions and policies are versioned artifacts (see §6).
- Multi-agent designs define handoff contracts (schemas, success criteria) and what happens when a downstream agent fails.
- Third-party MCP servers and external agents are vetted and registered like any other tool (§4) before use.

### 8.3 Identity and policy enforcement
- Each agent has its own workload identity (principle 5). Agents never hold long-lived credentials; secrets are brokered outside the prompt.
- Actions on behalf of a user carry delegated user context and never exceed that user's rights.
- Tool access is enforced by a deterministic policy layer outside the model and agent code (for example the EXLerate gateway, or AgentCore Policy with Cedar). The prompt is not a security boundary.
- Roll new policies out in log-only mode first where the platform supports it.

### 8.4 Durability and cost control
- Long-running agents use a durable checkpoint store, not in-memory state.
- Tools with side effects are idempotent (idempotency keys) or verify before retrying, because replay after a checkpoint re-executes later steps.
- Each task has hard limits on steps, tool calls, tokens and spend, plus loop detection.
- Every production agent has a kill switch and a rollback path for prompts, tools and models.

### 8.5 Memory
- State what the agent persists, for how long and why. Memory is isolated per tenant and user.
- No Client-Confidential or higher data or PII in long-term memory unless approved and retention-limited.
- Memory writes from untrusted content are validated, to reduce memory poisoning.

### 8.6 Threat model and testing
- The SDD maps the design to the OWASP Top 10 for Agentic Applications (goal hijack, tool misuse, identity and privilege abuse, supply chain, code execution, memory poisoning, inter-agent communication, cascading failure, human-agent trust, rogue agents) and the OWASP Top 10 for LLM Applications.
- Adversarial testing (prompt injection, tool abuse, data exfiltration) is completed before production and repeated on material change. Findings feed the eval set (§1).

---

## References
- AWS Well-Architected Agentic AI Lens: https://docs.aws.amazon.com/wellarchitected/latest/agentic-ai-lens/design-principles.html
- AWS Well-Architected Generative AI Lens: https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/design-principles.html
- OWASP GenAI Security Project, Top 10 for Agentic Applications (December 2025)
- NIST AI Risk Management Framework

---

## How this document is used by the skills
- `arb-assistant` reads this document when principle 12 applies (the design has an AI or agentic part) and judges the SDD against sections 1 to 8.
- `arb-generate-architecture` includes a §11 (AI/Agentic) section in every SDD whose design uses GenAI, and proposes EXLerate reuse first.
- `arb-review-architecture` walks sections 1-8 of this doc for any SDD whose §11 is non-empty, producing one recommendation per section.
