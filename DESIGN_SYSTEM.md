# DevSecOps Control Center — Design System

## Direction
**Enterprise Operations Dark UI**

Characteristics:
- high information density
- clear status semantics
- enterprise visual hierarchy
- security operations aesthetic
- restrained animation
- strong accessibility
- fast scanning
- drill-down navigation

## Principles
1. Information hierarchy before decoration.
2. Status must be readable without relying only on color.
3. Every high-level metric should support drill-down.
4. Dense data views must remain legible.
5. Components must work with synthetic and real data.
6. Design for degraded, empty, loading, unauthorized, and partial-data states.
7. Actions must look different from navigation.
8. Production-impacting actions require explicit visual friction.
9. AI answers expose evidence and freshness.
10. Dark theme is primary, but tokens allow future themes.

## Token layers
`Primitive Tokens → Semantic Tokens → Component Tokens`

Token groups:
surface, border, text, accent, status, severity, environment, spacing, typography, radius, elevation, motion, z-index.

Do not hardcode status colors in feature components.

## Semantic vocabularies

Deployment:
pending, queued, running, succeeded, failed, cancelled, rolled_back

Health:
healthy, degraded, unhealthy, unknown

Security:
passed, warning, failed, exempted, not_evaluated

Approval:
not_required, pending, approved, rejected, expired

Promotion:
completed, in_progress, pending, blocked

Severity:
critical, high, medium, low, informational

## Core components
```text
AppShell
Sidebar
Topbar
PageHeader
Breadcrumb
Card
MetricCard
TrendCard
SummaryCard
Badge
StatusBadge
EnvironmentBadge
SeverityBadge
RoleBadge
Button
IconButton
SplitButton
Tabs
SegmentedControl
DataTable
DataGrid
Pagination
ColumnSelector
FilterBar
SearchInput
DateRangePicker
Timeline
ReleaseTimeline
ActivityTimeline
EnvironmentStepper
ProgressBar
ProgressRing
ScoreGauge
Drawer
Modal
Popover
Tooltip
Alert
Toast
Notification
Skeleton
LoadingState
EmptyState
ErrorState
UnauthorizedState
PartialDataState
Avatar
UserMenu
AIQueryBar
AIAssistantPanel
AIAnswer
AIEvidenceCard
AISourceChip
```

## Navigation
```text
Overview

Delivery
  Applications
  Pipelines
  Environments
  Deployments
  Releases

Security
  Security Overview
  Vulnerabilities
  Controls
  Exceptions

Operations
  Health
  Workloads
  Alerts

Compliance
  Frameworks
  Evidence
  Policies

Analytics
  DORA Metrics
  Reports
  Insights

Intelligence
  Ask Control Center
  Query History

Automation
  Runbooks
  Actions
  Approvals

Platform
  Integrations
  Settings
```

Automation can remain feature-flagged until the Action Plane exists.

## Application 360
Header:
application, current production version, health, security score, criticality, owner, last deployment.

Tabs:
Overview, Releases, Deployments, Security, Health, Dependencies, Compliance, Evidence, Audit, Actions.

Contextual AI entry:
**Ask about this application**

## AI assistant UX
Do not implement AI as a generic floating chatbot.

Use:
- global “Ask Control Center”
- contextual “Ask about this application”
- optional compact query entry on Overview

Suggested questions:
- What version is in production?
- What changed in the last release?
- Are there failed controls?
- What technical debt is open?
- Which environments are behind production?
- Why is this release not production ready?

Answer anatomy:
```text
Answer
Supporting facts
Evidence / Sources
Scope
Freshness
Suggested drill-down
```

Never fabricate missing state.

## Future actions
Restart, Scale, Redeploy, Rollback, Execute Runbook.

Production actions must visually show target, reason, risk, approval, confirmation, execution state, and audit reference.

AI recommendations and executed operator actions must remain visually distinct.
