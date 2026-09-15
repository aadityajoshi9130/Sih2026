# UI/UX Specification - AeroTwin-UAV

## Experience principles

- Show what matters now before detail.
- Pair every warning with evidence and a recommended next action.
- Never present synthetic output as an operational command.
- Use consistent status language and colours throughout.
- Design desktop for analysis and mobile for awareness and quick review.

## Navigation and user journey

```text
Sign in -> Dashboard -> Simulation setup -> Live monitoring -> Fault analysis
                                  -> Mission assessment -> Run report/history
```

Primary navigation: Dashboard, Simulation, Live Monitoring, Fault Analysis, Mission Assessment, Reports. Utility navigation: profile, notifications, settings, sign out. Administrators additionally see Users and Scenarios.

## Screens

| Screen | Purpose | Key content/actions |
| --- | --- | --- |
| Sign in | Secure access | Email, password, validation, forgot-password path. |
| Dashboard | Current overview | Health, fitness, risk, alerts, latest telemetry, quick actions. |
| Simulation setup | Configure a run | Sliders/number fields, selects, scenario, start/reset. |
| Live monitoring | Observe a run | Live charts, status, connection state, virtual engine. |
| Fault analysis | Explain risk | Fault ranking, confidence, evidence, timeline, recommendation. |
| Mission assessment | Evaluate inputs | Mission form, result, reasons, baseline comparison. |
| Run history/report | Review output | Filters, run list, full report, print action. |
| Admin | Govern product | User roles, active state, scenarios, audit list. |

## Components and interactions

- App shell: top bar, responsive sidebar, breadcrumb, page title.
- Status cards: health, fitness, risk, connection state, active alerts.
- Chart card: current reading, unit, trend, time range, accessible data-table alternative.
- Simulation form: labelled sliders paired with exact numeric input, select controls, validation summary, start/pause/reset.
- Alert card: severity, title, time, evidence chips, recommended action, detail link.
- Fault-evidence panel: ranked signals, baseline/current comparison, explanation.
- Mission-result card: risk state, reasons, recommendation, input summary.

Starting a simulation transitions to monitoring only after the first valid event arrives. Pause/resume updates after server acknowledgement; reset requires confirmation. Changing mission inputs never mutates a run; users explicitly select **Evaluate mission**. Chart tooltips expose timestamp, value, unit, and status.

## States

| State | Required behaviour |
| --- | --- |
| Loading | Skeletons matching card/chart shape; no unexplained blocked navigation. |
| Empty history | Explain that no runs exist and link to simulation setup. |
| Empty chart | State that samples appear after the run starts. |
| API error | Preserve last valid data; show retry and request ID when useful. |
| Offline/reconnecting | Persistent connection badge; disable live-only actions where needed. |
| Permission denied | Explain restriction without exposing protected data. |

## Responsive and accessible design

- Desktop (>=1024 px): persistent sidebar, four metric cards, multi-chart grid.
- Tablet (768-1023 px): collapsible sidebar, two-column content grid.
- Mobile (<768 px): drawer navigation, stacked cards, health/risk/alerts prioritised.
- Minimum touch target: 44 x 44 CSS px.
- Meet WCAG 2.1 AA contrast; use semantic headings, labels, visible focus, keyboard operation, and reduced-motion support.
- Never use colour alone for status; combine text, icon, and accessible label.

## Visual tokens

| Token | Value / use |
| --- | --- |
| `navy-950` | `#071C33`, navigation/app shell |
| `blue-600` | `#1677FF`, primary action/active navigation |
| `cyan-500` | `#15C7E8`, live/synchronised state |
| `success-600` | `#168A4B`, healthy/normal |
| `warning-600` | `#B7791F`, caution/medium risk |
| `danger-600` | `#D92D20`, critical/fault |
| `surface` | `#FFFFFF`, content cards |
| `background` | `#F4F8FC`, page background |
| Typography | Inter/system sans; 16 px body, 14 px secondary, 20-24 px headings |
| Spacing | 4 px grid; 16-24 px card padding; 24-32 px page gaps |
