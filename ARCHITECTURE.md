# System Architecture - AeroTwin-UAV

## Architecture principles

- Start as a modular monolith: simple to deploy, test, and evolve.
- Keep simulation, analysis, persistence, and presentation independent.
- Use explicit schemas at every boundary.
- Treat live state as a stream backed by persisted history, not as UI-only data.
- Avoid queues, microservices, and a time-series database until measured load requires them.

## Recommended stack

| Layer | Technology |
| --- | --- |
| Frontend | React, TypeScript, Vite, Tailwind CSS, React Router, TanStack Query, Recharts |
| Backend | Python 3.12, FastAPI, Pydantic, SQLAlchemy, Alembic |
| Auth | Argon2id/bcrypt passwords; signed access/refresh tokens or secure server sessions |
| Database | PostgreSQL in deployment; SQLite locally |
| Live transport | FastAPI WebSocket |
| Reports | HTML print stylesheet initially; server PDF after workflow stabilises |
| Tests | Vitest/React Testing Library, pytest, Playwright |
| Deployment | Docker Compose, TLS reverse proxy, managed PostgreSQL |
| Monitoring | Structured logs, error tracking, OpenTelemetry-ready metrics |

## Components

```text
Browser UI
  -> REST API: configuration, history, assessment, reports, administration
  <-> WebSocket: telemetry and decision snapshots
FastAPI modular monolith
  -> Auth module
  -> Simulation service
  -> Analysis / rules service
  -> Mission assessment service
  -> Report service
  -> SQLAlchemy repository layer
PostgreSQL
```

## Data flow

1. The browser submits validated configuration to `POST /api/v1/runs`.
2. The backend persists a run and deterministic seed.
3. When started, the simulation service generates a telemetry sample at the configured interval.
4. The analysis service evaluates the sample and recent trend window.
5. In one transaction, telemetry, decision snapshot, and changed alerts are persisted.
6. The backend broadcasts the canonical event to authorised run subscribers.
7. The browser updates charts from valid events; on reconnect it queries persisted history.
8. Mission assessment and report services read persisted run state, never browser-calculated values.

## API conventions

- Routes are versioned under `/api/v1`; JSON uses ISO 8601 UTC timestamps.
- Every response includes `requestId`; mutation responses include the updated resource version.
- WebSocket events use `{ type, runId, sequence, occurredAt, payload }`.
- Use cursor pagination for telemetry and audit history.

## Storage design

- PostgreSQL is the source of truth.
- Index telemetry by `(run_id, timestamp)` and alerts by `(run_id, status, created_at)`.
- Store run configuration, evidence, and assessment input as validated JSONB where flexible shape is useful.
- Store report metadata, not duplicate telemetry blobs.
- Back up production data daily and verify restore procedures before release.

## Security

- Backend authorisation is authoritative; UI visibility is not a security boundary.
- Use TLS, secure cookies where applicable, CSRF protection for cookie sessions, CORS allowlists, and security headers.
- Restrict database network access, use least-privilege credentials, and rotate secrets via the deployment provider.
- Redact passwords, tokens, and personal fields from logs.

## Deployment, monitoring, and scale

```text
Internet -> TLS reverse proxy -> frontend static files + FastAPI container -> PostgreSQL
```

- Use health-checked frontend/backend containers and separate development, staging, and production configurations.
- Run migrations before application rollout.
- Monitor availability, error rate, API latency, WebSocket connections, simulation-loop failures, and database health.
- The MVP supports a small user base with one application instance. At proven scale, move simulation workers to background workers and distribute events through Redis.
