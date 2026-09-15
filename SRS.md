# Software Requirements Specification - AeroTwin-UAV

## 1. Scope and terminology

The system shall provide synthetic UAV piston-engine simulation, monitoring, analysis, mission assessment, and reporting. “Shall” denotes a testable requirement. Telemetry is synthetic; fault outputs are explanatory simulation results.

## 2. Roles and permissions

| Role | Permissions |
| --- | --- |
| Operator | Create and operate own runs; view live data, assessments, and reports. |
| Maintenance reviewer | All operator read capabilities; view all shared runs; add maintenance notes. |
| Administrator | Manage users, roles, scenarios, retention settings, and all runs. |

## 3. Functional requirements

| ID | Requirement |
| --- | --- |
| FR-01 | The system shall authenticate users with email and password. |
| FR-02 | The system shall enforce role-based access on every protected API request. |
| FR-03 | An operator shall configure RPM, altitude, ambient temperature, throttle, load condition, and mission profile before creating a run. |
| FR-04 | The system shall validate configuration values against configured synthetic ranges before accepting a run. |
| FR-05 | The system shall create a unique run ID and persist its configuration, seed, creator, and lifecycle state. |
| FR-06 | The system shall support start, pause, resume, and reset actions for an authorised run. |
| FR-07 | While running, the system shall generate and broadcast a timestamped telemetry sample at a configurable interval, defaulting to one second. |
| FR-08 | A telemetry sample shall contain RPM, CHT, EGT, oil pressure, vibration RMS, fuel flow, and engine load. |
| FR-09 | The dashboard shall display latest values, history charts, engine health, fitness score, mission risk, active alerts, and virtual-engine status. |
| FR-10 | The system shall support a deterministic injector-abnormality scenario. |
| FR-11 | The analysis service shall calculate anomaly score, predicted fault, confidence, contributing signals, severity, and recommendation for each analysed state. |
| FR-12 | Every created alert shall reference its run, analysis snapshot, status, timestamp, and evidence. |
| FR-13 | The system shall assess a mission using altitude, ambient temperature, duration, mission type, and current run decision. |
| FR-14 | The system shall persist telemetry, decisions, alerts, assessments, and maintenance notes for a run. |
| FR-15 | The system shall produce a print-friendly report from persisted run data. |
| FR-16 | The system shall label telemetry, scores, predictions, and reports as synthetic. |
| FR-17 | Administrators shall be able to activate/deactivate user accounts and manage available scenarios. |

## 4. Business rules

- BR-01: Only one active simulation worker may run for a run ID.
- BR-02: A paused run shall emit no new telemetry until resumed.
- BR-03: Reset shall create a new baseline sequence while retaining the original run audit record or create a documented successor run.
- BR-04: An alert shall be deduplicated when its fault type and severity are unchanged within the configured alert window.
- BR-05: Mission risk shall be `low`, `medium`, or `high`; an assessment shall include reasons and a human-readable recommendation.
- BR-06: No API or UI action shall claim to control equipment or autonomously approve a mission.

## 5. Data requirements

| Entity | Minimum fields |
| --- | --- |
| User | id, email, password_hash, role, active, created_at |
| SimulationRun | id, creator_id, configuration, scenario, seed, status, started_at, ended_at |
| TelemetrySample | id, run_id, timestamp, rpm, cht_c, egt_c, oil_pressure_bar, vibration_rms, fuel_flow_lph, engine_load_pct |
| DecisionSnapshot | id, run_id, timestamp, health, fitness, risk, anomaly_score, fault, confidence, recommendation |
| Alert | id, run_id, decision_id, type, severity, status, evidence, created_at, resolved_at |
| MissionAssessment | id, run_id, inputs, risk, result, reasons, created_at |
| MaintenanceNote | id, run_id, author_id, body, created_at |

## 6. Validation and edge cases

- Numeric inputs shall reject non-numeric, missing, NaN, infinite, and out-of-range values.
- A start request for a running run shall return an idempotent success response, not create a second worker.
- A pause/resume/reset request for a non-existent or inaccessible run shall return `404` or `403` without state change.
- A disconnected client shall reconnect with exponential backoff and fetch missed history before rendering new samples.
- If a WebSocket payload fails schema validation, the client shall discard it, display a non-blocking data-update warning, and retain the last valid state.
- If history is empty, the UI shall display an intentional empty state rather than an empty chart frame.
- If report generation fails, the system shall keep the run intact and provide a retryable error.

## 7. Authentication, authorisation, and security

- Passwords shall be hashed with Argon2id or bcrypt; plain passwords shall never be stored or logged.
- Session tokens shall be short-lived, signed, and transmitted only over HTTPS in production.
- The API shall verify authentication and role authorisation server-side, independent of UI controls.
- The application shall validate and sanitise all inputs, use parameterised database access, and prevent mass assignment.
- The system shall rate-limit sign-in attempts and log authentication failures without sensitive data.
- Secrets shall be supplied only through environment configuration.
- Audit events shall record sign-in, role change, scenario change, run control action, and report creation.

## 8. Error handling and observability

- APIs shall return structured errors with a stable code, safe message, and request ID.
- The client shall give actionable error messages and retry only safe/idempotent operations.
- Server exceptions shall be logged with request ID and stack trace; clients shall not receive stack traces.
- Health endpoints shall expose readiness and liveness without exposing credentials or internal data.

## 9. Non-functional requirements

| Area | Requirement |
| --- | --- |
| Performance | API reads shall target p95 under 500 ms under MVP load; live snapshots target 2-second end-to-end delivery. |
| Availability | The app shall recover from client reconnection and server restart without corrupting persisted runs. |
| Accessibility | Primary flows shall meet WCAG 2.1 AA colour contrast, keyboard navigation, and visible focus requirements. |
| Responsive design | The application shall support 320 px mobile through desktop layouts. |
| Security | Production traffic shall use HTTPS; security headers and restrictive CORS shall be configured. |
| Retention | Run data shall have a configurable retention policy and administrator deletion workflow. |

## 10. SRS acceptance

The SRS is accepted when automated and manual tests demonstrate each FR and business rule, role checks deny prohibited actions, invalid inputs produce safe errors, and the complete simulation-to-report workflow succeeds from a new authenticated session.
