# Development Plan - AeroTwin-UAV

## Delivery strategy

Build the smallest complete vertical slice first: authenticate, create a run, stream deterministic telemetry, display it, analyse one anomaly, assess a mission, and preserve the result. Add advanced visualisation and administration only after this workflow is reliable.

## Milestones

| Milestone | Deliverable | Dependencies | Priority |
| --- | --- | --- | --- |
| M0: Foundation | Repository, environments, CI, linting, formatting, Docker | None | P0 |
| M1: Identity and data | Schema, migrations, authentication, roles, audit skeleton | M0 | P0 |
| M2: Simulation API | Lifecycle, generator, persistence, WebSocket events | M1 | P0 |
| M3: Monitoring UI | Shell, configuration, dashboard, charts, reconnect flow | M2 | P0 |
| M4: Decision workflow | Injector scenario, rules, alerts, fault-analysis UI | M2, M3 | P0 |
| M5: Mission and reports | Assessment, history, print report, maintenance notes | M4 | P1 |
| M6: Hardening | Accessibility, tests, security review, performance, monitoring | M1-M5 | P0 before release |
| M7: Deployment | Staging, production, backups, alerts, release runbook | M6 | P0 before release |

## Work breakdown

### Setup

- Create `frontend/`, `backend/`, and deployment configuration.
- Configure TypeScript strict mode, Python formatting/linting, pre-commit hooks, and CI.
- Add `.env.example`, containerised local development, and documented start commands.

### Backend and database

- Implement migrations for users, runs, telemetry, decisions, alerts, assessments, notes, and audits.
- Build authenticated API routes, request validation, authorisation dependencies, and structured errors.
- Build seeded normal and injector-abnormality simulation modes.
- Persist canonical snapshots before WebSocket broadcast.
- Add rules-based analysis, alert deduplication, mission assessment, and report endpoints.

### Frontend

- Build protected routing and role-aware navigation.
- Implement simulation configuration and controls.
- Build dashboard, live charts, connection state, virtual-engine view, and alerts.
- Build fault analysis, mission assessment, history/report, and admin screens.
- Complete responsive, keyboard, loading, error, empty, and offline states.

### Quality and deployment

- Unit-test simulation rules, validators, permissions, and scoring.
- Integration-test APIs, transactions, and WebSocket lifecycle.
- End-to-end test complete scenario and access restrictions.
- Perform accessibility, performance, error-recovery, and security checks.
- Deploy staging, verify migrations/backups/monitoring, then release production.

## MVP definition of done

- All P0 PRD/SRS requirements are implemented and traced to tests.
- A seeded injector scenario is reproducible from creation through report generation.
- Authentication, role checks, input validation, audit logging, and secure configuration work.
- Desktop and mobile primary flows pass accessibility review.
- Unit, integration, and end-to-end suites pass in CI.
- Staging has health checks, structured logging, error monitoring, backups, and rollback instructions.
- All UI and reports clearly identify synthetic decision-support output.

## Post-MVP backlog

- Additional fault scenarios and configurable rule sets.
- Role-specific maintenance workflow and report approval.
- Validated external data-adapter boundary.
- ML experimentation only with governed, approved datasets.
- Background event processing only after measured concurrent load requires it.
