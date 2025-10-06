# 🎯 RevertIQ — Vibe Coding Exercise Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   RevertIQ: Build a Production-Grade Mean-Reversion API        │
│   From Comprehensive Specs to Working Implementation           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## What You're Building

A **statistically rigorous API** that analyzes historical market data to identify when and where mean-reversion trading strategies work best.

### Input
```json
{
  "ticker": "AAPL",
  "horizon": {"start": "2023-01-01", "end": "2024-12-31"},
  "signal": {"detrend": "ema", "zscore": {...}},
  "params": {"entry_grid": [-1.0, -1.5], ...}
}
```

### Output
```json
{
  "windows_ranked": [
    {
      "dow": "Tue",
      "window": "10:45-11:30",
      "oos_sharpe": 1.32,
      "oos_ret_per_trade_bp": 3.4,
      "fdr_adj_p": 0.03,
      "half_life_min": 27
    }
  ],
  "diagnostics": {
    "stationarity": {"adf_p": 0.01, "hurst": 0.38},
    "ou_half_life_min": 27.6
  },
  "provenance": {"data_hash": "sha256:...", "version": "1.0.0"}
}
```

## The Challenge

Build this **without starter code**. You get:
- ✅ Complete specifications
- ✅ Mathematical formulas
- ✅ API contracts
- ✅ Architecture blueprints
- ✅ Test scenarios

You provide:
- 🔨 Implementation
- 🧪 Tests
- 🚀 Deployment

## Project Structure

```
revertiq/
├── README.md                    ← Start here!
├── QUICKSTART.md                ← 15-minute setup
├── CONTRIBUTING.md              ← How to share your work
├── PROJECT_OVERVIEW.md          ← This file
├── .gitignore
│
├── docs/
│   ├── README.md                ← Documentation index
│   ├── 00-implementation-guide.md   ← Step-by-step checklist
│   ├── 01-product-requirements.md   ← Math & statistics
│   ├── 02-api-specification.md      ← REST API contract
│   ├── 03-system-architecture.md    ← System design
│   ├── 04-ux-design.md              ← User experience
│   ├── 05-wireframe-flows.md        ← UI/CLI flows
│   ├── 06-starter-templates.md      ← Boilerplate code
│   ├── 07-validation-testing.md     ← Test scenarios
│   └── 08-faq.md                    ← Common questions
│
└── [Your implementation goes here]
```

## Tech Stack (Suggested)

```
┌─────────────┐
│   Client    │  CLI, Web UI, API consumers
└──────┬──────┘
       │
┌──────▼──────┐
│  API Layer  │  FastAPI / Axum / Express
└──────┬──────┘
       │
┌──────▼──────┐
│  Core Logic │  Z-scores, Walk-forward, FDR
└──────┬──────┘
       │
┌──────▼──────┐
│ Data Layer  │  Polygon API → Parquet → PostgreSQL
└─────────────┘
```

**Languages**: Python (recommended), Rust, Julia, Go  
**Storage**: PostgreSQL + Parquet files  
**Cache**: Redis  
**Queue**: Redis/RabbitMQ/SQS  
**Data**: Polygon.io (free tier works)

## Implementation Timeline

```
Week 1: Foundation
├── Day 1-2: Read docs, setup environment
├── Day 3-4: Polygon API + z-score calculation
└── Day 5-7: Statistical tests (ADF, Hurst)

Week 2: Core Analytics
├── Day 8-10: Walk-forward validation
├── Day 11-12: FDR correction
└── Day 13-14: Cost modeling

Week 3: API & Infrastructure
├── Day 15-17: REST endpoints
├── Day 18-19: Async jobs + caching
└── Day 20-21: Auth + rate limiting

Week 4: Polish
├── Day 22-24: CLI tool
├── Day 25-26: Tests
└── Day 27-30: Deployment + docs
```

## Success Criteria

Your implementation is **complete** when:

### ✅ Core Features
- [x] POST /v1/analyze returns ranked windows
- [x] Walk-forward prevents overfitting
- [x] FDR correction controls false discoveries
- [x] Cost modeling integrated
- [x] Async job support

### ✅ Statistical Rigor
- [x] ADF, KPSS, Hurst tests
- [x] Bootstrap confidence intervals
- [x] OU half-life estimation
- [x] Deterministic outputs

### ✅ Engineering Quality
- [x] API matches spec exactly
- [x] Provenance tracking (data_hash + version)
- [x] Result caching
- [x] Error handling
- [x] Tests (>80% coverage)

### 🌟 Bonus
- [ ] CLI with pretty output
- [ ] Web dashboard with heatmaps
- [ ] Webhooks for async notifications
- [ ] Docker deployment
- [ ] Live monitoring

## Key Concepts to Master

### Statistics
- **Mean Reversion**: Prices return to average after deviating
- **Z-Score**: `(price - mean) / std_dev` — normalized deviation
- **Walk-Forward**: Train on past, test on future, roll forward
- **FDR**: Control false discoveries when testing many hypotheses
- **Hurst < 0.5**: Indicates mean-reverting behavior

### Engineering
- **Provenance**: Every response includes data_hash + version
- **Idempotency**: Same request → same result (safe retries)
- **Vectorization**: Use numpy/pandas, not Python loops
- **Caching**: Store results, intermediate calculations

## Learning Outcomes

By completing this, you'll gain expertise in:

1. **Quantitative Finance**: Mean reversion, z-scores, OU processes
2. **Statistics**: Hypothesis testing, multiple testing correction
3. **Time Series**: Stationarity tests, autocorrelation
4. **API Design**: REST, async jobs, versioning
5. **Data Engineering**: Parquet, caching, provenance
6. **System Architecture**: Queues, workers, deployment

## Getting Started

### Option 1: Guided Path
1. Read `QUICKSTART.md`
2. Follow `docs/00-implementation-guide.md`
3. Use templates from `docs/06-starter-templates.md`
4. Validate with `docs/07-validation-testing.md`

### Option 2: Deep Dive
1. Read all docs in order (00 → 08)
2. Design your architecture
3. Implement from scratch
4. Compare with reference specs

### Option 3: Speedrun
1. Skim `01-product-requirements.md`
2. Copy starter template from `06`
3. Build minimal viable API
4. Iterate and expand

## Resources

- **Docs**: `/docs` folder (10 comprehensive guides)
- **Community**: Tag `#revertiq-vibe-coding`
- **Questions**: See `docs/08-faq.md`
- **Help**: Open a discussion

## Philosophy

This is **vibe coding**: you're given the vision (production-quality specs) and the vibe (statistical rigor + clean APIs), then you code it your way.

No hand-holding. No starter code. Just specs and your skills.

This mirrors real-world engineering: requirements → architecture → implementation.

## What Makes This Hard (and Fun)

1. **Statistical rigor** — Not just backtesting, proper hypothesis testing
2. **Reproducibility** — Deterministic outputs with full provenance
3. **Performance** — Efficient vectorized operations on large datasets
4. **API design** — Clean, well-documented REST API
5. **Production-ready** — Caching, rate limiting, async jobs

## Final Notes

⚠️ **Disclaimer**: This is educational. Don't use for real trading without understanding the risks.

📝 **License**: Docs are reference material. Your code is yours.

🤝 **Sharing**: Encouraged! Tag your repos, share findings, help others.

---

**Ready to vibe code?** 

Start with `README.md` → `QUICKSTART.md` → `docs/00-implementation-guide.md`

Let's build something amazing! 🚀
