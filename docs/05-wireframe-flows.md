🧭 RevertIQ UX Wireframes & Interaction Flows

(All layouts described textually — you can later translate these into Figma, Framer, or low-fidelity mocks.)

1. Global UX Architecture

RevertIQ’s UI (and CLI visual output) is organized into four experience layers:

Layer	Purpose	Typical User
Input Layer	Define ticker, timeframe, and parameters	Builder / Analyst
Compute Layer	Run analysis job, track progress	Builder / Operator
Insight Layer	View results (leaderboard, heatmaps, diagnostics)	Analyst / Operator
Explain Layer	Narrative + visual interpretation	Educator / Manager
2. Persona Wireframes & Flows
🧱 Persona 1: Quant Developer — CLI / Terminal Flow
Flow Overview
[Start CLI] → [Authenticate] → [Submit Analysis Job] → [Progress Spinner] → [Results Table]

Wireframe (text UI layout)
┌────────────────────────────────────────────┐
│ RevertIQ v1.0 — Mean Reversion Analyzer    │
├────────────────────────────────────────────┤
│ API Key: ✅ Verified                       │
│ Ticker: AAPL       Range: 2023-01 → 2025-10│
│ Interval: 1m       Mode: Long-only         │
│ Train/Test: 120d / 30d (step 30d)          │
├────────────────────────────────────────────┤
│ [Running Analysis...] ███████░░ 72%        │
│ ETA: ~38s                                 │
├────────────────────────────────────────────┤
│ Best OOS Windows (Sharpe)                  │
│--------------------------------------------│
│ DOW   Window       Sharpe  Ret(bp)  p(FDR) │
│ Tue   10:45–11:30   1.32     3.4     0.03  │
│ Wed   09:45–10:30   1.11     2.8     0.07  │
│ Thu   13:15–14:00   0.95     2.1     0.09  │
├────────────────────────────────────────────┤
│ Drift alert: weakening post-2025-03        │
│ Data hash: sha256:7f... | FDR α=0.10       │
└────────────────────────────────────────────┘


Key Interactions

rtiq run <ticker> → prompts config or loads preset TOML.

rtiq status <analysis_id> → shows progress and ETA.

rtiq export <analysis_id> --csv → dumps full diagnostics.

📊 Persona 2: Quant Researcher — Web Dashboard Flow
Flow Overview
Dashboard → Select Ticker → Configure Analysis → Run → View Heatmap → Inspect Window Details → Export

Wireframe Layout

1️⃣ Dashboard (Landing)

[Top Bar] RevertIQ | Version | Profile
----------------------------------------------------
[Search Bar] [Ticker Dropdown] [Date Range Picker]
[Run Analysis] Button
----------------------------------------------------
Recent Analyses:
| Ticker | Range | Sharpe (avg) | Significant Windows | Status | 🔍 |
| AAPL   | 2023–2025 | 1.28 | 14 | ✅ Complete | View |
| NVDA   | 2022–2024 | 0.95 | 9  | ⏳ Running | ... |


2️⃣ Analysis View (Result Screen)
Main Panel:

Heatmap (DOW × Time window)

Color intensity = OOS Sharpe ratio

Hover tooltip: ret/trade, hit rate, p(FDR)

Right Sidebar:

Ticker meta (symbol, date span, bar interval)

Filter controls (min trades, FDR threshold, side)

Download CSV / JSON buttons

📈  Heatmap Visualization
  ↑
  | Sharpe intensity
  |
  | Mon  Tue  Wed  Thu  Fri
  |----|----|----|----|----|
  |9:45|....|####|....|....|
  |10:15|##.|####|##..|....|
  ...
  (hover → tooltip w/ metrics)


3️⃣ Detailed Window Drilldown Modal

--------------------------------------------------
Window: Tue 10:45–11:30
Sharpe (OOS): 1.32 | pFDR: 0.03 | Half-life: 27m
--------------------------------------------------
[Metrics Chart]   [Cost Sensitivity Graph]
[Return Distribution Histogram]
[Walk-forward Yearly Stability Plot]
--------------------------------------------------
[Export JSON] [Copy API Call]

⚙️ Persona 3: Operator — Tile Dashboard Flow
Flow Overview
Login → Portfolio Summary → Ticker Card → Alert Setup

Wireframe: Portfolio Overview
RevertIQ Operator Console
----------------------------------------------------
Portfolio Mean Reversion Health
----------------------------------------------------
| Symbol | Current Edge | Top Window | Confidence | Drift |
|--------|---------------|-------------|-------------|-------|
| AAPL   | 🟩 +3.4bp     | Tue 10:45–11:30 | 97% | ⚠️ |
| NVDA   | 🟨 +2.1bp     | Thu 13:15–14:00 | 90% | 🟩 |
| MSFT   | ⬜ Neutral    | - | - | - |
----------------------------------------------------
[Alerts] 📢 Enable webhook when window becomes active


Ticker Tile View

📈 AAPL — Mean Reversion Snapshot
--------------------------------------------------
Best Window: Tue 10:45–11:30
Expected Return (bps): +3.4  |  Sharpe: 1.32
Confidence: 97% (pFDR=0.03)
Half-life: 27m
Drift Status: Weakening (last 6m)
--------------------------------------------------
[ Enable Live Scout 🔔 ]  [ Download Report ]

🧩 Persona 4: Platform Integrator — Embedded Widget Flow
Wireframe: “Top Windows Widget”
Top Mean Reversion Windows — powered by RevertIQ
-------------------------------------------------
🟩 Tue 10:45–11:30   +3.4 bp | 1.32 Sharpe | 97%
🟨 Wed 09:45–10:30   +2.8 bp | 1.11 Sharpe | 93%
🟨 Thu 13:15–14:00   +2.1 bp | 0.95 Sharpe | 90%
-------------------------------------------------
Data source: Polygon | Version 1.0.0


Interactions

Hover → microtooltips with stationarity diagnostics.

Click → open hosted drilldown page or embed modal.

Configurable dark/light theme via SDK.

🎓 Persona 5: Educator — Explain Mode Flow
Flow Overview
Select Symbol → Toggle Explain Mode → Receive Narrative + Visuals → Export Slides

Wireframe: Explain Mode Page
[Explain Mode] — AAPL Mean Reversion Overview (2023–2025)
------------------------------------------------------------
📘 Narrative Summary
"Between 2023 and 2025, AAPL showed its strongest mean reversion on 
Tuesdays between 10:45–11:30 AM. On average, price deviations of 
−1.5σ reverted within ~27 minutes, producing +3.4bp per trade 
(after transaction costs). Statistical significance >95%."
------------------------------------------------------------
📊 Visuals
[Heatmap Image]  [Return Distribution]  [Half-life Curve]
------------------------------------------------------------
📚 Concepts
Z-Score  — normalized deviation of price from rolling mean.
Half-life — expected time for mean reversion to reach halfway.
------------------------------------------------------------
[Download Slide Deck ⬇️]  [Copy Citation JSON]

3. Cross-Screen UX Patterns
Pattern	Description
Job Cards	Visual summary of all runs (status, completion %, version, ticker).
Explainable Metrics Tooltip	Hover on any number → shows definition, formula, and data provenance.
Version Footers	Every view includes RevertIQ + Polygon data version for auditability.
Color Coding	Green = statistically significant; Yellow = moderate; Gray = insignificant.
Drift Alerts	Animated ⚠️ badge if reversion stability declines >1σ from historical mean.
CI Visualization	Bar ± shaded region to show uncertainty.
Command-to-UI Sync	CLI and Web dashboard share the same analysis_id for traceability.
4. Interaction Flow Summary
Step	User Action	System Reaction	Output
1	Submit ticker/timeframe	Validates params, checks data coverage	Progress view
2	Analysis running	Background engine runs math stack	Status polling 10s interval
3	Analysis complete	FDR filter + ranking	Results JSON + visuals
4	Drilldown click	Retrieve diagnostics for window	Modal with charts
5	Export	Generate deterministic report	JSON/CSV/PDF
6	(Optional) Enable webhook	Store callback + thresholds	Real-time alerts on new results
5. Accessibility & Responsiveness

Contrast ratios ≥ 4.5:1 for data visualization.

Keyboard navigation for tables and filters.

Touch-optimized layout for trader dashboards.

CLI output readable on dark terminals.

6. Visual Branding

Primary color: Deep Indigo (#3B3F6E) — evokes trust and quantitative rigor.

Accent: Cyan (#28D6E4) — denotes live statistical edges.

Typography: Inter / JetBrains Mono (CLI, dashboard).

Design language: Minimalist, grid-aligned, “data first”.

7. UX KPIs
KPI	Target
Dashboard Load Time	< 3s
Heatmap Render Time	< 2s
CLI Response Time	< 15s full analysis
Export Latency	< 5s
User Trust Rating (surveys)	≥ 90% “high clarity”
Time to Insight (first result visible)	< 60s