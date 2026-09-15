# AeroTwin-UAV - Technical Stack

## Recommended MVP architecture

```text
React web client
  <-> WebSocket / REST API
  <-> FastAPI simulation and decision service
  <-> SQLite telemetry store
```

The client visualises data and submits simulation/mission inputs. The backend owns simulation state, synthetic anomaly logic, scoring, and report data. A WebSocket sends live snapshots while REST handles configuration, history, and exports.

## Stack

| Layer | Choice | Purpose |
| --- | --- | --- |
| Frontend | React + TypeScript + Vite | Fast, component-based responsive web UI. |
| Styling | Tailwind CSS | Consistent dashboard layout, states, and responsive design. |
| Charts | Recharts | Lightweight live line, gauge, and bar-chart visualisation. |
| 3D / engine visual | React Three Fiber, optional | Interactive visual engine state; use a 2D illustration first if time is limited. |
| Backend | Python 3.12 + FastAPI | Simulation, API, WebSocket, analytics, and report services. |
| Validation | Pydantic | Typed request, telemetry, and decision contracts. |
| Live transport | FastAPI WebSocket | Push one telemetry snapshot per second. |
| Database | SQLite + SQLAlchemy | MVP persistence for configuration, telemetry, alerts, and reports. |
| Testing | Vitest + React Testing Library; pytest | UI logic and backend simulation/decision tests. |
| Deployment | Docker Compose; Render/Railway/VPS | Reproducible local demo and simple hosting path. |

## Data model

```text
SimulationRun 1---* TelemetrySample
SimulationRun 1---* Alert
SimulationRun 1---* Prediction
MissionProfile 1---* MissionAssessment
```

### Telemetry sample

```ts
type TelemetrySample = {
  timestamp: string;
  rpm: number;
  chtC: number;
  egtC: number;
  oilPressureBar: number;
  vibrationRms: number;
  fuelFlowLph: number;
  engineLoadPct: number;
};
```

### Decision output

```ts
type Decision = {
  engineHealth: number;       // 0-100
  fitnessScore: number;       // 0-100
  missionRisk: "low" | "medium" | "high";
  anomalyScore: number;       // 0-100, simulated
  predictedFault: string | null;
  confidence: number;         // demo confidence, 0-100
  contributingSignals: string[];
  recommendation: string;
};
```

## API surface

| Method | Route | Purpose |
| --- | --- | --- |
| `POST` | `/api/simulations` | Create a run with configuration and selected scenario. |
| `POST` | `/api/simulations/{id}/start` | Start live generation. |
| `POST` | `/api/simulations/{id}/pause` | Pause generation. |
| `POST` | `/api/simulations/{id}/reset` | Reset run to baseline. |
| `GET` | `/api/simulations/{id}` | Current configuration and decision state. |
| `GET` | `/api/simulations/{id}/telemetry` | Recent history for charts. |
| `GET` | `/api/simulations/{id}/analysis` | Prediction, evidence, and recommendation. |
| `POST` | `/api/mission-assessments` | Evaluate a mission profile. |
| `GET` | `/api/reports/{runId}` | Generate/export report data. |
| `WS` | `/ws/simulations/{id}` | Push live telemetry and decision snapshots. |

## Simulation and analytics rules

- Begin from parameterised baseline ranges, then apply small bounded noise.
- Model scenarios as deterministic modifiers so the demo is repeatable.
- Derive scores from transparent weighted rules; retain contributing signals for explanations.
- Isolate the decision engine behind an interface so a validated ML model can replace it later.
- Never expose a simulated score as a certified prediction.

## Engineering standards

- TypeScript strict mode and server-side request validation.
- One shared telemetry/decision schema where practical.
- Seeded scenario tests for repeatable results.
- Persist timestamps in UTC; format for the browser locale.
- Use environment variables for deployment configuration; never commit secrets.
