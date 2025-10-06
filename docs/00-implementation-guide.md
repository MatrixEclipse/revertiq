# 🛠️ RevertIQ Implementation Guide

## Quick Start Checklist

### Prerequisites
- [ ] Polygon.io API key (free tier works)
- [ ] Python 3.10+ or Rust 1.70+
- [ ] PostgreSQL 14+
- [ ] Redis 7+

### Phase 1: Data Layer (Days 1-3)
- [ ] Set up Polygon API client
- [ ] Implement bar data fetching (1m, 5m intervals)
- [ ] Create Parquet storage layer (`lake/market/{ticker}/{interval}/{date}.parquet`)
- [ ] Build data hash function for reproducibility
- [ ] Add sessionization (RTH filtering, calendar awareness)

**Validation**: Fetch AAPL 1m bars for 2023-01-01, verify Parquet write/read

### Phase 2: Signal Engine (Days 4-7)
- [ ] Implement rolling mean (EMA, SMA, VWAP)
- [ ] Calculate rolling volatility (std, EWMA)
- [ ] Compute z-score time series
- [ ] Build entry/exit signal detector
- [ ] Add hold duration tracking

**Validation**: Generate z-scores for AAPL, plot to verify mean-reverting behavior

### Phase 3: Statistical Tests (Days 8-10)
- [ ] Implement ADF test (Augmented Dickey-Fuller)
- [ ] Implement KPSS test
- [ ] Calculate Hurst exponent
- [ ] Fit Ornstein-Uhlenbeck process
- [ ] Compute half-life estimates

**Validation**: Run stationarity tests on known mean-reverting series

### Phase 4: Walk-Forward Engine (Days 11-14)
- [ ] Build train/test split logic
- [ ] Implement parameter grid search (z_in, z_out, T_max)
- [ ] Calculate in-sample Sharpe
- [ ] Run out-of-sample validation
- [ ] Aggregate OOS results across folds

**Validation**: Walk-forward on AAPL 2023-2024, verify no look-ahead bias

### Phase 5: Multiple Testing Correction (Days 15-16)
- [ ] Implement bootstrap hypothesis test (H0: mean return = 0)
- [ ] Calculate raw p-values per window
- [ ] Apply Benjamini-Hochberg FDR correction
- [ ] Filter windows by significance threshold

**Validation**: Verify FDR controls false discovery rate at α=0.10

### Phase 6: Cost Modeling (Days 17-18)
- [ ] Add spread cost (bps)
- [ ] Add slippage model
- [ ] Add fee structure
- [ ] Compute cost-adjusted returns
- [ ] Build cost sensitivity analysis

**Validation**: Verify break-even cost calculation

### Phase 7: API Layer (Days 19-22)
- [ ] Implement POST /v1/analyze (sync)
- [ ] Add async job queue (Redis/RabbitMQ)
- [ ] Implement GET /v1/analysis/{id}
- [ ] Add authentication (Bearer tokens)
- [ ] Implement rate limiting
- [ ] Add request validation

**Validation**: curl POST /v1/analyze, verify JSON response matches spec

### Phase 8: Caching & Provenance (Days 23-24)
- [ ] Implement result caching (24h TTL)
- [ ] Add data_hash to all responses
- [ ] Include revertiq_version
- [ ] Store provenance metadata
- [ ] Add idempotency support

**Validation**: Identical requests return cached results with same data_hash

### Phase 9: CLI Tool (Days 25-26)
- [ ] Build CLI with argument parsing
- [ ] Add pretty table output
- [ ] Implement progress spinner
- [ ] Add export commands (CSV, JSON)

**Validation**: `rtiq run AAPL` produces formatted table

### Phase 10: Testing & Deployment (Days 27-30)
- [ ] Write unit tests (>80% coverage)
- [ ] Add integration tests
- [ ] Create Docker image
- [ ] Write docker-compose.yml
- [ ] Add deployment docs

**Validation**: `docker-compose up` → API responds to health check

## Key Implementation Tips

### Z-Score Calculation
```python
# Pseudocode
def compute_zscore(prices, lookback=20):
    mu = prices.rolling(lookback).mean()
    sigma = prices.rolling(lookback).std()
    return (prices - mu) / sigma
```

### Walk-Forward Structure
```
Data: [====Train====][==Test==]
              [====Train====][==Test==]
                      [====Train====][==Test==]
```

### FDR Correction
```python
from statsmodels.stats.multitest import multipletests
reject, pvals_corrected, _, _ = multipletests(
    raw_pvals, alpha=0.10, method='fdr_bh'
)
```

### API Response Format
```json
{
  "analysis_id": "an_3kX9v",
  "status": "complete",
  "windows_ranked": [...],
  "global_stats": {...},
  "diagnostics": {...},
  "provenance": {
    "data_hash": "sha256:...",
    "revertiq_version": "1.0.0"
  }
}
```

## Common Pitfalls

❌ **Look-ahead bias**: Never use future data in training  
❌ **Overfitting**: Always validate on OOS data  
❌ **Ignoring costs**: Transaction costs kill many strategies  
❌ **Multiple testing**: Must correct p-values when testing many windows  
❌ **Non-stationarity**: Check ADF/KPSS before claiming mean reversion

## Success Metrics

- **Latency**: <15s for full analysis
- **Accuracy**: Matches reference implementation within 1%
- **Reproducibility**: 100% deterministic outputs
- **Coverage**: >80% test coverage
- **Uptime**: API responds to health checks

## Resources

- **Polygon API**: https://polygon.io/docs
- **Statsmodels**: https://www.statsmodels.org/
- **Parquet**: https://arrow.apache.org/docs/python/parquet.html
- **FastAPI**: https://fastapi.tiangolo.com/
- **FDR**: Benjamini & Hochberg (1995)

---

**Next**: Read `01-product-requirements.md` for mathematical details
