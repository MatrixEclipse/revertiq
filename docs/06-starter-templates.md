# 🏗️ Starter Templates & Project Structure

## Recommended Project Structures

### Python Implementation

```
revertiq/
├── README.md
├── requirements.txt
├── setup.py
├── .env.example
├── docker-compose.yml
├── src/
│   ├── __init__.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── main.py              # FastAPI app
│   │   ├── routes.py            # Endpoint handlers
│   │   ├── models.py            # Pydantic request/response models
│   │   └── auth.py              # Authentication
│   ├── core/
│   │   ├── __init__.py
│   │   ├── signal.py            # Z-score calculation
│   │   ├── stats.py             # ADF, KPSS, Hurst tests
│   │   ├── walk_forward.py      # Walk-forward engine
│   │   ├── costs.py             # Transaction cost modeling
│   │   └── fdr.py               # Multiple testing correction
│   ├── data/
│   │   ├── __init__.py
│   │   ├── polygon_client.py    # Polygon API wrapper
│   │   ├── storage.py           # Parquet I/O
│   │   └── cache.py             # Redis caching
│   ├── workers/
│   │   ├── __init__.py
│   │   └── analyzer.py          # Async job worker
│   └── utils/
│       ├── __init__.py
│       ├── provenance.py        # Data hashing & versioning
│       └── calendar.py          # Trading calendar
├── tests/
│   ├── test_signal.py
│   ├── test_stats.py
│   ├── test_walk_forward.py
│   └── test_api.py
└── cli/
    └── rtiq.py                  # CLI tool
```

### Rust Implementation

```
revertiq/
├── Cargo.toml
├── .env.example
├── docker-compose.yml
├── src/
│   ├── main.rs
│   ├── api/
│   │   ├── mod.rs
│   │   ├── handlers.rs
│   │   └── models.rs
│   ├── core/
│   │   ├── mod.rs
│   │   ├── signal.rs
│   │   ├── stats.rs
│   │   └── walk_forward.rs
│   ├── data/
│   │   ├── mod.rs
│   │   ├── polygon.rs
│   │   └── storage.rs
│   └── workers/
│       ├── mod.rs
│       └── analyzer.rs
├── tests/
│   ├── integration_tests.rs
│   └── unit_tests.rs
└── cli/
    └── main.rs
```

## Starter Files

### requirements.txt (Python)

```txt
# API Framework
fastapi==0.104.1
uvicorn[standard]==0.24.0
pydantic==2.5.0

# Data Processing
pandas==2.1.3
numpy==1.26.2
polars==0.19.19
pyarrow==14.0.1

# Statistics
statsmodels==0.14.0
scipy==1.11.4

# Market Data
polygon-api-client==1.12.5
requests==2.31.0

# Storage & Cache
redis==5.0.1
psycopg2-binary==2.9.9
sqlalchemy==2.0.23

# Job Queue
celery==5.3.4
rq==1.15.1

# Testing
pytest==7.4.3
pytest-asyncio==0.21.1
httpx==0.25.2

# Utils
python-dotenv==1.0.0
click==8.1.7
rich==13.7.0
```

### Cargo.toml (Rust)

```toml
[package]
name = "revertiq"
version = "1.0.0"
edition = "2021"

[dependencies]
# API Framework
axum = "0.7"
tokio = { version = "1", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"

# Data Processing
polars = { version = "0.35", features = ["lazy", "parquet"] }
ndarray = "0.15"
ndarray-stats = "0.5"

# Statistics
statrs = "0.16"

# Storage
redis = { version = "0.24", features = ["tokio-comp"] }
sqlx = { version = "0.7", features = ["postgres", "runtime-tokio"] }

# HTTP Client
reqwest = { version = "0.11", features = ["json"] }

# Utils
dotenv = "0.15"
chrono = "0.4"
uuid = { version = "1.6", features = ["v4"] }
```

### .env.example

```bash
# API Configuration
API_HOST=0.0.0.0
API_PORT=8000
API_VERSION=1.0.0

# Polygon API
POLYGON_API_KEY=your_key_here

# Database
DATABASE_URL=postgresql://revertiq:password@localhost:5432/revertiq

# Redis
REDIS_URL=redis://localhost:6379/0

# Storage
DATA_LAKE_PATH=./lake
CACHE_TTL_SECONDS=86400

# Job Queue
WORKER_CONCURRENCY=4
MAX_QUEUE_SIZE=100

# Rate Limiting
RATE_LIMIT_PER_MINUTE=60
RATE_LIMIT_PER_DAY=2000

# Analysis Defaults
DEFAULT_TRAIN_DAYS=120
DEFAULT_TEST_DAYS=30
DEFAULT_FDR_ALPHA=0.10
```

### docker-compose.yml

```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://revertiq:password@postgres:5432/revertiq
      - REDIS_URL=redis://redis:6379/0
    depends_on:
      - postgres
      - redis
    volumes:
      - ./lake:/app/lake

  worker:
    build: .
    command: python -m src.workers.analyzer
    environment:
      - DATABASE_URL=postgresql://revertiq:password@postgres:5432/revertiq
      - REDIS_URL=redis://redis:6379/0
    depends_on:
      - postgres
      - redis
    volumes:
      - ./lake:/app/lake

  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_USER=revertiq
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=revertiq
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

### Dockerfile (Python)

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application
COPY . .

# Expose port
EXPOSE 8000

# Run API
CMD ["uvicorn", "src.api.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## Minimal API Starter (Python + FastAPI)

```python
# src/api/main.py
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import Optional
import uuid

app = FastAPI(title="RevertIQ", version="1.0.0")

class AnalyzeRequest(BaseModel):
    ticker: str
    horizon: dict
    bar: dict
    windows: dict
    signal: dict
    params: dict
    costs: dict
    async_mode: bool = False

class AnalyzeResponse(BaseModel):
    analysis_id: str
    status: str
    ticker: str
    windows_ranked: list = []
    global_stats: dict = {}
    diagnostics: dict = {}
    provenance: dict = {}

@app.post("/v1/analyze", response_model=AnalyzeResponse)
async def analyze(request: AnalyzeRequest):
    # TODO: Implement analysis logic
    analysis_id = f"an_{uuid.uuid4().hex[:8]}"
    
    if request.async_mode:
        # Queue job
        return AnalyzeResponse(
            analysis_id=analysis_id,
            status="queued",
            ticker=request.ticker
        )
    
    # Run sync analysis
    # results = run_analysis(request)
    
    return AnalyzeResponse(
        analysis_id=analysis_id,
        status="complete",
        ticker=request.ticker,
        provenance={
            "revertiq_version": "1.0.0",
            "data_hash": "sha256:..."
        }
    )

@app.get("/healthz")
async def health():
    return {"status": "ok"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

## CLI Starter (Python + Click)

```python
# cli/rtiq.py
import click
from rich.console import Console
from rich.table import Table

console = Console()

@click.group()
def cli():
    """RevertIQ CLI - Mean Reversion Analysis Tool"""
    pass

@cli.command()
@click.argument('ticker')
@click.option('--start', default='2023-01-01', help='Start date')
@click.option('--end', default='2025-10-01', help='End date')
def run(ticker, start, end):
    """Run mean reversion analysis for TICKER"""
    console.print(f"[bold]Analyzing {ticker}[/bold] from {start} to {end}")
    
    # TODO: Call API or run analysis directly
    
    # Display results
    table = Table(title="Best OOS Windows")
    table.add_column("DOW", style="cyan")
    table.add_column("Window", style="magenta")
    table.add_column("Sharpe", justify="right", style="green")
    table.add_column("Ret(bp)", justify="right")
    table.add_column("p(FDR)", justify="right")
    
    # TODO: Add real results
    table.add_row("Tue", "10:45-11:30", "1.32", "3.4", "0.03")
    
    console.print(table)

@cli.command()
@click.argument('analysis_id')
def status(analysis_id):
    """Check status of analysis job"""
    console.print(f"Status for {analysis_id}: [green]complete[/green]")

if __name__ == '__main__':
    cli()
```

## Database Schema (PostgreSQL)

```sql
-- migrations/001_initial.sql

CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    api_key VARCHAR(255) UNIQUE NOT NULL,
    tier VARCHAR(50) DEFAULT 'starter',
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE analyses (
    id VARCHAR(50) PRIMARY KEY,
    tenant_id UUID REFERENCES tenants(id),
    ticker VARCHAR(10) NOT NULL,
    status VARCHAR(20) NOT NULL,
    params JSONB NOT NULL,
    results JSONB,
    data_hash VARCHAR(255),
    created_at TIMESTAMP DEFAULT NOW(),
    completed_at TIMESTAMP
);

CREATE INDEX idx_analyses_tenant ON analyses(tenant_id);
CREATE INDEX idx_analyses_status ON analyses(status);
CREATE INDEX idx_analyses_ticker ON analyses(ticker);

CREATE TABLE windows (
    id SERIAL PRIMARY KEY,
    analysis_id VARCHAR(50) REFERENCES analyses(id),
    dow VARCHAR(3),
    window VARCHAR(20),
    oos_sharpe DECIMAL(10, 4),
    oos_ret_bp DECIMAL(10, 4),
    fdr_adj_p DECIMAL(10, 6),
    n_trades INTEGER
);

CREATE INDEX idx_windows_analysis ON windows(analysis_id);
```

---

**Next Steps**: Copy the relevant starter template for your chosen stack and begin implementation!
