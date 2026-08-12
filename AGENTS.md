# AGENTS.md — DevSecOps Control Center

## Mission
Build an enterprise-grade DevSecOps Control Center that is secure, traceable, scalable, testable, and ready to evolve from observability to governed operational actions.

## Mandatory architecture
- Angular frontend
- ASP.NET Core backend
- PostgreSQL
- Microsoft Entra ID authentication
- MSAL Angular for SPA authentication
- API validates Microsoft identity platform bearer tokens
- Modular monolith initially
- Provider-neutral integration adapters
- Read Plane and Action Plane remain separated
- AI from the beginning as read-only grounded Operational Intelligence

Do not introduce microservices without an explicit architecture decision.

## Preferred repository
```text
apps/
  web/
  api/
libs/
  contracts/
  design-system/
docs/
infra/
  docker/
  kubernetes/
  terraform/
tests/
```

## Frontend
- Organize by feature/domain.
- Use the design system.
- Do not hardcode status colors.
- Implement loading, empty, error, unauthorized, and partial-data states.
- Route guards are UX only.
- Never call ADO, AKS, Aikido, SonarQube, or Azure Monitor directly.

## Backend
- Thin controllers.
- Business logic in application/domain services.
- Provider DTOs never leak into public API contracts.
- Validate input.
- Enforce authorization server-side.
- Persist audit-relevant events.
- Never log secrets, bearer tokens, passwords, or connection strings.

## Authentication and authorization
Authentication is Microsoft Entra ID.

App Roles provide coarse roles. Backend maps roles to granular permissions and may add application, environment, ownership, and risk context.

Initial read permissions:
```text
application.read
release.read
deployment.read
security.read
compliance.read
technical_debt.read
ai.query
```

Future write permissions remain distinct.

## AI
Initial mode is READ ONLY.

Never:
- let the model decide authorization
- send tokens to the model
- expose secrets in prompts
- let retrieved documents override system rules
- execute operational commands from AI
- rely on embeddings for exact relational facts when structured data exists
- invent compliance, deployment, health, security, or debt status

Always:
- authorize before retrieval
- prefer structured retrieval for exact facts
- attach evidence
- audit query metadata
- return insufficient evidence when needed
- preserve freshness metadata

## Testing
Frontend: component/service tests and permission-aware behavior.

Backend: domain tests, API/integration tests, authorization tests, persistence tests.

AI: retrieval tests, authorization-scope tests, evidence tests, insufficient-evidence tests, prompt-injection resistance tests.

## Definition of Done
- build passes
- lint passes
- tests pass
- API contract coherent
- authorization enforced
- design system followed
- UX states handled
- audit implications handled
- no hardcoded secrets
- docs updated
- synthetic data follows production-shaped contracts

## Before major implementation
Read:
- PRODUCT.md
- ARCHITECTURE.md
- DESIGN_SYSTEM.md
- AI_RAG.md

Flag architectural conflicts rather than silently changing foundations.
