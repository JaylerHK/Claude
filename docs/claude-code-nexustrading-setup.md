# Claude Code Best Practices — COMPLETE GUIDE

> Comprehensive guide to CLAUDE.md, memory system, and project setup for NexusTrading.

---

## PART 1: CLAUDE.md — Best Practices

### 1.1 File Locations

| Scope | Location | Purpose |
|-------|----------|---------|
| **Global** | `~/.claude/CLAUDE.md` | Personal preferences (all projects) |
| **Project** | `./.claude/CLAUDE.md` | Team-shared instructions |
| **Project** | `./CLAUDE.md` | Alternative location |
| **Rules** | `./.claude/rules/*.md` | Path-specific rules |

### 1.2 Template: NexusTrading CLAUDE.md

```markdown
# NexusTrading — Crypto Quant Platform

## Project Overview
Real-time crypto trading platform with Polymarket integration, arbitrage detection, and portfolio management.

## Tech Stack
- Backend: FastAPI, Python 3.11, PostgreSQL, TimescaleDB
- Frontend: React 18, TypeScript, Tailwind
- APIs: Binance, Polymarket, CoinGecko
- Database: PostgreSQL 15, TimescaleDB (time-series)
- Cache: Redis

## Architecture
```
src/
├── api/              # REST endpoints (/routes, /schemas)
├── models/           # SQLAlchemy models
├── services/         # Business logic
├── strategies/       # Trading strategies
├── indicators/       # Technical indicators (TA-Lib)
├── arbitrage/       # Cross-exchange arbitrage
└── utils/           # Helpers (logging, rates)
```

## Commands
- Dev: `docker-compose up`
- Test: `pytest -v --cov=src`
- Lint: `ruff check . && mypy src`
- Migrate: `alembic upgrade head`

## Coding Standards
- **Python**: PEP 8, type hints required, pydantic for validation
- **TypeScript**: strict mode, no `any`
- **Async**: Use `async/await` for I/O, sync for CPU-bound
- **Testing**: pytest, 80% coverage minimum

## API Conventions
- RESTful, JSON responses
- Error: `{"error": "message", "code": "ERR_CODE"}`
- Auth required except `/health`, `/ready`

## Trading Rules
- Never execute real trades without human approval
- All positions require stop-loss
- Max 10% portfolio per trade
- Log all signals with timestamp

## Common Patterns
- Rate limiting: Token bucket
- Retries: Exponential backoff (3 attempts)
- Circuit breaker for external APIs

## Testing
- Mock external APIs with pytest-mock
- Fixtures in `tests/fixtures/`
- Naming: `test_<module>_<function>`

## Don't
- ❌ Use print() — use logging
- ❌ Commit secrets — use .env
- ❌ Skip type hints
- ❌ Trade without stop-loss
```

### 1.3 Path-Specific Rules

Create `.claude/rules/` folder:

```
.claude/
├── CLAUDE.md
└── rules/
    ├── testing.md
    ├── security.md
    ├── api.md
    └── frontend/
        └── components.md
```

#### Example: testing.md
```yaml
---
paths:
  - "tests/**/*.py"
  - "**/*_test.py"
---

# Testing Rules
1. Use pytest with fixtures
2. Mock external APIs
3. Test naming: `test_<func>_<expected>`
4. Include happy path + 2 edge cases

## Don't
- Test private functions directly
- Use real API keys
- Skip coverage warnings
```

### 1.4 Import External Files

```markdown
# In CLAUDE.md
See @README.md for overview
See @docs/api.md for API docs
See @.env.example for config
```

---

## PART 2: Multi-Tier Memory System

### 2.1 Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    MEMORY HIERARCHY                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ ~/.claude/CLAUDE.md                                 │   │
│  │ GLOBAL — Personal preferences (~15 lines)          │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                │
│                           ▼                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ .claude/CLAUDE.md                                   │   │
│  │ PROJECT — Team conventions (~50-80 lines)           │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                │
│                           ▼                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ .claude/rules/                                      │   │
│  │ MODULE — Path-specific rules (loaded on demand)     │   │
│  └─────────────────────────────────────────────────────┘   │
│                           │                                │
│                           ▼                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ ~/.claude/projects/<project>/memory/               │   │
│  │ AUTO MEMORY — Claude learns automatically          │   │
│  │ First 200 lines of MEMORY.md loaded each session    │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 2.2 Folder Structure

```
~/.claude/                           # Global (personal)
├── CLAUDE.md                        # Personal prefs (~15 lines)
├── rules/
│   ├── preferences.md
│   └── workflows.md
└── projects/
    └── NexusTrading/
        └── memory/
            ├── MEMORY.md            # Auto memory index
            ├── debugging.md
            ├── api-patterns.md
            └── learnings.md

NexusTrading/                        # Project root
├── .claude/
│   ├── CLAUDE.md                   # Project (~80 lines)
│   ├── CLAUDE.local.md             # Local overrides (gitignored)
│   └── rules/
│       ├── testing.md
│       ├── security.md
│       └── backend/
│           └── api.md
├── src/
├── tests/
└── ...
```

### 2.3 Auto Memory Files

| File | Purpose |
|------|---------|
| `MEMORY.md` | Index — first 200 lines loaded at session start |
| `debugging.md` | Debug patterns and solutions |
| `api-patterns.md` | API design decisions |
| `learnings.md` | Key insights from sessions |
| `bugs-found.md` | Bug patterns and fixes |

### 2.4 Setup Commands

```bash
# Create project memory directory
mkdir -p ~/.claude/projects/NexusTrading/memory

# Create CLAUDE.md in project
mkdir -p .claude/rules

# Create rules
touch .claude/rules/testing.md
touch .claude/rules/security.md
touch .claude/rules/api.md

# Verify
ls -la .claude/
```

---

## PART 3: Best Prompts for Coding

### 3.1 Code Review
```
@./src/ Review for:
1. Security vulnerabilities (SQL injection, XSS)
2. Performance issues (N+1, memory leaks)
3. Code smells (SOLID violations)
4. Error handling gaps

Format: File:line | Severity | Issue | Fix
```

### 3.2 Feature Implementation
```
Create REST API for [FEATURE] with:
- CRUD endpoints
- JWT auth with refresh tokens
- Pydantic validation
- Tests with 80% coverage

Output: Complete code, no placeholders
```

### 3.3 Bug Investigation
```
Debug error: [PASTE ERROR]
File: @./src/buggy.py
1. Root cause
2. Why it happened
3. Fix with code
4. Prevention
```

### 3.4 Refactoring
```
Refactor @./src/old.py to:
1. Dependency injection
2. Testable (mockable)
3. SOLID principles
4. Type hints
Output: Complete refactored code
```

---

## PART 4: Essential Claude Code Commands

| Command | Description |
|--------|-------------|
| `/help` | Show commands |
| `/review` | Code review mode |
| `/test` | Generate tests |
| `/doc` | Generate docs |
| `/compact` | Summarize context |
| `/memory` | View/manage memory |
| `/model` | Switch model |
| `/plan` | Enable plan mode |

---

## PART 5: Troubleshooting

### Claude not following CLAUDE.md?
1. Run `/memory` to verify
2. Make instructions more specific
3. Check for conflicts

### CLAUDE.md too large?
- Move to `@import` files
- Split into `.claude/rules/`

### Memory not persisting?
- Auto memory location: `~/.claude/projects/<project>/memory/`
- First 200 lines of MEMORY.md loaded at start

---

## Resources

- [CLAUDE.md Docs](https://code.claude.com/docs/en/memory)
- [CLAUDE.md Best Practices](https://uxplanet.org/claude-md-best-practices)
- [Skills Guide](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)

---

*Generated for NexusTrading — 2026*
