# Claude Code Multi-Layer Memory System — COMPLETE GUIDE

> Comprehensive setup for long-term + short-term memory, folder structure, and reference files.

---

## Overview: The Memory Hierarchy

Claude Code has **3 complementary memory systems**:

```
┌─────────────────────────────────────────────────────────────┐
│                    MEMORY HIERARCHY                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐  │
│  │ ~/.claude/CLAUDE.md                                  │  │
│  │ GLOBAL - Personal preferences, all projects           │  │
│  │ ~15 lines max                                        │  │
│  └─────────────────────────────────────────────────────┘  │
│                           │                                │
│                           ▼                                │
│  ┌─────────────────────────────────────────────────────┐  │
│  │ .claude/CLAUDE.md                                    │  │
│  │ PROJECT - Team conventions, shared with git          │  │
│  │ ~50-80 lines max                                      │  │
│  └─────────────────────────────────────────────────────┘  │
│                           │                                │
│                           ▼                                │
│  ┌─────────────────────────────────────────────────────┐  │
│  │ .claude/rules/                                       │  │
│  │ MODULE - Path-specific rules                         │  │
│  │ Loaded on demand                                     │  │
│  └─────────────────────────────────────────────────────┘  │
│                           │                                │
│                           ▼                                │
│  ┌─────────────────────────────────────────────────────┐  │
│  │ .claude/local.md                                     │  │
│  │ LOCAL - Personal overrides, gitignored               │  │
│  │ Never shared                                         │  │
│  └─────────────────────────────────────────────────────┘  │
│                           │                                │
│                           ▼                                │
│  ┌─────────────────────────────────────────────────────┐  │
│  │ ~/.claude/projects/<project>/memory/                 │  │
│  │ AUTO MEMORY - Claude learns automatically           │  │
│  │ First 200 lines of MEMORY.md loaded each session    │  │
│  └─────────────────────────────────────────────────────┘  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 1: Folder Structure

### Complete Directory Layout

```
~/
├── .claude/                              # Global config
│   ├── CLAUDE.md                         # Global preferences (~15 lines)
│   ├── rules/                            # Global rules
│   │   ├── preferences.md                # Personal coding preferences
│   │   └── workflows.md                  # Preferred workflows
│   └── projects/                        # Auto memory by project
│       └── <project-name>/
│           ├── MEMORY.md                 # Index (first 200 lines loaded)
│           ├── debugging.md              # Debugging patterns learned
│           ├── api-conventions.md        # API decisions
│           └── ...
│
projects/
└── my-project/
    ├── .claude/
    │   ├── CLAUDE.md                     # Project instructions (~50-80 lines)
    │   ├── CLAUDE.local.md              # Local overrides (gitignored)
    │   └── rules/                       # Path-specific rules
    │       ├── code-style.md             # Coding standards
    │       ├── testing.md                # Testing conventions
    │       ├── security.md               # Security requirements
    │       ├── frontend/                 # Frontend-specific
    │       │   └── components.md
    │       └── backend/                  # Backend-specific
    │           └── api.md
    │
    ├── src/
    └── ...
```

---

## Part 2: File Templates

### 2.1 Global CLAUDE.md (~/.claude/CLAUDE.md)

```markdown
# Global Preferences

## How I Work
- Always use plan mode first, then execute
- Run tests after every change
- Ask before git commits over 50 lines

## Coding Standards
- TypeScript: strict mode, no `any`
- Python: PEP 8, type hints required
- Prefer explicit over implicit

## Communication
- Explain decisions, don't just make them
- Ask if uncertain about requirements

## What I Won't Do
- Won't modify test files without explicit request
- Won't touch production without confirmation
- Won't use print() - use logging instead
```

### 2.2 Project CLAUDE.md (./.claude/CLAUDE.md)

```markdown
# Project: Nexus Trading Platform

## Overview
Crypto quant trading platform with Polymarket integration. Real-time trading signals and portfolio management.

## Tech Stack
- Backend: FastAPI, Python 3.11, PostgreSQL
- Frontend: React 18, TypeScript, Tailwind
- Database: PostgreSQL 15, TimescaleDB
- Cache: Redis
- Trading: Polymarket API, Binance API

## Architecture
```
src/
├── api/              # REST endpoints
├── models/           # SQLAlchemy models
├── services/         # Business logic
├── strategies/        # Trading strategies
├── indicators/       # Technical indicators
└── utils/            # Helpers
```

## Commands
- Dev: `docker-compose up`
- Test: `pytest -v`
- Lint: `ruff check . && mypy src`
- Migrate: `alembic upgrade head`

## Coding Standards
- Async for I/O, sync for CPU
- All trading functions require type hints
- Risk parameters must be validated
- Never log actual prices in production

## API Conventions
- RESTful, JSON responses
- Error format: `{"error": "message", "code": "ERR_CODE"}`
- All endpoints require auth except /health

## Testing
- pytest, 80% coverage minimum
- Mock external APIs
- Include edge cases

## Common Patterns
- Token bucket rate limiting
- Exponential backoff on API failures
- Circuit breaker for external services
```

### 2.3 Path-Specific Rules (./.claude/rules/)

#### testing.md
```yaml
---
paths:
  - "tests/**/*.py"
  - "**/*_test.py"
---

# Testing Conventions

1. Use pytest with fixtures
2. Mock external API calls with pytest-mock
3. Test naming: `test_<function>_<expected_behavior>`
4. Include docstrings explaining what's being tested
5. Happy path + 2 edge cases minimum

## Don't
- Don't test private functions directly
- Don't use real API keys in tests
- Don't skip test coverage warnings
```

#### security.md
```yaml
---
paths:
  - "src/auth/**"
  - "**/security*"
  - "**/crypto*"
---

# Security Requirements

1. Never log secrets or API keys
2. Use parameterized queries only
3. Validate all inputs
4. CSRF protection required for all forms
5. Rate limit auth endpoints

## Protected Code
- src/auth/ - Requires review before changes
- Migration files - Never auto-generated
```

### 2.4 Local Overrides (./.claude/CLAUDE.local.md)

```markdown
# Local Overrides

## My Setup
- VS Code as editor
- Use pytest-vim for test running
- I prefer verbose logging during dev

## Custom Commands
- /test-current runs tests for current file
- /format runs ruff + prettier

## Notes
- M2 MacBook Pro, 32GB RAM
- Docker Desktop with 4 cores allocated
```

---

## Part 3: Auto Memory System

### How Auto Memory Works

```
~/.claude/projects/<project>/memory/
├── MEMORY.md              # Index - FIRST 200 LINES loaded
├── debugging.md           # Detailed debugging notes
├── api-patterns.md        # API design decisions
├── bugs-found.md          # Bug patterns and fixes
└── learnings.md           # General learnings
```

### Auto Memory Example

Claude automatically writes to these files when you:
- Correct its mistakes
- Provide preferences
- Discover patterns

### Managing Auto Memory

```bash
# View/manage memory
/memory

# Toggle auto memory on/off
/memory → toggle auto memory

# Memory is stored at:
~/.claude/projects/<project-name>/memory/
```

### Best Practice: Move Stable Learnings to CLAUDE.md

```markdown
# In auto memory (learnings.md):
# After multiple sessions, move stable patterns to CLAUDE.md

# IN CLAUDE.md (permanent):
## Bug Patterns
- Always check null for user input from API responses
- Race condition in concurrent order placement
```

---

## Part 4: CLI Commands for Memory

### Essential Commands

```bash
# Start with project analysis (generates CLAUDE.md)
/init

# View loaded memory files
/memory

# Check current context
/context

# Summarize for next session
/compact

# Switch model
/model sonnet
```

### Memory Management

```bash
# Add directory to CLAUDE.md search path
claude --add-dir ../shared-config

# Load additional directories' CLAUDE.md
export CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1

# Disable auto memory (if needed)
export CLAUDE_CODE_DISABLE_AUTO_MEMORY=1
```

---

## Part 5: /compact Command — Session Summary

### When to Use

- Context getting full (~100k tokens)
- Need to preserve important decisions
- Starting new phase of work

### How It Works

```
Before /compact:
┌────────────────────────────────────────┐
│ Session History (~100k tokens)          │
│ - Early decisions                      │
│ - Context that's no longer relevant    │
└────────────────────────────────────────┘
        ↓ /compact
After /compact:
┌────────────────────────────────────────┐
│ Compacted Summary (~2k tokens)          │
│ - Key decisions                        │
│ - Current state                        │
│ - TODO items                           │
└────────────────────────────────────────┘
        ↓
Full history archived (not in context, but preserved)
```

### Example /compact Output

```markdown
## Session Summary

### Completed
- Set up Polymarket API integration
- Implemented basic arbitrage detection
- Created database schema

### Current State
- Working on signal generation
- Need to add risk management

### Next Steps
1. Add stop-loss logic
2. Implement position sizing
3. Test with paper trading

### Decisions Made
- Use TimescaleDB for time-series
- Redis for caching signals
- 5-minute polling interval
```

---

## Part 6: Advanced Patterns

### 6.1 Import External Files

```markdown
# In CLAUDE.md
See @README.md for project overview
See @docs/api.md for API documentation

# Git workflow
@docs/git-workflow.md

# Team conventions
@~/.claude/team-preferences.md
```

### 6.2 Module-Specific CLAUDE.md

```
.claude/CLAUDE.md              # Root - always loaded
src/auth/CLAUDE.md            # Auth module
src/trading/CLAUDE.md         # Trading module
src/api/CLAUDE.md             # API module
```

Claude loads these **on demand** when you work in those directories.

### 6.3 Hooks for Automatic Context

```json
// .claude/settings.json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.py)",
        "hooks": [
          {
            "type": "command",
            "command": "ruff format $file && ruff check $file"
          }
        ]
      }
    ]
  }
}
```

### 6.4 MCP Integration for External Memory

```json
// .mcp.json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@mem0/mcp-server"]
    }
  }
}
```

---

## Part 7: Setup Scripts

### Quick Setup Script

```bash
#!/bin/bash
# setup-claude-memory.sh

# Create global config
mkdir -p ~/.claude/rules

# Create project structure
mkdir -p .claude/rules

# Copy templates
cp templates/global-claude.md ~/.claude/CLAUDE.md
cp templates/project-claude.md .claude/CLAUDE.md
cp templates/testing-rules.md .claude/rules/testing.md

# Add gitignore
echo ".claude/CLAUDE.local.md" >> .gitignore
echo ".claude/projects/" >> .gitignore

echo "✅ Claude memory system set up!"
```

---

## Part 8: Decision Guide — What Goes Where

| Rule Type | Where | Reasoning |
|-----------|-------|-----------|
| "Run tests after changes" | Global | Want everywhere |
| "Use shadcn/ui" | Project | Team convention |
| "I use VS Code" | Local | Only me |
| "No any in TypeScript" | Project | Team standard |
| "Ask before commit" | Global | Personal preference |
| "API keys in .env" | Project | Domain knowledge |
| "Prices in src/config" | Project | Project structure |
| "Debug patterns" | Auto memory | Learned over time |

---

## Part 9: Best Practices Summary

### Do
✅ Keep global CLAUDE.md under 15 lines
✅ Keep project CLAUDE.md under 80 lines
✅ Use `.claude/rules/` for detailed rules
✅ Use path-specific rules to reduce noise
✅ Move stable auto-memory learnings to CLAUDE.md
✅ Review CLAUDE.md monthly for outdated content
✅ Use `/compact` before context overflows

### Don't
❌ Don't exceed 200 lines per CLAUDE.md
❌ Don't duplicate rules across files
❌ Don't include style rules (use formatters)
❌ Don't put secrets in CLAUDE.md
❌ Don't ignore auto memory - review it regularly

---

## Part 10: Resources

| Resource | URL |
|----------|-----|
| **Official Docs** | https://code.claude.com/docs/en/memory |
| **CLAUDE.md Templates** | https://github.com/abhishekray07/claude-md-templates |
| **Best Practices** | https://uxplanet.org/claude-md-best-practices |
| **Anthropic Guide** | https://www.anthropic.com/engineering/claude-code-best-practices |
| **HumanLayer Guide** | https://www.humanlayer.dev/blog/writing-a-good-claude-md |

---

*_Powered by MAX CLAW_*
