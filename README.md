<div align="center">
  <img src="media/logo.png" alt="RevertIQ Logo" width="400"/>
  
  # RevertIQ — Vibe Coding Exercise
  
  **Build a production-grade mean-reversion analytics API from comprehensive specs**
</div>

## What is this?

This is a **vibe coding exercise** — a challenge to build a complete, statistically rigorous mean-reversion analysis platform using only the detailed documentation provided. No hand-holding, no starter code. Just specs, architecture docs, and your engineering skills.

## The Challenge

Build **RevertIQ**: an API-first platform that analyzes historical market data to identify statistically significant mean-reversion trading windows. Think "Stripe for quant analytics" — clean APIs, reproducible results, and production-ready infrastructure.

## 📚 Your Documentation

All specs are in `/docs`:

1. **[01-product-requirements.md](docs/01-product-requirements.md)** — Mathematical foundation, metrics, and statistical tests
2. **[02-api-specification.md](docs/02-api-specification.md)** — Complete REST API contract with request/response schemas
3. **[03-system-architecture.md](docs/03-system-architecture.md)** — System design, data flow, deployment blueprint
4. **[04-ux-design.md](docs/04-ux-design.md)** — User experience philosophy and persona journeys
5. **[05-wireframe-flows.md](docs/05-wireframe-flows.md)** — UI/CLI interaction flows and wireframes

## 🎯 Success Criteria

Your implementation is successful when:

### Core Functionality ✅
- [ ] **POST /v1/analyze** accepts ticker + parameters, returns ranked mean-reversion windows
- [ ] **Walk-forward validation** prevents overfitting (train/test splits)
- [ ] **Statistical rigor**: FDR correction, bootstrap CIs, stationarity tests
- [ ] **Cost modeling**: realistic transaction costs integrated into returns
- [ ] **Async support**: handle long-running jobs with status polling

### Data & Math ✅
- [ ] Integrates with **Polygon.io** (or similar) for market data
- [ ] Computes **z-scores** with configurable detrending (EMA/SMA/VWAP)
- [ ] Implements **Ornstein-Uhlenbeck** half-life estimation
- [ ] Runs **ADF, KPSS, Hurst** tests for mean-reversion validation
- [ ] Applies **Benjamini-Hochberg FDR** correction for multiple testing

### Engineering ✅
- [ ] **Deterministic**: same inputs → identical outputs (data hashing + versioning)
- [ ] **Provenance**: every response includes data_hash, version, timestamps
- [ ] **Caching**: intelligent result caching with TTL
- [ ] **Rate limiting**: per-tenant quotas
- [ ] **Error handling**: structured error responses with field-level validation

### Bonus Points 🌟
- [ ] CLI tool with pretty table output
- [ ] Web dashboard with heatmap visualization
- [ ] Webhook support for async notifications
- [ ] Docker/K8s deployment configs
- [ ] Comprehensive test suite (unit + integration)

## 🚀 Getting Started

### Phase 1: Foundation (Week 1)
1. Read all docs thoroughly
2. Set up project structure (language of your choice)
3. Implement data ingestion from Polygon API
4. Build z-score calculation engine

### Phase 2: Core Analytics (Week 2)
1. Implement walk-forward optimization
2. Add statistical tests (ADF, KPSS, Hurst)
3. Build FDR correction logic
4. Create cost modeling layer

### Phase 3: API Layer (Week 3)
1. Implement REST endpoints per spec
2. Add authentication & rate limiting
3. Build job queue for async processing
4. Implement result caching

### Phase 4: Polish (Week 4)
1. Add provenance & versioning
2. Build CLI tool
3. Create deployment configs
4. Write tests & documentation

## 🛠️ Tech Stack Suggestions

**Backend**: Python (pandas, numpy, statsmodels) or Rust (polars, ndarray)  
**API**: FastAPI, Flask, or Axum  
**Queue**: Redis, RabbitMQ, or SQS  
**Storage**: PostgreSQL + S3/MinIO (Parquet files)  
**Cache**: Redis/KeyDB  
**Data**: Polygon.io API (free tier available)

## 📊 What Makes This Hard (and Fun)

1. **Statistical rigor**: Not just backtesting — proper hypothesis testing and multiple-testing correction
2. **Reproducibility**: Deterministic outputs with full provenance tracking
3. **Performance**: Efficient vectorized operations on large time series
4. **API design**: Clean, well-documented REST API with proper error handling
5. **Production-ready**: Caching, rate limiting, async jobs, observability

## 🎓 Learning Outcomes

By completing this exercise, you'll gain deep experience in:

- Quantitative finance fundamentals (mean reversion, z-scores, OU processes)
- Statistical hypothesis testing and multiple-testing corrections
- Time-series analysis and stationarity tests
- API design and async job processing
- Data engineering (Parquet, caching, provenance)
- Production system architecture

## 📝 Submission Guidelines

When you're done:

1. **Demo video**: Show POST /analyze → results with heatmap
2. **Code walkthrough**: Explain key architectural decisions
3. **Test results**: Show statistical validation on real data
4. **Deployment**: Bonus points for live API endpoint

## 🤝 Community

Share your progress, ask questions, and help others:

- Tag your repos with `#revertiq-vibe-coding`
- Share interesting findings (e.g., "AAPL really does revert on Tuesday mornings!")
- Compare implementations across different tech stacks

## ⚖️ License

This exercise and documentation are provided as-is for educational purposes.

---

**Ready to vibe code?** Start with `docs/01-product-requirements.md` and build something amazing. 🚀
