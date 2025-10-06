# 📚 RevertIQ Documentation Index

Complete documentation for building a production-grade mean-reversion analytics platform.

## 📖 Reading Order

### Start Here
1. **[00-implementation-guide.md](00-implementation-guide.md)** - Step-by-step implementation checklist
2. **[../QUICKSTART.md](../QUICKSTART.md)** - Get started in 15 minutes

### Core Specifications
3. **[01-product-requirements.md](01-product-requirements.md)** - Mathematical foundation & metrics
4. **[02-api-specification.md](02-api-specification.md)** - Complete REST API contract
5. **[03-system-architecture.md](03-system-architecture.md)** - System design & deployment

### Design & UX
6. **[04-ux-design.md](04-ux-design.md)** - User experience philosophy
7. **[05-wireframe-flows.md](05-wireframe-flows.md)** - UI/CLI interaction flows

### Implementation Support
8. **[06-starter-templates.md](06-starter-templates.md)** - Project structures & boilerplate
9. **[07-validation-testing.md](07-validation-testing.md)** - Testing & validation guide
10. **[08-faq.md](08-faq.md)** - Frequently asked questions

## 🎯 Quick Navigation

### By Role
- **Developer**: Start with 00 → 01 → 02 → 06
- **Architect**: Read 03 → 02 → 01
- **Data Scientist**: Focus on 01 → 07
- **Designer**: See 04 → 05

### By Topic
- **Math & Statistics**: 01-product-requirements.md
- **API Design**: 02-api-specification.md
- **Infrastructure**: 03-system-architecture.md
- **Testing**: 07-validation-testing.md
- **Getting Started**: 00-implementation-guide.md, QUICKSTART.md

## 📊 Document Summary

| Doc | Topic | Pages | Key Content |
|-----|-------|-------|-------------|
| 00 | Implementation | 3 | Phase-by-phase checklist, tips, pitfalls |
| 01 | Product Reqs | 15 | Z-scores, walk-forward, FDR, OU process |
| 02 | API Spec | 10 | Endpoints, schemas, errors, examples |
| 03 | Architecture | 8 | Data flow, storage, compute, deployment |
| 04 | UX Design | 7 | Personas, journeys, design principles |
| 05 | Wireframes | 8 | CLI/Web layouts, interaction flows |
| 06 | Templates | 10 | Project structures, starter code |
| 07 | Testing | 6 | Test scenarios, validation, benchmarks |
| 08 | FAQ | 5 | Common questions & troubleshooting |

## 🔑 Key Concepts

### Statistical
- **Z-Score**: Normalized price deviation from mean
- **Walk-Forward**: Anti-overfitting validation technique
- **FDR**: False Discovery Rate correction for multiple testing
- **Hurst Exponent**: Measure of mean-reversion strength
- **OU Process**: Ornstein-Uhlenbeck mean-reversion model

### Engineering
- **Provenance**: Data hash + version for reproducibility
- **Idempotency**: Safe request retries
- **Async Jobs**: Long-running analysis handling
- **Parquet**: Columnar storage format

## 🎓 Learning Path

### Beginner (Week 1-2)
- Read 00, 01, 06
- Implement z-score calculation
- Test on synthetic data
- Build minimal API

### Intermediate (Week 3-4)
- Read 02, 03, 07
- Add walk-forward validation
- Implement statistical tests
- Deploy with Docker

### Advanced (Week 5+)
- Read 04, 05
- Build CLI/Web UI
- Add webhooks & monitoring
- Optimize performance

## 🛠️ External Resources

- **Polygon API**: https://polygon.io/docs
- **Statsmodels**: https://www.statsmodels.org/
- **FastAPI**: https://fastapi.tiangolo.com/
- **Parquet**: https://arrow.apache.org/docs/python/parquet.html

---

**Ready to build?** Start with `00-implementation-guide.md`! 🚀
