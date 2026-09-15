# AeroTwin-UAV - Project Brain

## Product identity

- **Name:** AeroTwin-UAV
- **Tagline:** From Simulation to Safer Skies
- **Challenge:** SIH26054
- **One-line pitch:** A software-only digital twin that turns simulated UAV piston-engine telemetry into explainable maintenance and mission-readiness advice.

## Canonical decisions

| Topic | Decision |
| --- | --- |
| Prototype type | Web application with synthetic telemetry only. |
| Engine | Virtual four-cylinder aero piston engine. |
| Initial simulated fault | Injector abnormality. |
| Core signals | RPM, CHT, EGT, oil pressure, vibration RMS, fuel flow. |
| Primary outcomes | Engine health, fitness score, mission risk, anomaly evidence, recommendation. |
| Safety stance | Decision support; a human remains responsible for maintenance/mission decisions. |
| Visual direction | Dark navy shell, bright blue/cyan accents, white dashboard content, aircraft/engine imagery. |

## Data glossary

| Term | Meaning in this MVP |
| --- | --- |
| CHT | Cylinder Head Temperature. |
| EGT | Exhaust Gas Temperature. |
| RMS vibration | A simplified vibration-intensity signal. |
| Digital twin | The virtual engine state updated from synthetic telemetry. |
| Anomaly score | Transparent demo estimate of how unusual current trends are. |
| RUL | Remaining Useful Life; a future demo estimate, not an operational guarantee. |

## Baseline simulation configuration

```yaml
engine:
  type: Aero Piston, 4-cylinder
  displacement_cc: 1200
  max_power_hp: 115
simulation:
  rpm: 2500
  altitude_m: 3000
  ambient_temperature_c: -10
  throttle_pct: 70
  load_condition: cruise
  mission_profile: surveillance
```

## Example normal ranges for the UI

These are presentation ranges only, not engineering limits.

| Signal | Typical display range |
| --- | --- |
| RPM | 500-6,000 rpm |
| CHT | 100-300 C |
| EGT | 300-900 C |
| Oil pressure | 0-8 bar |
| Vibration | 0-3 RMS |
| Fuel flow | 0-25 L/h |

## Injector-abnormality simulation

1. The run starts in normal cruise with stable trends.
2. The scenario introduces a gradual EGT deviation, vibration increase, and fuel-flow instability.
3. The analytics service raises the anomaly score and explains those contributing signals.
4. The fault panel identifies an injector abnormality with simulated confidence.
5. The dashboard changes risk to caution/high as appropriate.
6. The recommendation is to inspect injector #3 and check the fuel system before the next mission.

## Do / do not say

| Use | Avoid |
| --- | --- |
| simulated telemetry | real aircraft telemetry |
| estimated / demo confidence | guaranteed prediction |
| maintenance recommendation | autonomous maintenance decision |
| decision support | flight-control system |
| software-only prototype | deployed defence system |

## Development direction

- Build the synthetic-data workflow as a robust, testable application rather than a presentation-only prototype.
- Keep simulation, decision logic, and UI separate so validated data adapters and ML models can be introduced safely later.
- Treat explainability, run history, and repeatable scenarios as core features.

## Open decisions

- Whether to include a true interactive 3D engine or a polished 2D state visual for the first release.
- Whether reporting exports as PDF in the MVP or starts with a print-friendly web page.
- Whether to add a login screen; it is not required for the core prototype.
- Which deployment host and database should be used after the local application is complete.
