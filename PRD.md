# Product Requirements Document - AeroTwin-UAV

## Product definition

**AeroTwin-UAV** is a web application that simulates a four-cylinder UAV piston engine and turns synthetic telemetry into explainable engine-health, fault-risk, and mission-suitability guidance. It is a software-only decision-support product: it does not operate an aircraft, connect to an aircraft, or replace qualified maintenance decisions.

## Problem

Engine problems are easier to manage when changing behaviour is visible before a mission-critical failure. Static readings and simple threshold alerts do not show an operator how values are trending, why a warning was produced, or how current condition affects a mission. AeroTwin-UAV provides one coherent flow from configured operating conditions through live telemetry, virtual-engine state, analysis, and a human-readable recommendation.

## Target users

| User | Need |
| --- | --- |
| Operator | Monitor engine condition, understand alerts, and assess a planned mission. |
| Maintenance reviewer | Inspect fault evidence, review history, and record a recommended next action. |
| Administrator | Manage users, roles, simulation scenarios, and system configuration. |

## Goals

- Provide a stable, real-time synthetic telemetry simulation.
- Make engine state and trend changes understandable at a glance.
- Produce explainable, deterministic synthetic anomaly and fault assessments.
- Support mission what-if assessment using current engine condition.
- Preserve run history, alerts, predictions, and reports for review.
- Establish an architecture that can later accept validated data sources without changing the core UI.

## MVP scope

### Included

- Secure sign-in and role-based access.
- Engine configuration: RPM, altitude, ambient temperature, throttle, load condition, mission profile.
- Start, pause, resume, and reset a synthetic simulation.
- Live RPM, CHT, EGT, oil pressure, vibration RMS, and fuel-flow telemetry.
- Dashboard with health, fitness, risk, alerts, charts, and virtual-engine status.
- Deterministic injector-abnormality scenario with visible evidence and recommendation.
- Mission suitability assessment.
- Run history and print-friendly report.

### Deferred

- Real engine or sensor connectivity.
- Production ML model training/inference.
- Autonomous operational or maintenance actions.
- Native mobile applications.
- Collaborative maintenance workflows, messaging, and external ticketing integrations.
- Advanced 3D engine rendering; a responsive 2D state visual is sufficient for the MVP.

## Core features

| Feature | User value |
| --- | --- |
| Simulation control | Creates repeatable operating conditions and scenarios. |
| Live monitoring | Shows current measurements and trends without manual refresh. |
| Digital twin view | Makes the current virtual engine state legible. |
| Fault analysis | Explains unusual behaviour, predicted fault, confidence, and evidence. |
| Mission assessment | Converts engine condition and mission inputs into proceed/caution guidance. |
| History and reports | Enables review of a completed run and its decisions. |

## User stories

- As an operator, I can configure and start a simulation so I can monitor a defined operating condition.
- As an operator, I can see live charts and health status so I can spot a deteriorating trend.
- As a maintenance reviewer, I can open an alert and see its contributing signals so I can understand why it was raised.
- As a maintenance reviewer, I can review a run report so I can plan an inspection.
- As an operator, I can change mission conditions and receive suitability guidance so I can compare scenarios.
- As an administrator, I can manage authorised users and simulation scenarios so access and demonstrations remain controlled.

## Success metrics

| Metric | MVP target |
| --- | --- |
| Simulation update delivery | At least 95% of active-browser snapshots rendered within 2 seconds. |
| Scenario repeatability | Same seeded configuration produces the same alert sequence. |
| Explanation completeness | 100% of active fault alerts contain severity, evidence, and recommended action. |
| Critical-flow completion | At least 90% of test users can configure, run, detect, assess, and report a scenario unaided. |
| Reliability | No unhandled client/server errors in a 30-minute continuous test run. |

## Assumptions

- The MVP uses only synthetic, non-sensitive data.
- Initial deployment has a small number of concurrent authenticated users.
- Scores, confidence, ranges, and recommendations are presentation rules, not engineering limits.
- A validated data governance process is required before any future real-data integration.

## Risks and mitigations

| Risk | Mitigation |
| --- | --- |
| Simulated output is mistaken for operational advice | Persistent synthetic-data label and non-autonomy disclaimer. |
| Inconsistent values across screens | Use a run ID and a single persisted decision snapshot. |
| Overstated AI capability | Explain deterministic rules and label confidence as simulated. |
| Live updates create UI instability | Bound event rate, validate payloads, reconnect safely, and test long runs. |
| Scope expansion delays the MVP | Keep the first release to one engine type and one canonical fault scenario. |

## Acceptance criteria

1. An authorised user can create, start, pause, resume, and reset a simulation run.
2. During a run, all six core telemetry signals update on the dashboard without a browser refresh.
3. The injector-abnormality scenario produces a reproducible anomaly, evidence list, risk state, and recommendation.
4. Mission assessment uses the current run decision plus submitted mission inputs.
5. A saved report matches the persisted telemetry and decision state for its run.
6. Unauthorised users cannot access protected API routes or run data.
7. Every user-facing screen states that outputs are synthetic decision support.
