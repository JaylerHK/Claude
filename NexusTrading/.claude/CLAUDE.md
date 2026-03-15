# CLAUDE.md — NexusTrading

> Crypto quantitative trading platform. Claude Code's onboarding guide.

---

## What

Real-time crypto trading platform with Polymarket integration, arbitrage detection, and portfolio management.

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| **Backend** | FastAPI, Python 3.11 |
| **Database** | PostgreSQL 15, TimescaleDB (time-series) |
| **Cache** | Redis |
| **APIs** | Binance, Polymarket, CoinGecko |
| **Frontend** | React 18, TypeScript, Tailwind |
| **Testing** | pytest, pytest-cov |

---

## Why

We need a system that:
1. Monitors crypto markets in real-time
2. Executes trades via Polymarket
3. Detects cross-exchange arbitrage opportunities
4. Manages portfolio risk automatically

---

## How — Commands

```bash
# Development
docker-compose up              # Start all services
docker-compose up -d           # Detached

# Testing
pytest -v                     # Run all tests
pytest -v --cov=src          # With coverage
pytest -k "test_strategy"    # Specific tests

# Linting
ruff check .
ruff format .

# Database
alembic upgrade head          # Run migrations
alembic migration create <name>  # Create migration
```

---

## Architecture

```
src/
├── api/              # FastAPI routes, schemas
├── models/           # SQLAlchemy models
├── services/         # Business logic
├── strategies/       # Trading strategies
├── indicators/       # Technical indicators
├── arbitrage/        # Cross-exchange arbitrage
├── polymarket/       # Polymarket API client
├── binance/         # Binance API client
└── utils/           # Helpers (rates, logging)
```

---

## Patterns We Use

| Pattern | When |
|---------|------|
| **Async/await** | All I/O (API calls, DB queries) |
| **Pydantic** | Request/response validation |
| **SQLAlchemy** | Database ORM |
| **Circuit breaker** | External API calls |
| **Exponential backoff** | Failed API retries |

---

## Patterns We DON'T Use

- ❌ Synchronous blocking in handlers
- ❌ Raw SQL (use ORM)
- ❌ Print statements (use logging)
- ❌ Hardcoded config (use .env)

---

## Trading Rules (NEVER violate)

1. **Never execute real trades** — paper trading only by default
2. **Always set stop-loss** — no exceptions
3. **Max 10% per position** — portfolio risk management
4. **Log every signal** — timestamp, price, confidence
5. **No revenge trading** — after loss, cooldown 1 hour

---

## API Conventions

```python
# Error response
{"error": "message", "code": "ERR_CODE"}

# Success response
{"data": {...}, "meta": {"timestamp": "ISO8601"}}
```

---

## Testing

```python
# Test naming
test_<strategy>_<scenario>_<expected>

# Example
test_ma_crossover_bull_market_buy_signal
```

- Mock all external API calls
- Minimum 80% coverage
- Include edge cases

---

## Known Issues

- Polymarket API rate limits: 60 req/min
- Binance WebSocket reconnects on disconnect
- TimescaleDB requires hypertables for time-series

---

## Code Examples

- Trading signal: `src/strategies/base.py`
- API endpoint: `src/api/v1/signals.py`
- Arbitrage detection: `src/arbitrage/detector.py`

---

## Refs

- @README.md — Setup guide
- @docs/api.md — API documentation
- @.env.example — Environment variables
