# Skills Setup Guide

> Complete guide to skills for OpenClaw (MaxClaw) and Claude Code.

---

## PART 1: OpenClaw Skills (MaxClaw)

### What Are OpenClaw Skills?

Skills are **capabilities** that extend OpenClaw's functionality. Located in `~/.openclaw/workspace/skills/`.

### Essential Skills for MaxClaw

| Skill | Purpose | Priority |
|-------|---------|----------|
| **proactive-agent** | Automated scheduled workflows | 🔴 Tier 1 |
| **healthcheck** | VPS security monitoring | 🔴 Tier 1 |
| **skill-creator** | Build custom skills | 🟡 Tier 2 |
| **agent-reach** | Multi-platform search | 🟡 Tier 2 |

### Install Commands

```bash
# Install from ClawHub
npx clawhub@latest install proactive-agent
npx clawhub@latest install healthcheck
npx clawhub@latest install skill-creator

# Or via OpenClaw
openclaw plugins install proactive-agent
openclaw plugins install healthcheck
```

### Create Custom OpenClaw Skill

```bash
# Create directory
mkdir -p ~/.openclaw/workspace/skills/my-skill

# Create SKILL.md
cat > ~/.openclaw/workspace/skills/my-skill/SKILL.md << 'EOF'
---
name: my-skill
description: Custom skill description
---

# My Custom Skill

When user asks to [task], follow these steps:

1. Step one
2. Step two
3. Step three

## Output Format
Provide results in markdown.
EOF
```

### Skill Structure

```
~/.openclaw/workspace/skills/<skill-name>/
├── SKILL.md           # Required: instructions
├── resources/         # Optional: reference files
└── scripts/          # Optional: executable scripts
```

---

## PART 2: Claude Code Skills (Your Mac)

### What Are Claude Code Skills?

Skills are **reusable workflows** you invoke with `/skill-name`. Located in `.claude/skills/`.

### Create Custom Skill

```bash
# Create directory
mkdir -p .claude/skills/trading-bot

# Create SKILL.md
cat > .claude/skills/trading-bot/SKILL.md << 'EOF'
---
name: Trading Bot
description: Create or modify trading strategies, backtest, and analyze performance
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
    
    def generate_signal(self, data):
        return {
            "action": "BUY/SELL/HOLD",
            "confidence": 0.8,
            "stop_loss": self.stop_loss
        }
```

## Risk Rules
- Max 10% portfolio per trade
- Always set stop-loss
- No leverage > 3x

## Output Format
1. Signal (BUY/SELL/HOLD)
2. Confidence (0-1)
3. Entry price
4. Stop-loss
5. Take-profit
6. Position size
```

### Skill Structure

```
.claude/skills/<skill-name>/
├── SKILL.md           # Required: name, description, instructions
├── resources/         # Optional: reference files
└── scripts/           # Optional: executable code
```

### SKILL.md Frontmatter

| Field | Required | Description |
|-------|----------|-------------|
| `name` | ✅ | Human-friendly name (max 64 chars) |
| `description` | ✅ | When to use this skill (max 200 chars) |
| `dependencies` | ❌ | Required packages |

### Use Skills in Claude Code

```
# Invoke skill
/skill trading-bot

# Or describe naturally
"I need to create a new trading strategy"
```

---

## PART 3: Recommended Skills for Your Goals

### For Trading/Crypto

| Skill | Location | Purpose |
|-------|----------|---------|
| Trading Bot | Claude Code | Strategy creation, backtesting |
| Code Review | Claude Code | Security & quality review |
| Healthcheck | OpenClaw | VPS security monitoring |
| Proactive Agent | OpenClaw | Scheduled reports |

### Install Commands

```bash
# Claude Code skills (on your Mac)
mkdir -p .claude/skills/code-review
# Create SKILL.md manually

# OpenClaw skills (MaxClaw)
npx clawhub@latest install healthcheck
```

---

## PART 4: Custom Skills for NexusTrading

### Trading Strategy Skill

Create `.claude/skills/trading-strategy/SKILL.md`:

```yaml
---
name: Trading Strategy
description: Create, analyze, or backtest crypto trading strategies
dependencies:
  - pandas>=2.0.0
  - numpy>=1.24.0
  - backtesting>=0.3.0
---

# Trading Strategy Skill

## When to Use
- Create new trading strategy
- Modify existing strategy
- Backtest strategy performance
- Analyze indicators

## Strategy Components
1. Entry signal logic
2. Exit signal logic
3. Position sizing
4. Risk management (stop-loss, take-profit)

## Backtest Requirements
- Minimum 1 year data
- Include fees and slippage
- Test multiple market conditions

## Output
- Strategy code
- Backtest results
- Risk metrics
```

### Code Review Skill

Create `.claude/skills/code-review/SKILL.md`:

```yaml
---
name: Code Review
description: Comprehensive security and quality code review
---

# Code Review Skill

## Checklist
1. Security (SQL injection, XSS, secrets)
2. Performance (N+1, memory)
3. Error handling
4. Test coverage

## Output Format
| File | Line | Severity | Issue | Fix |
|------|------|----------|-------|-----|
```

---

## PART 5: Quick Setup Commands

### On Your Mac (Claude Code)

```bash
# Create skills directory
mkdir -p .claude/skills/trading-bot
mkdir -p .claude/skills/code-review
mkdir -p .claude/skills/security-review

# Create each SKILL.md (see above templates)
```

### On VPS (OpenClaw/MaxClaw)

```bash
# Install essential skills
npx clawhub@latest install healthcheck
npx clawhub@latest install proactive-agent

# Or check available
clawhub skills list
```

---

## References

| Resource | URL |
|----------|-----|
| OpenClaw Skills Docs | https://docs.openclaw.ai/tools/creating-skills |
| ClawHub | https://clawhub.ai/skills |
| Claude Code Skills | https://code.claude.com/docs/en/skills |
| Awesome Claude Skills | https://github.com/travisvn/awesome-claude-skills |

---

*Created: 2026-03-15*
