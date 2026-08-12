# DevSecOps Control Center — Architecture

## Baseline
- Frontend: Angular
- Backend: ASP.NET Core Web API
- Database: PostgreSQL
- Authentication: Microsoft Entra ID
- SPA authentication: MSAL Angular
- API protection: Microsoft identity platform bearer tokens
- AI: provider-neutral application service
- Retrieval: hybrid structured + semantic retrieval
- Observability: OpenTelemetry
- Runtime target: containers / AKS
- Secrets: Azure Key Vault
- CI/CD: Azure DevOps

Initial style: **modular monolith**. Do not introduce microservices without an explicit architecture decision.

## High-level architecture
```text
Users
  |
Angular SPA
  |
OIDC/OAuth2 + PKCE
  |
Microsoft Entra ID
  |
access token
  |
Control Center API
  +----------------------+----------------------+
  |                      |                      |
Query Plane        AI Intelligence        Action Plane
  |                      |                      |
  +-----------+----------+                Policy Engine
              |                                 |
          Domain Modules                    Approvals
              |                                 |
          PostgreSQL                        Executors
              |
       Integration Layer
              |
   Azure DevOps | AKS | Aikido | SonarQube | Azure Monitor
```

## Read Plane vs Action Plane
The platform separates query/read operations from operational commands.

Read Plane: dashboards, releases, security, compliance, health, audit, AI retrieval.

Action Plane, future: restart, scale, redeploy, rollback, runbook execution, configuration changes.

No UI component and no AI component may invoke AKS, Azure DevOps, or operational systems directly.

Future actions follow:
`Request → Authentication → Authorization → Policy → Approval → Execution → Verification → Audit`.

## Modules
```text
Identity
Applications
Environments
Releases
Deployments
Pipelines
Security
Compliance
Health
TechnicalDebt
Evidence
Audit
Intelligence
Automation
Integrations
```

## Core domain
```text
Application
  |- Repository
  |- Environment[]
  |- Release[]
  |- Deployment[]
  |- SecurityEvaluation[]
  |- TechnicalDebtSnapshot[]
  |- HealthEvaluation[]
  |- Evidence[]

Release
  |- Version
  |- Commit
  |- PipelineRun
  |- Artifact
  |- SecurityEvaluation
  |- ComplianceEvaluation
  |- Deployment[]

Deployment
  |- Environment
  |- Release
  |- Approval[]
  |- HealthEvaluation
  |- Evidence[]
```

## Microsoft Entra ID
Angular authenticates using MSAL Angular. Prefer Authorization Code Flow with PKCE for the SPA.

ASP.NET Core validates Microsoft identity platform access tokens. Frontend guards improve UX only. They are not a security boundary.

## Authorization
Use Entra ID App Roles for coarse roles, mapped by the backend to granular permissions.

Initial roles:
- Viewer
- Developer
- ApplicationOwner
- DevOpsOperator
- DevSecOpsEngineer
- SecurityReviewer
- Auditor
- Approver
- PlatformAdministrator

Initial permissions:
```text
application.read
release.read
deployment.read
security.read
compliance.read
technical_debt.read
ai.query
deployment.execute
deployment.rollback
workload.restart
workload.scale
runbook.execute
security.exception.request
security.exception.approve
production.approve
administration.manage
```

Authorization must be able to add application ownership, team, environment, and action risk as contextual policy inputs.

## Persistence
PostgreSQL is the normalized operational system of record.

Initial entities:
applications, repositories, environments, releases, artifacts, pipeline_runs, deployments, approvals, security_evaluations, compliance_evaluations, technical_debt_snapshots, health_evaluations, evidence, audit_events, ai_conversations, ai_queries, ai_evidence_links.

## Integrations
Hide provider schemas behind adapters such as:
```text
IDeliveryProvider
ISourceControlProvider
ISecurityProvider
IHealthProvider
IChangeManagementProvider
```

Initial providers may include Azure DevOps, Kubernetes, Aikido, SonarQube, Azure Monitor.

## Ingestion
Prefer:
`External Sources → Collectors → Normalization → PostgreSQL → Query API`.

Avoid building dashboards from many synchronous third-party calls.

## AI / Dynamic RAG
```text
Question
  |
AI Query API
  |
Authorization Context
  |
Query Planner
  +---------------------+
  |                     |
Structured Retrieval   Semantic Retrieval
(SQL/domain services)  (documents/evidence)
  |                     |
  +----------+----------+
             |
        Context Builder
             |
         LLM Gateway
             |
  Grounded Answer + Evidence
             |
         Audit Event
```

Structured facts such as current version, environment, deployment status, vulnerability counts, control result, and technical-debt metrics must come from structured sources whenever possible.

Semantic retrieval is for release notes, policies, evidence documents, scan explanations, runbooks, and incident summaries.

## Dynamic RAG rule
Example: “¿Cuáles fueron mis últimas versiones y cuáles tienen deuda técnica?”

Planner:
1. Resolve applications accessible to the user.
2. Query recent releases.
3. Join latest technical-debt snapshot.
4. Retrieve supporting evidence if explanation is needed.
5. Generate a grounded answer.

## AI security
The AI module must:
- inherit authenticated authorization scope
- never bypass server authorization
- never receive access tokens
- redact secrets before model invocation
- persist query audit metadata
- expose evidence identifiers
- distinguish fact from interpretation
- return insufficient-evidence responses when required

Initial AI capability is **read-only**.

## Initial API
```text
GET  /api/v1/applications
GET  /api/v1/applications/{id}
GET  /api/v1/applications/{id}/releases
GET  /api/v1/releases/{id}
GET  /api/v1/deployments
GET  /api/v1/environments
GET  /api/v1/security/summary
GET  /api/v1/technical-debt
POST /api/v1/intelligence/query
GET  /api/v1/intelligence/queries/{id}
```
