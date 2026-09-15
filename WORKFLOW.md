# AeroTwin-UAV - Product Workflow

## 1. System data flow

```text
Operator configuration
  -> simulation engine
  -> synthetic telemetry stream
  -> digital-twin state
  -> anomaly/fault analysis
  -> health + mission decision
  -> dashboard, alert, report
```

## 2. Runtime workflow

1. The user selects operating conditions and a mission profile.
2. The backend creates a simulation run and baseline telemetry model.
3. On start, the backend emits a telemetry snapshot every second.
4. The client appends snapshots to charts and updates the virtual-engine state.
5. The decision engine compares current values and trend windows with baseline ranges.
6. If conditions exceed a scenario threshold, it creates/updates an anomaly and prediction.
7. The dashboard updates health, fitness, risk, alerts, evidence, and recommendation.
8. A mission assessment combines mission inputs with the current engine decision.
9. The report uses the same persisted state so all screens tell one consistent story.

## 3. Screen workflow

| Screen | Entry | Main user action | Output |
| --- | --- | --- | --- |
| Landing | Open app | Launch prototype | Product context and dashboard entry. |
| Dashboard | After launch | Review live status | Health, risk, alerts, latest telemetry. |
| Simulation | Navigation | Configure and start scenario | Live synthetic run. |
| Monitoring | Active run | Inspect charts and engine state | Trend awareness. |
| Fault prediction | Alert or navigation | Inspect evidence | Fault likelihood and recommendation. |
| Mission suitability | Navigation | Change mission inputs | Proceed/caution guidance. |
| Reports | Navigation | Export/print | Shareable session summary. |

## 4. End-to-end product scenario

1. Create a simulation run using operating conditions and a mission profile.
2. Start the run and observe baseline live telemetry and digital-twin state.
3. Select or schedule an injector-abnormality scenario.
4. Review the resulting anomaly evidence, prediction, and alert lifecycle.
5. Submit mission parameters and compare the assessment with baseline status.
6. Create a report from the persisted run for review and maintenance planning.

## 5. Scenario state machine

```text
idle -> configured -> running -> warning -> fault-analysed -> mission-assessed -> reported
                 \-> paused -> running
                 \-> reset -> configured
```

## 6. Consistency rules

- One run ID owns all live values, alerts, predictions, assessment results, and report data.
- Do not hard-code separate values per screen.
- Use one canonical anomaly: injector abnormality for the MVP simulation.
- Clearly label all metrics as synthetic values.
- Maintenance and mission actions remain recommendations for a human operator.

## 7. Development workflow

1. Build and test the simulation and telemetry contract before designing advanced visualisations.
2. Implement the dashboard against fixture data, then connect it to the live WebSocket stream.
3. Implement explainable, deterministic decision rules and test each scenario end to end.
4. Persist runs, telemetry, alerts, predictions, and assessments before adding reporting.
5. Add authentication, audit history, role-based access, and monitored deployment when the core workflow is stable.
6. Before adopting real-world data or ML models, establish data provenance, validation, security review, and domain-expert approval.
