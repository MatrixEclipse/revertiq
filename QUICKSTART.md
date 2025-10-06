# ⚡ Quick Start Guide

Get started building RevertIQ in 15 minutes.

## Prerequisites

- Python 3.10+ (or Rust 1.70+)
- Polygon.io API key ([get free tier](https://polygon.io/))
- PostgreSQL & Redis (or use Docker)

## 30-Second Setup

```bash
# Clone or create project
mkdir revertiq && cd revertiq

# Copy environment template
cat > .env << EOF
POLYGON_API_KEY=your_key_here
DATABASE_URL=postgresql://revertiq:password@localhost:5432/revertiq
REDIS_URL=redis://localhost:6379/0
EOF

# Start services (Docker)
docker-compose up -d postgres redis

# Install dependencies (Python)
pip install -r requirements.txt

# Run API
python -m src.api.main
```

## Your First Analysis

```bash
# Using CLI
rtiq run AAPL --start 2023-01-01 --end 2024-12-31

# Using API
curl -X POST http://localhost:8000/v1/analyze \
  -H "Content-Type: application/json" \
  -d '{
    "ticker": "AAPL",
    "horizon": {"start": "2023-01-01", "end": "2024-12-31"},
    "bar": {"interval": "1m", "rth_only": true},
    "windows": {"spec": "09:45-16:00/30m"},
    "signal": {
      "detrend": {"type": "ema", "lookback": 20},
      "zscore": {"lookback": 60, "vol": "ewm"},
      "side": "long_only"
    },
    "params": {
      "entry_grid": [-1.0, -1.25, -1.5],
      "exit_grid": [0.0, -0.25],
      "hold_grid_min": [15, 30, 45]
    },
    "costs": {
      "spread_bp": 0.5,
      "slippage_bp": 0.8,
      "fee_bp": 0.2,
      "mode": "bars"
    },
    "walk_forward": {"train_days": 120, "test_days": 30, "step_days": 30},
    "significance": {"fdr_alpha": 0.10, "min_trades": 50},
    "async": false
  }'
```

## Expected Output

```json
{
  "analysis_id": "an_3kX9v",
  "status": "complete",
  "ticker": "AAPL",
  "windows_ranked": [
    {
      "dow": "Tue",
      "window": "10:45-11:30",
      "oos_sharpe": 1.32,
      "oos_ret_per_trade_bp": 3.4,
      "fdr_adj_p": 0.03,
      "n_trades": 418
    }
  ],
  "global_stats": {
    "total_trades": 1240,
    "oos_sharpe": 1.28,
    "avg_ret_bp": 3.1
  },
  "diagnostics": {
    "stationarity": {
      "adf_p": 0.01,
      "mean_reverting": true,
      "hurst": 0.38
    }
  },
  "provenance": {
    "data_hash": "sha256:7f...",
    "revertiq_version": "1.0.0"
  }
}
```

## Implementation Roadmap

### Week 1: Foundation
1. Read `docs/01-product-requirements.md`
2. Set up Polygon API client
3. Implement z-score calculation
4. Test on synthetic data

### Week 2: Statistics
1. Add ADF, KPSS, Hurst tests
2. Implement walk-forward engine
3. Add FDR correction
4. Validate on real data

### Week 3: API
1. Build FastAPI endpoints
2. Add async job queue
3. Implement caching
4. Add authentication

### Week 4: Polish
1. Build CLI tool
2. Add comprehensive tests
3. Create Docker deployment
4. Write documentation

## Next Steps

1. **Read the docs**: Start with `docs/00-implementation-guide.md`
2. **Choose your stack**: See `docs/06-starter-templates.md`
3. **Validate early**: Use `docs/07-validation-testing.md`
4. **Join the community**: Share progress with `#revertiq-vibe-coding`

## Need Help?

- 📖 **Stuck on math?** → See `docs/01-product-requirements.md`
- 🔧 **API questions?** → See `docs/02-api-specification.md`
- 🏗️ **Architecture?** → See `docs/03-system-architecture.md`
- ✅ **Testing?** → See `docs/07-validation-testing.md`

---

**Ready to build?** Start with the implementation guide! 🚀
