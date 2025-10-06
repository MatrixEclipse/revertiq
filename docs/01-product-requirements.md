RevertIQ — Core Features & Mathematical Specification
1. System Objective

RevertIQ’s primary analytical goal is to determine, for any given ticker and time range, the historical periods of highest mean-reversion expectancy — measured in statistically defensible, reproducible metrics.

Each run must output:

Validated intraday and interday mean-reversion windows (time-of-day × day-of-week).

Optimized parameters for entry (z-in), exit (z-out), and hold duration (Tₘₐₓ).

Performance statistics adjusted for costs, multiple-testing, and non-stationarity.

Confidence diagnostics (e.g., p-values, CIs, sample size, drift indicators).

2. Mathematical Foundation
2.1. Core Process Definition

Let 
𝑃
𝑡
P
t
	​

 be the price series sampled at a fixed interval (1-minute or higher).
We define the mean-reversion deviation as:

𝑧
𝑡
=
𝑃
𝑡
−
𝜇
𝑡
𝜎
𝑡
z
t
	​

=
σ
t
	​

P
t
	​

−μ
t
	​

	​


where:

𝜇
𝑡
μ
t
	​

 = rolling mean (EMAₖ, SMAₖ, or VWAPₖ over window 
𝑘
k)

𝜎
𝑡
σ
t
	​

 = rolling volatility (std. dev. or exponentially weighted std.)

This forms a z-score time series — a normalized measure of deviation from equilibrium.

2.2. Signal Triggers

For a given parameter set 
(
𝑧
𝑖
𝑛
,
𝑧
𝑜
𝑢
𝑡
,
𝑇
𝑚
𝑎
𝑥
)
(z
in
	​

,z
out
	​

,T
max
	​

):

Entry condition:

𝑧
𝑡
≤
−
𝑧
𝑖
𝑛
z
t
	​

≤−z
in
	​

 → open long position at 
𝑃
𝑡
P
t
	​


Exit conditions:

𝑧
𝑡
≥
𝑧
𝑜
𝑢
𝑡
z
t
	​

≥z
out
	​

 (mean has been reached)

Time elapsed ≥ 
𝑇
𝑚
𝑎
𝑥
T
max
	​


Profit per trade:

𝑟
𝑖
=
𝑃
𝑒
𝑥
𝑖
𝑡
−
𝑃
𝑒
𝑛
𝑡
𝑟
𝑦
𝑃
𝑒
𝑛
𝑡
𝑟
𝑦
−
𝑐
r
i
	​

=
P
entry
	​

P
exit
	​

−P
entry
	​

	​

−c

where 
𝑐
c = total transaction cost (bps spread + slippage + fee).

For short trades, the conditions are mirrored.

2.3. Mean-Reversion Quality Metrics

Each trade outcome contributes to:

Return per trade (bps): 
𝑟
‾
r

Win rate: 
𝑛
𝑤
𝑖
𝑛
𝑠
𝑛
𝑡
𝑜
𝑡
𝑎
𝑙
n
total
	​

n
wins
	​

	​


Average hold time: median and interquartile range of holding durations.

Sharpe ratio (annualized):

𝑆
=
𝐸
[
𝑟
𝑖
]
𝑆
𝑡
𝑑
[
𝑟
𝑖
]
×
𝑁
S=
Std[r
i
	​

]
E[r
i
	​

]
	​

×
N
	​


where 
𝑁
N = expected trades per year.

Half-life estimate (OU model):

Fit an Ornstein–Uhlenbeck process to 
𝑧
𝑡
z
t
	​

:

𝑑
𝑧
𝑡
=
𝜃
(
𝜇
−
𝑧
𝑡
)
𝑑
𝑡
+
𝜎
𝑑
𝑊
𝑡
dz
t
	​

=θ(μ−z
t
	​

)dt+σdW
t
	​


Estimate 
𝜃
θ → reversion speed; compute half-life:

𝑡
1
/
2
=
ln
⁡
(
2
)
𝜃
t
1/2
	​

=
θ
ln(2)
	​


Report 
𝑡
1
/
2
t
1/2
	​

 as “expected mean-reversion duration.”

2.4. Stationarity & Reversion Tests

For each symbol / window:

ADF test (Augmented Dickey–Fuller): test null hypothesis of unit root.

KPSS test: confirm stationarity of residuals.

Hurst exponent (H):

𝐻
<
0.5
H<0.5: mean-reverting

𝐻
=
0.5
H=0.5: random walk

𝐻
>
0.5
H>0.5: trending

Z-series autocorrelation (ρ₁): short-lag correlation as a secondary check.

Outputs include p-values and binary flags:

"stationarity": { "adf_p": 0.01, "kpss_p": 0.12, "hurst": 0.38, "mean_reverting": true }

2.5. Walk-Forward Optimization

RevertIQ enforces anti-overfitting by walk-forward methodology:

Split data into rolling windows:

Training: N days (e.g., 120)

Testing: M days (e.g., 30)

Step: S days (e.g., 30)

Optimize 
(
𝑧
𝑖
𝑛
,
𝑧
𝑜
𝑢
𝑡
,
𝑇
𝑚
𝑎
𝑥
)
(z
in
	​

,z
out
	​

,T
max
	​

) on training slice for max Sharpe.

Freeze parameters, test on next slice (out-of-sample).

Aggregate OOS results across all rolls.

This ensures the final “best day/time windows” reflect forward performance, not curve-fit bias.

2.6. Multiple-Testing & Significance Control

Across hundreds of windows × parameters, RevertIQ corrects for data-snooping:

Compute raw p-values via bootstrap hypothesis tests:

𝐻
0
:
mean return
=
0
H
0
	​

:mean return=0

Apply Benjamini–Hochberg FDR correction at user-defined α (default 10%).

Report both raw and adjusted p-values.

Only windows with FDR-adjusted p ≤ α are surfaced as statistically significant.

2.7. Cost Modeling

Transaction costs are integrated via configurable parameters:

Component	Default	Formula
Bid-Ask Spread	0.5 bps	Half-spread × 2 per round trip
Slippage	0.8 bps	Dynamic or fixed multiplier on spread
Fee	0.2 bps	Broker + exchange
Total Cost (c)	1.5 bps	Sum, applied per trade

Sensitivity analysis automatically reports break-even cost and degradation curve (Sharpe vs cost multiplier).

2.8. Robustness & Drift Diagnostics

To guarantee ongoing validity:

Yearly Stability Table: annual OOS Sharpe & CI.

Drift Flag: triggered if last-year Sharpe < median(−1 SD).

Trade Count Filter: discard windows with 
𝑛
<
50
n<50.

Regime Detection (optional): volatility clustering (GARCH(1,1) or thresholding).

3. Outputs & Deliverables

Every API call returns:

3.1. Ranked Window Leaderboard

Ranked by OOS Sharpe or expected return per trade (bps).

Includes:

dow, window, entry_band, exit_band, hold_cap

Sharpe, return/trade, hit-rate, half-life, cost_break_even_bp

p-values (raw, FDR), sample count, drift flag

3.2. Statistical Summary Block

Aggregated metrics across all trades:

{
  "global_stats": {
    "total_trades": 1240,
    "oos_sharpe": 1.28,
    "avg_ret_bp": 3.1,
    "hit_rate": 0.54,
    "mean_hold_min": 24.3,
    "fdr_alpha": 0.10,
    "significant_windows": 14
  }
}

3.3. Diagnostics

Stationarity test results

OU half-life distribution

Volatility & volume heatmaps

Cost sensitivity table

Drift indicator summary

3.4. Data Provenance

Polygon API version + query range

Adjustments applied (splits/dividends)

Data hash for reproducibility

Timestamp and RevertIQ version tag

4. Feature Pillars (for Personas)
Persona	Core Value Delivered	Metrics Returned	Optional Visualizations
Builder	API-driven quantification of reversion strength	JSON: windows, Sharpe, CI	Time-series of z-scores, residual histograms
Analyst	Reproducible statistical tests & significance	Full stationarity & OU diagnostics	Correlation matrices, half-life charts
Operator	Fast summary of when & how to trade	Top windows with after-cost returns	Daily/weekly heatmaps
Platform	Embeddable, white-label quant analytics	JSON endpoints, CSV export	Widget-ready formatted output
Educator	Conceptual illustration of reversion	Simplified “Explain Mode”	Annotated charts, flow diagrams
5. KPIs / Success Criteria
KPI	Target
Out-of-sample Sharpe stability	±0.15 across adjacent windows
API latency (per call)	< 3 s (cached); < 15 s (full run)
Mean trade sample size	≥ 50 per window
CI width / mean ratio	< 0.5
Reproducibility (data hash match)	100% deterministic runs
User satisfaction (surveyed)	≥ 90% trust in statistical rigor