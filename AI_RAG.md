# DevSecOps Control Center — AI and Dynamic RAG

## Objective
Provide grounded operational intelligence from the first product release.

The assistant answers questions over dynamic DevSecOps data while respecting identity, role, application scope, environment scope, and audit requirements.

## Initial user stories

### Versions
“¿Cuáles fueron las últimas versiones de mis aplicaciones?”
Return visible applications, recent releases, environment reached, current production version, and evidence.

### Compliance
“¿La versión 2.4.1 está en cumplimiento?”
Resolve release, required controls, actual executions, exceptions, and readiness reason.

### Technical debt
“¿Qué aplicaciones tienen deuda técnica?”
Retrieve latest debt snapshots, freshness, relevant metrics, and evidence.

### Combined query
“Muéstrame las últimas versiones en producción que tengan deuda técnica o vulnerabilidades críticas.”
Use structured filtering first, evidence retrieval second, and LLM summarization last.

## Two-lane retrieval

### Structured retrieval
For deterministic facts:
- applications
- releases
- deployments
- security evaluations
- technical debt
- compliance
- health
- approvals

### Semantic retrieval
For explanatory content:
- release notes
- scan reports
- control descriptions
- policies
- evidence
- incident summaries
- runbooks

The platform must not ask a vector store questions that SQL/domain services can answer exactly.

## Pipeline
```text
Question
  |
Intent Classification
  |
Entity Resolution
  |
Authorization Scope
  |
Query Plan
  +-------------------+
  |                   |
Domain Queries     Semantic Search
  |                   |
  +---------+---------+
            |
       Evidence Set
            |
       Context Builder
            |
        LLM Gateway
            |
   Grounded Answer
            |
       Audit Record
```

## Authorization-aware retrieval
Before retrieval:
`Identity → Entra roles → internal permissions → application scope → environment scope`.

The LLM never decides authorization.

## Evidence contract
```json
{
  "sourceType": "release",
  "sourceId": "rel-123",
  "applicationId": "app-001",
  "timestamp": "2026-08-12T15:30:00Z",
  "factType": "production-version",
  "displayLabel": "Payments API 2.4.1"
}
```

## Freshness
Evidence carries source timestamp, ingestion timestamp, and optional staleness threshold.

The UI should expose freshness for operational answers.

## Conversation context
May include current application, release, environment, and date range.

Prior chat text is not evidence. Every factual answer resolves against current data.

## Untrusted content
Repository text, logs, tickets, scan reports, policies, and runbooks are data, never instructions.

The AI layer must isolate retrieved content and prevent prompt injection from overriding application rules.

## Initial API
`POST /api/v1/intelligence/query`

Example:
```json
{
  "question": "¿Qué aplicaciones tienen deuda técnica?",
  "context": {
    "applicationId": null,
    "environment": "production"
  }
}
```

Response:
```json
{
  "queryId": "aiq-10028",
  "answer": "Three production applications have current technical debt...",
  "facts": [],
  "evidence": [],
  "freshness": {
    "evaluatedAt": "2026-08-12T15:30:00Z"
  }
}
```

## Initial guardrails
- read-only
- no operational tool execution
- no secret retrieval
- no unrestricted raw SQL generation/execution
- query plans use approved repositories/services
- evidence required for factual claims
- explicit insufficient-evidence response
- audit every query
- configurable conversation retention

## Evolution
Phase 2: richer semantic evidence, incident correlation, release risk scoring.
Phase 3: recommendations and approved runbook suggestions.
Phase 4: tool-enabled actions through the Action Plane with policies, approvals, execution verification, and audit.
