# DevSecOps Control Center — Product Definition

## Vision
DevSecOps Control Center is an enterprise platform for delivery intelligence, security posture, compliance, operational health, and governed operational actions.

The platform evolves through three capability levels: **Observe → Understand → Act**.

AI is included from the first release as an Operational Intelligence capability, not as a later chatbot add-on.

## Questions the product must answer
- What version of each application is deployed in each environment?
- What release, commit, pipeline, artifact, and approval produced that version?
- Is the current version healthy?
- Did the release pass the expected DevSecOps controls?
- Does the application have technical debt, vulnerabilities, exceptions, or failed controls?
- What changed recently?
- What evidence exists for audit and compliance?
- What safe operational action can an authorized operator request in future phases?

## Initial product scope
### Delivery Intelligence
Applications, versions, releases, pipelines, deployments, environments, promotion status, recent activity, release readiness.

### Security Intelligence
Security status per release, vulnerability counts, control execution, technical debt indicators, security exceptions, evidence.

### Operational Intelligence
Application health summary, deployment health, release correlation, AI assistant with dynamic retrieval.

### Governance Foundations
Microsoft Entra ID authentication, app roles and permissions, audit events, environment-aware authorization, Read Plane separated from Action Plane.

## AI capability from day one
The first increment must include an AI Assistant capable of answering questions grounded in current platform data.

Examples:
- ¿Cuáles fueron las últimas versiones de Payments API?
- ¿Qué versión está actualmente en producción?
- ¿La versión 2.4.1 cumple los controles requeridos?
- ¿Qué controles fallaron en el último release?
- ¿Qué aplicaciones tienen deuda técnica?
- ¿Qué vulnerabilidades críticas están asociadas a versiones productivas?
- ¿Qué cambió entre las versiones 2.3.8 y 2.4.1?

The assistant must include evidence references to releases, deployments, security evaluations, technical debt snapshots, policies, or other source records.

## Initial non-goals
- Autonomous remediation
- Direct AI execution against AKS or Azure
- Full ITSM replacement
- General-purpose enterprise chatbot
- Microservice decomposition

## Product principles
1. Evidence over assumptions.
2. Every release is traceable.
3. The UI never talks directly to external operational systems.
4. External data is normalized into the Control Center domain.
5. Authentication and authorization are enforced server-side.
6. Production actions require policy evaluation.
7. AI responses are grounded and auditable.
8. The same design system is used across the product.
9. Operational actions are reversible where technically possible.
10. Synthetic data uses production-shaped contracts.

## Foundation release success criteria
- Sign-in with Microsoft Entra ID.
- Backend bearer-token validation.
- Role and permission model represented.
- Overview consumes backend API data.
- Applications, environments, releases, and deployments persist in PostgreSQL.
- Synthetic seed data available.
- AI answers questions about versions, compliance, security controls, and technical debt.
- AI answers expose evidence.
- Relevant AI and security interactions are auditable.
