# 🤝 Contributing to RevertIQ Vibe Coding Exercise

## How to Participate

This is a **self-directed coding exercise**. There's no "official" implementation — the goal is for you to build your own version based on the specs.

## Sharing Your Implementation

### 1. Fork & Build
- Fork this repo (or start fresh)
- Build your implementation in your preferred language
- Follow the specs in `/docs`

### 2. Document Your Approach
Create a `SOLUTION.md` in your repo with:
- **Tech stack choices** and why
- **Key architectural decisions**
- **Interesting findings** (e.g., which tickers show strongest mean reversion)
- **Challenges faced** and how you solved them
- **Performance benchmarks**

### 3. Share Results
- Post your repo with tag `#revertiq-vibe-coding`
- Share interesting statistical findings
- Compare approaches with other implementations

## Implementation Variants

Feel free to explore different approaches:

### Language Variants
- **Python**: pandas, numpy, statsmodels (most common)
- **Rust**: polars, ndarray (performance-focused)
- **Julia**: DataFrames.jl, Statistics.jl (scientific computing)
- **Go**: gonum (systems programming)

### Architecture Variants
- **Monolith**: Single service with embedded queue
- **Microservices**: Separate API, worker, and storage services
- **Serverless**: Lambda/Cloud Functions with managed queues
- **Streaming**: Real-time analysis with Kafka/Flink

### Feature Extensions
- **Live Scout**: Real-time z-score monitoring
- **Portfolio Mode**: Multi-ticker correlation analysis
- **Regime Detection**: GARCH-based volatility clustering
- **ML Enhancement**: Use ML to predict reversion probability

## Code Quality Guidelines

Even though this is a vibe coding exercise, aim for production quality:

- ✅ **Tests**: Unit tests for statistical functions
- ✅ **Docs**: Clear README and API documentation
- ✅ **Types**: Type hints (Python) or strong typing (Rust/Go)
- ✅ **Linting**: Follow language-specific style guides
- ✅ **Git**: Meaningful commit messages

## Validation Checklist

Before sharing your implementation:

- [ ] Runs end-to-end on real Polygon data
- [ ] Produces statistically valid results (p-values, CIs)
- [ ] Handles errors gracefully
- [ ] Includes at least basic tests
- [ ] Has clear setup instructions
- [ ] Demonstrates deterministic outputs

## Community Guidelines

### Do's ✅
- Share interesting findings and insights
- Help others debug statistical issues
- Compare performance across implementations
- Propose spec improvements or clarifications

### Don'ts ❌
- Don't claim your implementation is "official"
- Don't copy others' code without attribution
- Don't share production API keys
- Don't make unsubstantiated trading claims

## Questions & Discussion

For questions about the specs:
1. Check the docs thoroughly first
2. Look for similar questions in discussions
3. Open an issue with `[Question]` tag

For implementation help:
- Share your approach and what you've tried
- Include relevant code snippets
- Describe expected vs actual behavior

## Spec Improvements

Found an ambiguity or error in the specs? Great!

1. Open an issue describing the problem
2. Suggest a clarification or fix
3. If accepted, submit a PR to update the docs

## Recognition

Outstanding implementations may be featured:
- **Most Performant**: Fastest analysis time
- **Most Complete**: Implements all bonus features
- **Most Creative**: Novel approach or visualization
- **Best Documented**: Clearest explanation of approach

## License

By contributing, you agree your code is your own and can be shared under your chosen license.

---

**Happy vibe coding!** 🚀
