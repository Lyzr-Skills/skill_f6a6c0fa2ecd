# EXL Approved AWS Services

Authoritative list of AWS services approved for use in EXL solutions.

> Status legend: **Recommended** = preferred default · **Approved** = use freely · **Restricted** = approved with conditions · **Deprecated** = remove from new designs · **Prohibited** = do not use.

> Maintained by: EXL Office of the Chief Architect + Cloud CoE. Update via PR.

---

## Compute

| Service | Status | Notes / Conditions |
|---|---|---|
| AWS Lambda | Recommended | Default for event-driven and short-lived work |
| AWS Fargate (ECS / EKS) | Recommended | Default for containerized services |
| Amazon ECS | Approved | Use Fargate launch type |
| Amazon EKS | Approved | Required for portable Kubernetes workloads; managed node groups or Fargate |
| AWS App Runner | Approved | Simple HTTPS services |
| Amazon EC2 | Restricted | Only when managed alternative does not meet requirements; document why |
| AWS Batch | Approved | Batch / HPC |
| AWS Elastic Beanstalk | Deprecated | Migrate to ECS Fargate or App Runner |

## Storage

| Service | Status | Notes |
|---|---|---|
| Amazon S3 | Recommended | Block public access ON, default SSE-KMS w/ CMK |
| Amazon EFS | Approved | Shared filesystems |
| Amazon FSx | Approved | Windows, Lustre HPC |
| Amazon EBS (gp3) | Approved | Right-size; encrypt with CMK |
| AWS Backup | Recommended | Centralized backup for RDS, EFS, EBS, DynamoDB, etc. |
| S3 Glacier (all classes) | Approved | Long-term retention per data classification |

## Databases

| Service | Status | Notes |
|---|---|---|
| Amazon Aurora (PostgreSQL / MySQL) | Recommended | Default OLTP; Aurora Serverless v2 for elastic |
| Amazon RDS (Postgres, MySQL, SQL Server, Oracle) | Approved | Multi-AZ required for Tier-1/2 |
| Amazon DynamoDB | Recommended | Default NoSQL key-value; on-demand for variable load |
| Amazon ElastiCache (Redis, Valkey) | Approved | Caching, session store |
| Amazon DocumentDB | Approved | Mongo-compatible needs |
| Amazon Neptune | Approved | Graph use cases |
| Amazon Keyspaces | Approved | Cassandra-compatible |
| Amazon Timestream | Approved | Time-series |
| Amazon Redshift | Recommended | Cloud data warehouse |

## Networking & Edge

| Service | Status | Notes |
|---|---|---|
| Amazon VPC | Recommended | Private subnets default |
| AWS Transit Gateway | Approved | Hub-and-spoke connectivity |
| AWS PrivateLink / VPC Endpoints | Recommended | All AWS-service traffic via endpoints where possible |
| Application Load Balancer | Recommended | HTTPS only; TLS 1.2+; WAF attached for public ALBs |
| Network Load Balancer | Approved | L4 |
| Amazon API Gateway | Recommended | REST / HTTP / WebSocket APIs |
| AWS AppSync | Approved | GraphQL |
| Amazon CloudFront + AWS WAF | Recommended | All public web endpoints |
| AWS Direct Connect | Approved | Hybrid connectivity |
| AWS Site-to-Site VPN | Approved | Hybrid connectivity (lower SLA) |
| Classic Load Balancer | Deprecated | Migrate to ALB/NLB |

## Security & Identity

| Service | Status | Notes |
|---|---|---|
| IAM Identity Center | Recommended | SSO entry point |
| AWS IAM (Roles, Policies) | Recommended | Workload identity |
| IAM Users | Prohibited | Workloads must use roles; humans must use SSO |
| AWS KMS | Recommended | CMK required for Internal+; auto-rotation ON |
| AWS Secrets Manager | Recommended | Rotation required for credentials |
| AWS Systems Manager Parameter Store | Approved | Non-secret config |
| AWS Certificate Manager | Recommended | TLS certs |
| AWS WAF | Recommended | All public HTTP(S) ingress |
| AWS Shield Advanced | Restricted | Tier-1 client-facing workloads |
| Amazon GuardDuty | Recommended | Org-wide |
| AWS Security Hub | Recommended | Org-wide |
| Amazon Macie | Recommended | Buckets with regulated data |
| Amazon Inspector | Recommended | EC2, ECR, Lambda |
| AWS Config | Recommended | Org-wide, conformance packs |
| AWS CloudTrail | Recommended | All accounts, multi-region trail, log file validation |

## Integration & Messaging

| Service | Status | Notes |
|---|---|---|
| Amazon SNS / SQS | Recommended | |
| Amazon EventBridge | Recommended | Event bus |
| AWS Step Functions | Recommended | Orchestration |
| Amazon MSK (Managed Kafka) | Approved | When Kafka semantics needed |
| Amazon MQ | Approved | ActiveMQ / RabbitMQ for lift-and-shift |
| AWS AppFlow | Approved | SaaS integrations |

## Analytics & Data

| Service | Status | Notes |
|---|---|---|
| AWS Glue | Recommended | ETL / catalog |
| Amazon Athena | Recommended | Ad-hoc SQL on S3 |
| Amazon EMR | Approved | Spark/Hadoop |
| Amazon Kinesis (Data Streams, Firehose, Analytics) | Approved | Streaming |
| Amazon OpenSearch Service | Approved | Search / observability |
| Amazon QuickSight | Approved | BI |
| AWS Lake Formation | Approved | Lakehouse governance |

## AI/ML

| Service | Status | Notes |
|---|---|---|
| Amazon Bedrock | Recommended | Hosted foundation models; **Claude on Bedrock preferred** |
| Amazon SageMaker | Recommended | Training / hosting / pipelines |
| Amazon Comprehend | Approved | NLP |
| Amazon Textract | Approved | OCR |
| Amazon Rekognition | Restricted | **No facial recognition / facial analysis on people** — see EXL responsible AI policy |
| Amazon Transcribe / Translate / Polly | Approved | |

## Observability

| Service | Status | Notes |
|---|---|---|
| Amazon CloudWatch (Logs, Metrics, Alarms) | Recommended | Plus OTel export to central |
| AWS X-Ray | Approved | Tracing (or OTel-based) |
| AWS Managed Grafana | Approved | |
| AWS Managed Prometheus | Approved | |

## DevOps

| Service | Status | Notes |
|---|---|---|
| AWS CodePipeline / CodeBuild / CodeDeploy | Approved | Native CI/CD |
| Amazon ECR | Recommended | Container registry; scan-on-push ON |
| AWS CloudFormation | Approved | When Terraform not feasible |
| AWS CDK | Approved | Where Terraform not feasible; pin construct versions |

## Prohibited / Restricted

- **Amazon Rekognition Facial Analysis / Identification on people** — Prohibited unless explicitly approved by EXL Responsible AI Board.
- **AWS Snowmobile** — Restricted (engagement-specific approval).
- **AWS IoT 1-Click** — Deprecated.

---

## How this document is used by the skills
- `arb-generate-architecture` selects services **only from this list** when proposing an AWS architecture.
- `arb-review-architecture` flags any service in an SDD not listed here as **Major** (or **Blocker** if Prohibited).
