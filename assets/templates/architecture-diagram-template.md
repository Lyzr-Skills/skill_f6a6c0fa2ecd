# Architecture Diagram Template (Mermaid / C4)

The `arb-generate-architecture` skill produces three diagrams per design. Use these as the default starting templates. All diagrams use **Mermaid** so they render inline in JIRA, Confluence, GitHub, and the IDE.

---

## 1. C4 Context (Level 1) — who uses the system and what it depends on

```mermaid
C4Context
  title System Context — <SOLUTION_NAME>

  Person(end_user, "End User", "Persona / role")
  Person(admin, "Administrator", "Internal operator")

  System(this, "<SOLUTION_NAME>", "Brief purpose statement")

  System_Ext(idp, "EXL Identity Provider", "SSO + MFA")
  System_Ext(monitoring, "EXL Observability Platform", "Logs / metrics / traces")
  System_Ext(client_api, "Client System", "Upstream / downstream system")

  Rel(end_user, this, "Uses", "HTTPS")
  Rel(admin, this, "Administers", "HTTPS / SSO")
  Rel(this, idp, "Authenticates via", "OIDC / SAML")
  Rel(this, monitoring, "Emits telemetry to", "OTLP")
  Rel(this, client_api, "Integrates with", "REST / Event")
```

---

## 2. C4 Container (Level 2) — the deployable units inside the system

```mermaid
flowchart LR
  subgraph Public[" Public Edge "]
    cdn[CDN + WAF]
  end
  subgraph Ingress[" Trusted Ingress "]
    apigw[API Gateway]
  end
  subgraph App[" Application Tier (Private) "]
    web[Web BFF]
    svc1[Domain Service A]
    svc2[Domain Service B]
    worker[Async Worker]
  end
  subgraph Data[" Data Tier (Private) "]
    db[(Primary DB)]
    cache[(Cache)]
    queue[[Message Bus]]
    objstore[(Object Store)]
  end
  subgraph Platform[" Platform Services "]
    secrets[Secrets Manager]
    kms[KMS / Key Vault / Cloud KMS]
    obs[Observability]
  end

  user((User)) --> cdn --> apigw
  apigw --> web --> svc1 & svc2
  svc1 --> db
  svc1 --> cache
  svc2 --> queue --> worker
  worker --> objstore
  App -. secrets/keys .-> secrets & kms
  App -. telemetry .-> obs
```

---

## 3. Sequence — the critical request flow

```mermaid
sequenceDiagram
  autonumber
  participant U as User
  participant E as CDN/WAF
  participant G as API Gateway
  participant S as Domain Service
  participant K as KMS
  participant D as Database

  U->>E: HTTPS request (TLS 1.3)
  E->>G: Forward (mTLS)
  G->>G: Validate JWT (OIDC)
  G->>S: Authenticated call
  S->>K: Decrypt envelope key (CMK)
  S->>D: Query (encrypted at rest)
  D-->>S: Result
  S-->>G: Response
  G-->>E: Response
  E-->>U: Response
```

---

## Conventions used by the generator skill

- **Trust zones**: every container diagram has `Public`, `Trusted Ingress`, `Application`, `Data`, `Platform` zones. Anything crossing a boundary is annotated with the protocol and auth mechanism.
- **Data classification**: labels (`[PII]`, `[Regulated]`, `[Internal]`, `[Public]`) decorate stores so reviewers can spot residency / encryption gaps.
- **Provider-neutral naming**: containers use generic terms (API Gateway, Cache, Message Bus). The provider-specific service is named in the SDD's component table, not the diagram.
- **No secrets in diagrams.** Show *that* a secret store exists, never *which* secret.
