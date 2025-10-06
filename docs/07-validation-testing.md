# ✅ Validation & Testing Guide

## How to Validate Your Implementation

### Test Data Sets

Use these known scenarios to validate correctness:

#### Scenario 1: Perfect Mean Reversion
```python
# Generate synthetic mean-reverting series
import numpy as np
np.random.seed(42)
theta = 0.15  # reversion speed
mu = 100      # mean level
sigma = 2     # volatility
dt = 1/252/6.5  # 1-minute bars

prices = [100]
for _ in range(1000):
    dz = np.random.normal(0, np.sqrt(dt))
    dx = theta * (mu - prices[-1]) * dt + sigma * dz
    prices.append(prices[-1] + dx)

# Expected: High Sharpe, low p-value, Hurst < 0.5
```

#### Scenario 2: Random Walk
```python
# Should show NO mean reversion
prices = [100]
for _ in range(1000):
    prices.append(prices[-1] + np.random.normal(0, 1))

# Expected: Low Sharpe, high p-value, Hurst ≈ 0.5
```

#### Scenario 3: Trending
```python
# Should fail stationarity tests
prices = [100 + 0.01*i + np.random.normal(0, 1) for i in range(1000)]

# Expected: ADF fails, KPSS fails, Hurst > 0.5
```

### Unit Test Examples

```python
# tests/test_signal.py
def test_zscore_calculation():
    prices = pd.Series([100, 101, 99, 102, 98, 103, 97])
    z = compute_zscore(prices, lookback=5)
    
    assert len(z) == len(prices)
    assert z.iloc[:4].isna().all()  # First 4 NaN
    assert abs(z.iloc[-1]) > 0  # Last value computed

def test_entry_signal_detection():
    z_scores = pd.Series([0.5, -1.2, -1.6, -0.8, 0.2])
    entries = detect_entries(z_scores, threshold=-1.5)
    
    assert entries[2] == True  # z=-1.6 triggers
    assert entries.sum() == 1  # Only one entry

# tests/test_stats.py
def test_adf_on_mean_reverting_series():
    # Use Scenario 1 data
    result = run_adf_test(mean_reverting_prices)
    assert result['p_value'] < 0.05
    assert result['is_stationary'] == True

def test_hurst_exponent():
    # Random walk should have H ≈ 0.5
    h = compute_hurst(random_walk_prices)
    assert 0.45 < h < 0.55

# tests/test_walk_forward.py
def test_no_lookahead_bias():
    # Ensure test data never leaks into training
    splits = create_walk_forward_splits(
        data, train_days=120, test_days=30
    )
    for train, test in splits:
        assert train.index.max() < test.index.min()

# tests/test_fdr.py
def test_fdr_correction():
    raw_pvals = [0.01, 0.05, 0.10, 0.20, 0.50]
    adjusted = apply_fdr_correction(raw_pvals, alpha=0.10)
    
    # Should reject first few, accept last
    assert adjusted[0] < 0.10
    assert adjusted[-1] > 0.10
```

### Integration Tests

```python
# tests/test_api.py
import httpx

def test_analyze_endpoint_sync():
    response = httpx.post("http://localhost:8000/v1/analyze", json={
        "ticker": "AAPL",
        "horizon": {"start": "2023-01-01", "end": "2023-12-31"},
        "bar": {"interval": "1m", "rth_only": True},
        # ... rest of params
        "async": False
    })
    
    assert response.status_code == 200
    data = response.json()
    assert data['status'] == 'complete'
    assert 'windows_ranked' in data
    assert 'provenance' in data

def test_deterministic_outputs():
    # Same request twice should return identical data_hash
    req = {...}  # Same params
    
    r1 = httpx.post("/v1/analyze", json=req).json()
    r2 = httpx.post("/v1/analyze", json=req).json()
    
    assert r1['provenance']['data_hash'] == r2['provenance']['data_hash']
```

### Performance Benchmarks

```python
# tests/test_performance.py
import time

def test_analysis_latency():
    start = time.time()
    result = run_analysis("AAPL", "2023-01-01", "2023-12-31")
    elapsed = time.time() - start
    
    assert elapsed < 15.0  # Must complete in <15s

def test_cache_hit_latency():
    # First call (cache miss)
    run_analysis("AAPL", ...)
    
    # Second call (cache hit)
    start = time.time()
    run_analysis("AAPL", ...)  # Same params
    elapsed = time.time() - start
    
    assert elapsed < 1.0  # Cached should be <1s
```

## Validation Checklist

### Mathematical Correctness ✅
- [ ] Z-scores match manual calculation
- [ ] ADF test matches statsmodels output
- [ ] Hurst exponent in valid range [0, 1]
- [ ] FDR correction reduces p-values correctly
- [ ] Walk-forward has no look-ahead bias

### API Compliance ✅
- [ ] Request/response match spec exactly
- [ ] Error codes follow spec (INVALID_ARGUMENT, etc.)
- [ ] Rate limit headers present
- [ ] Idempotency-Key works
- [ ] Async mode returns 202 Accepted

### Statistical Rigor ✅
- [ ] Confidence intervals cover true parameter
- [ ] FDR controls false discovery rate at α
- [ ] Bootstrap p-values match analytical
- [ ] Cost-adjusted returns < raw returns

### Reproducibility ✅
- [ ] Same inputs → identical outputs
- [ ] data_hash changes when data changes
- [ ] Version tag in all responses
- [ ] Provenance includes all metadata

### Performance ✅
- [ ] Sync analysis completes in <15s
- [ ] Cache hits return in <1s
- [ ] Handles 50+ concurrent requests
- [ ] Memory usage stays bounded

## Reference Outputs

### Expected AAPL Results (2023-2024)

Using default parameters, you should see approximately:

```json
{
  "windows_ranked": [
    {
      "dow": "Tue",
      "window": "10:30-11:00",
      "oos_sharpe": 1.1-1.4,
      "oos_ret_per_trade_bp": 2.5-4.0,
      "fdr_adj_p": 0.02-0.08
    }
  ],
  "diagnostics": {
    "stationarity": {
      "adf_p": 0.001-0.05,
      "mean_reverting": true,
      "hurst": 0.35-0.45
    },
    "ou_half_life_min": 20-35
  }
}
```

*Note: Exact values depend on data provider and date range*

## Common Issues & Fixes

### Issue: All p-values are 0.0
**Cause**: Not enough bootstrap samples  
**Fix**: Increase bootstrap iterations to 10,000+

### Issue: Sharpe ratios are negative
**Cause**: Costs too high or no mean reversion  
**Fix**: Check cost parameters, verify ticker shows reversion

### Issue: ADF test always fails
**Cause**: Using raw prices instead of z-scores  
**Fix**: Test stationarity on z-score series, not prices

### Issue: Different results each run
**Cause**: Missing random seed or data hash  
**Fix**: Set numpy/random seeds, implement data hashing

### Issue: Analysis takes >60s
**Cause**: Inefficient loops, not vectorized  
**Fix**: Use pandas/numpy vectorized operations

## Acceptance Criteria

Your implementation passes if:

1. **Correctness**: Produces statistically valid results on test data
2. **Compliance**: API matches spec exactly
3. **Performance**: Meets latency targets
4. **Reproducibility**: Deterministic outputs with provenance
5. **Robustness**: Handles errors gracefully

---

**Next**: Run full test suite and validate against reference data
