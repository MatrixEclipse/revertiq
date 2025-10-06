RevertIQ API — v1
0) Conventions

Base path: /v1

Auth: Authorization: Bearer <api_key>

Content-Type: application/json

Idempotency: Optional Idempotency-Key header on POSTs

Version pinning: X-RevertIQ-Version: 1.0

Time: ISO-8601 (YYYY-MM-DD for dates; full timestamps when needed)

Ticker format: Exchange-agnostic symbol (e.g., AAPL, MSFT)

1) Endpoints (Overview)

POST /analyze — run an analysis for one ticker (sync by default; can return 202 if async=true)

GET /analysis/{analysis_id} — fetch analysis status/result (for async or retrieval)

POST /batch/analyze — analyze multiple tickers in one request (always async)

GET /schemas — return JSON Schemas for all objects (for validation)

GET /metadata/calendar — trading sessions/holidays used for sessionization

GET /metadata/ticker/{ticker} — data coverage, first/last date, split flags, etc.

GET /healthz / GET /version — health/version

Webhooks (optional):

POST <client_url> — “analysis.complete” with compact summary

2) Request Models
2.1 Analyze Request (POST /analyze)
{
  "ticker": "AAPL",
  "horizon": { "start": "2023-01-01", "end": "2025-10-01" },
  "bar": { "interval": "1m", "rth_only": true },
  "windows": { "spec": "09:45-16:00/30m" },
  "signal": {
    "detrend": { "type": "ema", "lookback": 20 },
    "zscore":  { "lookback": 60, "vol": "ewm" },
    "side": "long_only"   // "long_only" | "short_only" | "both"
  },
  "params": {
    "entry_grid": [-1.0, -1.25, -1.5],
    "exit_grid":  [0.0, -0.25],
    "hold_grid_min": [15, 30, 45]
  },
  "costs": {
    "spread_bp": 0.5,
    "slippage_bp": 0.8,
    "fee_bp": 0.2,
    "mode": "bars" // "bars" | "quotes"
  },
  "walk_forward": { "train_days": 120, "test_days": 30, "step_days": 30 },
  "significance": { "fdr_alpha": 0.10, "min_trades": 50 },
  "exclusions": { "exclude_earnings": true, "halted_periods": "auto" },
  "output": { "include_trades": false, "format": "json" },
  "async": false
}


Notes

windows.spec expands into contiguous bins (e.g., 30-min rolling).

costs.mode="quotes" signals higher-fidelity cost modeling when NBBO is available.

exclusions.exclude_earnings filters earnings windows for cleaner mean-reversion.

Responses

200 OK with full result (sync)

202 Accepted with { analysis_id, status: "queued" } if async=true

2.2 Batch Analyze (POST /batch/analyze)
{
  "tickers": ["AAPL", "MSFT", "NVDA"],
  "shared": { ...same structure as POST /analyze but without ticker... },
  "async": true,
  "webhook_url": "https://client.example.com/revertiq-hook"
}


Response: 202 Accepted

{ "batch_id": "bat_7fQ...", "status": "queued", "submitted": 3 }

3) Response Models
3.1 Analyze Response (Success)
{
  "analysis_id": "an_3kX9v",
  "status": "complete",              // "queued" | "running" | "complete" | "failed"
  "ticker": "AAPL",
  "horizon": { "start": "2023-01-01", "end": "2025-10-01" },
  "assumptions": {
    "bar": { "interval": "1m", "rth_only": true },
    "windows": { "resolved": ["09:45-10:15","10:15-10:45", "..."] },
    "signal": { "detrend": {"type":"ema","lookback":20}, "zscore": {"lookback":60,"vol":"ewm"}, "side":"long_only" },
    "params": { "entry_grid": [-1.0,-1.25,-1.5], "exit_grid": [0.0,-0.25], "hold_grid_min": [15,30,45] },
    "costs": { "spread_bp":0.5,"slippage_bp":0.8,"fee_bp":0.2,"mode":"bars" },
    "walk_forward": { "train_days":120,"test_days":30,"step_days":30 },
    "significance": { "fdr_alpha":0.1,"min_trades":50 },
    "exclusions": { "exclude_earnings": true, "halted_periods": "auto" },
    "data": { "provider": "polygon", "adjusted": true }
  },
  "windows_ranked": [
    {
      "dow": "Tue",
      "window": "10:45-11:30",
      "oos_ret_per_trade_bp": 3.4,
      "oos_sharpe": 1.32,
      "hit_rate": 0.56,
      "median_time_to_mean_min": 22,
      "n_trades": 418,
      "entry_band": -1.5,
      "exit_band": 0.0,
      "hold_cap_min": 45,
      "fdr_adj_p": 0.03,
      "cost_break_even_bp": 2.8,
      "stability": [
        {"period":"2023","oos_sharpe":0.9},
        {"period":"2024","oos_sharpe":1.5},
        {"period":"2025","oos_sharpe":0.4}
      ],
      "drift_flag": true
    }
  ],
  "global_stats": {
    "total_trades": 1240,
    "oos_sharpe": 1.28,
    "avg_ret_bp": 3.1,
    "hit_rate": 0.54,
    "mean_hold_min": 24.3,
    "fdr_alpha": 0.10,
    "significant_windows": 14
  },
  "diagnostics": {
    "stationarity": { "adf_p": 0.01, "kpss_p": 0.12, "hurst": 0.38, "mean_reverting": true },
    "ou_half_life_min": 27.6,
    "cost_sensitivity": [
      {"cost_multiplier":0.5,"oos_sharpe":1.61},
      {"cost_multiplier":1.0,"oos_sharpe":1.28},
      {"cost_multiplier":2.0,"oos_sharpe":0.83}
    ],
    "trade_count_by_window": [
      {"dow":"Tue","window":"10:45-11:30","n":418}
    ]
  },
  "provenance": {
    "data_hash": "sha256:7f..",
    "polygon": { "aggregates_version": "v2", "quotes_used": false },
    "revertiq_version": "1.0.0",
    "generated_at": "2025-10-06T17:12:00Z"
  },
  "notes": [
    "Edge weak post-2025-03; consider monthly re-tuning."
  ]
}

3.2 Analyze Response (Queued/Running)
{ "analysis_id": "an_3kX9v", "status": "running", "progress_pct": 62 }

3.3 Error Format (All endpoints)
{
  "error": {
    "code": "INVALID_ARGUMENT",  // see table below
    "message": "hold_grid_min must be non-empty",
    "field": "params.hold_grid_min",
    "hint": "Provide at least one hold duration in minutes"
  },
  "request_id": "req_92Pp..."
}


Standard Error Codes

UNAUTHENTICATED, PERMISSION_DENIED

INVALID_ARGUMENT (with field)

RESOURCE_EXHAUSTED (rate limits)

NOT_FOUND (unknown analysis_id)

FAILED_PRECONDITION (e.g., insufficient data coverage)

INTERNAL, UNAVAILABLE

Rate Limits (headers)

X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset

4) Objects (Schemas)
4.1 WindowResult
{
  "type": "object",
  "required": ["dow","window","oos_ret_per_trade_bp","oos_sharpe","n_trades","entry_band","exit_band","hold_cap_min","fdr_adj_p"],
  "properties": {
    "dow": { "type": "string", "enum": ["Mon","Tue","Wed","Thu","Fri"] },
    "window": { "type": "string", "pattern": "^[0-2][0-9]:[0-5][0-9]-[0-2][0-9]:[0-5][0-9]$" },
    "oos_ret_per_trade_bp": { "type": "number" },
    "oos_sharpe": { "type": "number" },
    "hit_rate": { "type": "number", "minimum": 0, "maximum": 1 },
    "median_time_to_mean_min": { "type": "number" },
    "n_trades": { "type": "integer", "minimum": 1 },
    "entry_band": { "type": "number" },
    "exit_band": { "type": "number" },
    "hold_cap_min": { "type": "integer" },
    "fdr_adj_p": { "type": "number", "minimum": 0, "maximum": 1 },
    "cost_break_even_bp": { "type": "number" },
    "stability": {
      "type": "array",
      "items": { "type":"object", "properties": { "period":{"type":"string"}, "oos_sharpe":{"type":"number"} } }
    },
    "drift_flag": { "type": "boolean" }
  }
}

4.2 Diagnostics
{
  "type": "object",
  "properties": {
    "stationarity": {
      "type": "object",
      "properties": {
        "adf_p": { "type": "number" },
        "kpss_p": { "type": "number" },
        "hurst": { "type": "number" },
        "mean_reverting": { "type": "boolean" }
      }
    },
    "ou_half_life_min": { "type": "number" },
    "cost_sensitivity": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": { "cost_multiplier": { "type": "number" }, "oos_sharpe": { "type": "number" } }
      }
    },
    "trade_count_by_window": {
      "type": "array",
      "items": { "type":"object", "properties": { "dow":{"type":"string"}, "window":{"type":"string"}, "n":{"type":"integer"} } }
    }
  }
}

4.3 GlobalStats
{
  "type": "object",
  "properties": {
    "total_trades": { "type": "integer" },
    "oos_sharpe": { "type": "number" },
    "avg_ret_bp": { "type": "number" },
    "hit_rate": { "type": "number" },
    "mean_hold_min": { "type": "number" },
    "fdr_alpha": { "type": "number" },
    "significant_windows": { "type": "integer" }
  }
}

5) Filtering & Sorting (Query Params)

For retrieval endpoints (when paginating long results):

GET /analysis/{analysis_id}/windows?sort=oos_sharpe&order=desc&min_trades=50&max_fdr=0.1&dow=Tue

Returns paginated WindowResult[]

Pagination headers: X-Page, X-Per-Page, X-Total

6) Webhooks (Optional, for async workflows)

Event: analysis.complete
Payload (compact):

{
  "type": "analysis.complete",
  "analysis_id": "an_3kX9v",
  "ticker": "AAPL",
  "top_window": { "dow":"Tue", "window":"10:45-11:30", "oos_sharpe":1.32, "fdr_adj_p":0.03 },
  "generated_at": "2025-10-06T17:12:00Z",
  "dashboard_url": "https://revertiq.app/a/an_3kX9v"
}


Security: RevertIQ-Signature: sha256=... (HMAC over body)

7) Limits, Quotas, SLAs

Request size: ≤ 64 KB

Max windows per request: Expansion capped at 40 bins

Horizon max span: 5 years per request (configurable)

Latency targets: <3s cached/small; <15s full scans

SLA fields: status, progress_pct for long runs

8) Validation Rules (selected)

horizon.start < horizon.end

bar.interval ∈ {"1m","5m","15m","30m","60m"}

signal.side ∈ {"long_only","short_only","both"}

significance.min_trades ≥ 30 (default 50)

params.entry_grid: values < 0 for long entries; mirror logic applied internally if side="both"

9) Security & Compliance

API keys scoped per tenant; rotate via dashboard

PII-free; market data only

Deterministic outputs: every response contains provenance.data_hash and revertiq_version

10) Examples
10.1 Success (sync)

POST /v1/analyze → 200 OK with full body (see §3.1)

10.2 Accepted (async)

POST /v1/analyze { "async": true } → 202 Accepted

{ "analysis_id":"an_3kX9v", "status":"queued" }

10.3 Fetch result

GET /v1/analysis/an_3kX9v → returns full result or running status.

11) Backward Compatibility & Versioning

Breaking changes → new base path /v2

Additive changes allowed within /v1 with minor version in X-RevertIQ-Version

Schemas discoverable via GET /schemas