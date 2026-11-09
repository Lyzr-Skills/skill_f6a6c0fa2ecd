# Technology catalogue

Technologies the ARB sees often that are **not** on `approved-libraries.md` or the approved service lists. A checker that finds one reports it with the status below, so an author can see it needs a decision.

Statuses:
- **Requires review**: not approved. The author needs an ARB exception with a rationale, or a listed alternative. Treat as Major.
- **Named in principles**: not on the lists but named in `exl-architecture-principles.md` as acceptable. Not a finding.

This catalogue is advisory. It does not approve anything. A name missing here is not approved either: the approved lists remain the only source of approval. The ARB owns this file; add a row when a new technology turns up in a review.

| Technology | Category | Status | Aliases | Note |
|---|---|---|---|---|
| Langfuse | LLM observability | Requires review | - | Not on the approved lists |
| LiteLLM | AI gateway | Requires review | - | Not on the approved lists |
| Azure AI Agent Service | AI agent runtime | Requires review | AI Agent Service | Azure OpenAI Service is listed; this service is not |
| Vertex AI | AI platform (GCP) | Requires review | - | No approved GCP service list |
| OpenAI API | Model API | Requires review | - | Direct provider API, not a listed cloud service |
| Hugging Face | Model hub | Requires review | HuggingFace | Model and licence provenance needs review |
| Ollama | Model runtime | Requires review | - | - |
| vLLM | Model runtime | Requires review | - | - |
| CrewAI | Agent framework | Requires review | - | - |
| AutoGen | Agent framework | Requires review | - | - |
| Semantic Kernel | Agent framework | Requires review | - | - |
| Copilot Studio | Low-code agents | Requires review | - | - |
| Jenkins | CI/CD | Requires review | - | GitHub Actions on Azure is the listed CI/CD service |
| GitLab CI | CI/CD | Requires review | GitLab | - |
| CircleCI | CI/CD | Requires review | - | - |
| ArgoCD | CI/CD | Requires review | Argo CD | - |
| SonarQube | Code scanning | Requires review | - | - |
| Fortify | Code scanning | Requires review | Micro Focus Fortify | - |
| Snyk | Code scanning | Requires review | - | - |
| Trivy | Image scanning | Requires review | - | - |
| Datadog | Observability | Requires review | - | OpenTelemetry SDK is listed; Datadog is not |
| Splunk | Observability | Requires review | - | - |
| New Relic | Observability | Requires review | - | - |
| Loki | Observability | Requires review | Grafana Loki | - |
| Neo4j | Graph database | Requires review | - | - |
| MongoDB | Database | Requires review | - | Azure Cosmos DB is the listed NoSQL default |
| Elasticsearch | Search | Requires review | - | - |
| Pinecone | Vector database | Requires review | - | - |
| Weaviate | Vector database | Requires review | - | - |
| Snowflake | Data platform | Requires review | - | - |
| Celery | Task queue library | Requires review | - | - |
| Okta | Identity | Requires review | - | Microsoft Entra ID is the listed identity service |
| Auth0 | Identity | Requires review | - | - |
| Keycloak | Identity | Requires review | - | - |
| Power Automate | Low-code | Requires review | - | - |
| UiPath | RPA | Requires review | - | - |
| MuleSoft | Integration | Requires review | - | - |
| Terraform | Infrastructure as code | Named in principles | - | Preferred IaC in principle 5; not a finding |
| Bicep | Infrastructure as code | Named in principles | - | Provider-native IaC allowed in principle 5; not a finding |
| Open Policy Agent | Policy as code | Named in principles | OPA | Named in principle 5; not a finding |
