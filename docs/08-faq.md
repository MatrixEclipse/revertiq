# ❓ Frequently Asked Questions

## General

### What is RevertIQ?
A vibe coding exercise to build a production-grade mean-reversion analytics API. You get comprehensive specs, you build the implementation.

### Is this a real product?
No, it's an educational exercise. The specs are production-quality, but there's no official implementation or company.

### What do I get?
- Complete API specification
- Mathematical foundation & formulas
- System architecture blueprint
- UX/UI wireframes
- Implementation guide

### What don't I get?
- Working code (you build it!)
- Data subscriptions
- Support or SLAs
- Guarantees of profitability

## Technical

### Which language should I use?
**Python** is recommended for beginners (pandas, statsmodels).  
**Rust** for performance enthusiasts (polars, ndarray).  
**Julia** or **Go** also work great.

### Do I need a Polygon subscription?
No, the free tier (5 API calls/min) is sufficient for development. You can also use mock data.

### How long does this take?
- **Minimal viable**: 1-2 weeks (core API + basic stats)
- **Complete**: 3-4 weeks (all features + tests + deployment)
- **Production-ready**: 6-8 weeks (monitoring, scaling, polish)

### Can I use this for real trading?
**No.** This is educational. Past performance ≠ future results. Mean reversion can fail catastrophically in trending markets.

## Implementation

### Where do I start?
1. Read `docs/01-product-requirements.md`
2. Set up Polygon API client
3. Implement z-score calculation
4. Validate on synthetic data

### What's the hardest part?
Usually the **walk-forward validation** (preventing look-ahead bias) and **FDR correction** (multiple testing).

### Can I skip features?
Yes! Start with:
- Sync API only (no async)
- Single ticker (no batch)
- Basic costs (no quotes mode)
- CLI only (no web UI)

### How do I validate correctness?
Use the test scenarios in `docs/07-validation-testing.md`. Your ADF/Hurst results should match statsmodels.

## Statistics

### What is mean reversion?
The tendency of prices to return to an average after deviating. Think "rubber band effect."

### What's a z-score?
Normalized deviation from mean: `z = (price - mean) / std_dev`. Values like -2 or +2 indicate extremes.

### Why walk-forward validation?
To prevent overfitting. You optimize on past data, test on future data, then roll forward.

### What is FDR correction?
When testing 100 windows, some will look good by chance. FDR (False Discovery Rate) correction filters out statistical flukes.

### What's the Hurst exponent?
- **H < 0.5**: Mean-reverting (good!)
- **H = 0.5**: Random walk (neutral)
- **H > 0.5**: Trending (bad for mean reversion)

## Data

### Where does market data come from?
Polygon.io is recommended. Alternatives: Alpha Vantage, IEX Cloud, Yahoo Finance.

### What resolution do I need?
**1-minute bars** are ideal. 5-minute works too. Daily bars won't show intraday patterns.

### How much data?
At least **1 year** for meaningful statistics. 2-3 years is better for walk-forward validation.

### What about splits/dividends?
Use **adjusted prices** from your data provider. This is critical for accuracy.

## API Design

### Why REST instead of GraphQL?
Simplicity and caching. REST is easier for this use case.

### Why async jobs?
Some analyses take 10-30 seconds. Async prevents timeout issues and allows scaling.

### What's provenance?
Metadata proving reproducibility: data hash, version, timestamps. Critical for trust.

### Why idempotency keys?
Prevents duplicate analyses if a request is retried (network issues, etc.).

## Performance

### How fast should it be?
- **Cached**: <1s
- **Standard**: <15s
- **Heavy (quotes mode)**: <60s

### How do I optimize?
1. **Vectorize**: Use pandas/numpy, not Python loops
2. **Cache**: Parquet files, intermediate z-scores
3. **Parallelize**: Process windows concurrently
4. **Profile**: Find bottlenecks with cProfile/flamegraph

### Can I use GPUs?
Overkill for this problem. CPU vectorization is sufficient.

## Deployment

### Do I need Kubernetes?
No. Start with docker-compose. K8s is optional for scaling.

### What about serverless?
Possible but tricky (cold starts, timeouts). Better for async workers than API.

### How do I handle secrets?
Environment variables + .env file locally. KMS/Secrets Manager in production.

## Business

### Can I sell this?
You can sell **your implementation**, but attribute the specs if you use them directly.

### Is there a license?
The docs are educational/reference. Your code is yours under your chosen license.

### Can I use this in a startup?
Sure, but understand the risks. Quantitative trading is highly competitive and risky.

## Troubleshooting

### My Sharpe ratios are all negative
- Check transaction costs (too high?)
- Verify ticker shows mean reversion (try AAPL first)
- Ensure you're using adjusted prices

### ADF test always fails
- Are you testing z-scores or raw prices? (Should be z-scores)
- Is your lookback window too short? (Try 60+)

### Results change every run
- Set random seeds for bootstrap
- Implement data hashing
- Check for race conditions in async code

### API is slow (>30s)
- Profile your code (cProfile)
- Vectorize loops (pandas/numpy)
- Add caching for intermediate results
- Reduce window count or date range

### Out of memory
- Process data in chunks
- Use Parquet instead of CSV
- Stream results instead of loading all at once

## Philosophy

### Why "vibe coding"?
You're given the vision (specs) and vibe (production-quality), then you code it your way. No hand-holding, no starter code.

### What's the learning goal?
Build a **complete system** from specs alone. This mirrors real-world engineering: requirements → architecture → implementation.

### Is this realistic?
Yes! The specs are production-quality. Many startups begin with docs like these.

### What if I get stuck?
1. Re-read the relevant doc section
2. Search for the specific algorithm (e.g., "ADF test Python")
3. Ask in discussions (describe what you've tried)
4. Simplify: start with minimal features, add complexity later

---

**Still have questions?** Check the docs or open a discussion!
