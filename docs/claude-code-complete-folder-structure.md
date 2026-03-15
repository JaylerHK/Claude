# Claude Code — Complete Folder Structure & Configuration

> World-class setup guide for NexusTrading. Everything you need to know.

---

## Full Folder Structure

```
NexusTrading/
├── CLAUDE.md                          # ← PROJECT MEMORY (most important)
├── .claude/
│   ├── settings.json                  # Project settings (git)
│   ├── settings.local.json            # Local overrides (gitignored)
│   ├── CLAUDE.md                     # Additional project context
│   ├── CLAUDE.local.md               # Local context (gitignored)
│   ├── rules/                        # Path-scoped modular rules
│   │   ├── testing.md
│   │   ├── security.md
│   │   ├── api.md
│   │   └── backend/
│   │       └── trading.md
│   ├── skills/                       # Reusable workflows
│   │   ├── trading-bot/
│   │   │   ├── SKILL.md
│   │   │   └── resources/
│   │   │       └── patterns.md
│   │   └── code-review/
│   │       ├── SKILL.md
│   │       └── rules.md
│   ├── agents/                       # Specialized sub-agents
│   │   ├── security-review.md
│   │   └── refactor-agent.md
│   ├── commands/                     # Custom slash commands
│   │   ├── analyze-trades.md
│   │   └── generate-signal.md
│   └── .mcp.json                    # External integrations
├── src/
├── tests/
└── ...
```

---

## File-by-File Guide

---

### 1. CLAUDE.md — Project Memory (CRITICAL)

**Location:** `./CLAUDE.md` or `./.claude/CLAUDE.md`

This is the **most important file**. Loaded at every session start.

```markdown
# NexusTrading — Crypto Quant Platform

## Project Overview
Real-time crypto trading with Polymarket integration, arbitrage detection, portfolio management.

## Tech Stack
- Backend: FastAPI, Python 3.11, PostgreSQL, TimescaleDB
- Frontend: React 18, TypeScript, Tailwind
- APIs: Binance, Polymarket, CoinGecko
- Cache: Redis

## Architecture
```
src/
├── api/           # REST endpoints
├── models/        # SQLAlchemy
├── services/      # Business logic
├── strategies/    # Trading strategies
├── arbitrage/    # Cross-exchange arbitrage
└── utils/        # Helpers
```

## Commands
- Dev: `docker-compose up`
- Test: `pytest -v --cov=src`
- Lint: `ruff check . && mypy src`

## Coding Standards
- Python: PEP 8, type hints required
- Async for I/O, sync for CPU
- Testing: 80% coverage minimum

## Trading Rules
- NEVER execute real trades without approval
- Always set stop-loss
- Max 10% portfolio per trade
- Log all signals with timestamps

## Common Patterns
- Rate limiting: Token bucket
- Retries: Exponential backoff (3 attempts)
- Circuit breaker for external APIs
```

---

### 2. settings.json — Permissions & Config

**Location:** `.claude/settings.json`

Controls permissions, model, hooks, MCP.

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Write",
      "Bash(pytest .*)",
      "Bash(ruff .*)",
      "Bash(docker .*)"
    ],
    "deny": [
      "Read(./.env)",
      "Write(./prod*)",
      "Bash(sudo *)",
      "Bash(rm -rf / *)"
    ]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write.*\\.py$",
        "hooks": [
          {
            "type": "command",
            "command": "ruff format $CLAUDE_FILE_PATH && ruff check $CLAUDE_FILE_PATH"
          }
        ]
      }
    ]
  },
  "model": "glm-5",
  "autoMemoryEnabled": true,
  "allowedDirectories": [
    "$HOME/Workspace/NexusTrading"
  ]
}
```

#### Full settings.json Options

| Field | Type | Description |
|-------|------|-------------|
| `permissions.allow` | string[] | Tools that run automatically |
| `permissions.deny` | string[] | Blocked tools |
| `permissions.ask` | string[] | Require confirmation |
| `hooks` | object | Pre/post tool scripts |
| `model` | string | Default model |
| `autoMemoryEnabled` | boolean | Enable auto memory |
| `allowedDirectories` | string[] | Restrict file access |

---

### 3. settings.local.json — Local Overrides

**Location:** `.claude/settings.local.json` (gitignored)

Personal tweaks:

```json
{
  "model": "glm-5",
  "autoMemoryEnabled": true,
  "permissions": {
    "allow": [
      "Bash(git *)"
    ]
  }
}
```

---

### 4. .claude/rules/ — Path-Specific Rules

**Location:** `.claude/rules/*.md`

Modular rules loaded only when working in specific paths.

#### testing.md
```yaml
---
paths:
  - "tests/**/*.py"
  - "**/*_test.py"
---

# Testing Rules
1. Use pytest with fixtures
2. Mock external APIs with pytest-mock
3. Naming: `test_<module>_<function>_<expected>`
4. Include happy path + 2 edge cases
5. Target 80% coverage

## Don't
- Test private functions directly
- Use real API keys
- Skip coverage warnings
```

#### security.md
```yaml
---
paths:
  - "src/auth/**"
  - "**/security*"
  - "**/keys*"
  - "**/credentials*"
---

# Security Rules
1. NEVER log secrets or API keys
2. Use parameterized queries only
3. Validate all inputs
4. CSRF protection required
5. Rate limit auth endpoints

## Changes to these files require review
```

#### api.md
```yaml
---
paths:
  - "src/api/**"
  - "src/routes/**"
---

# API Development Rules
1. RESTful endpoints only
2. Error format: `{"error": "message", "code": "ERR_CODE"}`
3. All endpoints require auth except `/health`
4. Input validation with Pydantic
5. OpenAPI docs required

## Response Format
{
  "success": true,
  "data": {...},
  "meta": {"timestamp": "..."}
}
```

---

### 5. .claude/skills/ — Reusable Workflows

**Location:** `.claude/skills/<skill-name>/SKILL.md`

Skills are reusable workflows you invoke with `/skill-name`.

#### trading-bot/SKILL.md
```yaml
---
name: Trading Bot
description: Create or modify trading strategies, backtest, and analyze performance. Use for any trading-related code.
dependencies:
  - pandas>=2.0.0
  - numpy>=1.24.0
---

# Trading Bot Skill

## Overview
Specialized in building crypto trading strategies with risk management.

## When to Use
- Creating new trading strategies
- Modifying existing indicators
- Backtesting strategies
- Analyzing trade performance
- Implementing risk management

## Strategy Template
```python
class TradingStrategy:
    def __init__(self):
        self.position_size = 0.1  # 10% max
        self.stop_loss = 0.02     # 2% stop-loss
    
    def generate_signal(self, data: pd.DataFrame) -> dict:
        # Your logic here
        return {
            "action": "BUY/SELL/HOLD",
            "confidence": 0.8,
            "stop_loss": self.stop_loss,
            "take_profit": 0.05
        }
```

## Risk Rules
- Max 10% portfolio per trade
- Always set stop-loss
- No leverage > 3x
- Diversify across 5+ assets

## Backtesting
- Use backtesting.py or custom
- Minimum 1 year historical data
- Include fees and slippage

## Output Format
Always include:
1. Signal (BUY/SELL/HOLD)
2. Confidence score (0-1)
3. Entry price
4. Stop-loss
5. Take-profit
6. Position size
```

#### code-review/SKILL.md
```yaml
---
name: Code Review
description: Perform comprehensive code review for security, performance, and quality
---

# Code Review Skill

## Review Checklist
1. **Security**
   - SQL injection, XSS, CSRF
   - Hardcoded secrets
   - Input validation

2. **Performance**
   - N+1 queries
   - Memory leaks
   - Async blocking

3. **Code Quality**
   - SOLID violations
   - Code smells
   - Missing error handling

4. **Testing**
   - Coverage gaps
   - Edge cases

## Output Format
| File | Line | Severity | Issue | Fix |
|------|------|----------|-------|-----|
| src/api.py | 42 | HIGH | SQL injection | Use parameterized query |

## Severity Levels
- CRITICAL: Security vulnerability
- HIGH: Performance/bugs
- MEDIUM: Code quality
- LOW: Style/nitpick
```

---

### 6. .claude/agents/ — Specialized Sub-Agents

**Location:** `.claude/agents/<agent-name>.md`

Define focused sub-agents for specific tasks.

#### security-review.md
```markdown
# Security Review Agent

You are a security expert specializing in code vulnerability assessment.

## Your Task
Review code for security issues and provide detailed findings.

## Focus Areas
1. Authentication & authorization
2. Input validation
3. Data protection
4. API security
5. Dependency vulnerabilities

## Output Format
## Findings

### Critical
- [ ] Issue 1
- [ ] Issue 2

### High
- [ ] Issue 3

## Recommendations
1. Fix in order of severity
2. Include code examples
3. Reference OWASP Top 10
```

#### refactor-agent.md
```markdown
# Refactoring Agent

You specialize in code refactoring to improve quality.

## Focus
- Extract functions
- Apply SOLID principles
- Improve naming
- Add type hints
- Remove duplication

## Rules
- Never change functionality
- Preserve all tests
- Add type hints
- Keep git history clean
```

---

### 7. .claude/commands/ — Custom Slash Commands

**Location:** `.claude/commands/<command-name>.md`

Create custom slash commands.

#### analyze-trades.md
```yaml
---
allowed-tools: Read, GlobTool, GrepTool, Bash
description: Analyze trading performance and generate report
---

# Analyze Trades Command

Run this when user wants trade performance analysis.

## Steps
1. Find all trade logs in `logs/`
2. Calculate metrics:
   - Win rate
   - P&L
   - Sharpe ratio
   - Max drawdown
3. Generate markdown report

## Output
## Trading Performance Report

### Overall
- Total Trades: X
- Win Rate: X%
- Total P&L: $X

### By Strategy
| Strategy | Trades | Win Rate | P&L |
|----------|--------|----------|-----|
| trend_following | 50 | 45% | +$500 |
| arbitrage | 30 | 60% | +$300 |

### Recommendations
1. Stop underperforming strategies
2. Increase allocation to best performers
```

---

### 8. .mcp.json — External Integrations

**Location:** `.claude/.mcp.json`

Connect to external tools and services.

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your-token"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed"]
    },
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/notion-mcp-server"],
      "env": {
        "NOTION_API_KEY": "your-key"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost:5432/db"
      }
    }
  }
}
```

---

## Setup Commands

```bash
# 1. Create folder structure
mkdir -p .claude/rules/backend
mkdir -p .claude/skills/trading-bot/resources
mkdir -p .claude/agents
mkdir -p .claude/commands

# 2. Create settings.json
cat > .claude/settings.json << 'EOF'
{
  "permissions": {
    "allow": ["Read", "Write", "Bash(pytest .*)", "Bash(ruff .*)"],
    "deny": ["Read(./.env)", "Bash(sudo *)"]
  },
  "model": "glm-5",
  "autoMemoryEnabled": true
}
EOF

# 3. Create settings.local.json (gitignored)
cat > .claude/settings.local.json << 'EOF'
{
  "model": "glm-5"
}
EOF

# 4. Add to .gitignore
echo ".claude/settings.local.json" >> .gitignore

# 5. Verify structure
find .claude -type f | sort
```

---

## Auto Memory

Claude automatically saves learnings to:
```
~/.claude/projects/<project>/memory/
├── MEMORY.md          # Index (first 200 lines loaded)
├── debugging.md
├── api-patterns.md
└── learnings.md
```

### Commands
```bash
/memory           # View memory status
/memory on        # Enable auto memory
/memory off       # Disable auto memory
```

---

## Best Practices Summary

| Component | When to Use |
|-----------|-------------|
| **CLAUDE.md** | Project context, rules, patterns |
| **settings.json** | Permissions, model, hooks |
| **rules/** | Path-specific instructions |
| **skills/** | Reusable workflows |
| **agents/** | Specialized sub-agents |
| **commands/** | Custom slash commands |
| **.mcp.json** | External tool integration |

---

## Resources

- [Claude Code Docs](https://docs.claude.com/en/docs/claude-code)
- [Skills Guide](https://code.claude.com/docs/en/skills)
- [Settings Reference](https://docs.claude.com/en/docs/claude-code/settings)
- [MCP Docs](https://modelcontextprotocol.io)

---

*Generated for NexusTrading — 2026*
