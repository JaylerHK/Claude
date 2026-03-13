# AI & Vibe Coding Daily Briefing — ACTIONABLE EDITION

> Comprehensive commands, prompts, configs, and best practices for coding agents.

---

## I. Claude Code — Commands & Prompts

### 1. Essential Slash Commands

| Command | Description | Example |
|---------|-------------|---------|
| `/help` | Show all commands | `/help` |
| `/review` | Code review mode | `/review` |
| `/test` | Generate tests | `/test` |
| `/doc` | Generate docs | `/doc` |
| `/compact` | Summarize context | `/compact` |
| `/config` | Configure settings | `/config` |
| `/model` | Switch model | `/model sonnet` |
| `/mcp` | Manage MCP servers | `/mcp add github` |

### 2. CLI Commands (Non-Interactive)

```bash
# Basic query (print mode, exits after)
claude -p "What does this code do?" @./src/main.py

# With JSON output (for automation)
claude -p --output-format=json "Summarize this file" @./utils.js

# Skip permission prompts (for automated scripts)
claude -p --dangerously-skip-permissions "Write hello to /tmp/test.txt"

# Continue previous session
claude -c "Continue working on the feature"

# Resume specific session
claude -r session-id "Finish the implementation"

# Limit turns (for one-shot tasks)
claude -p --max-turns 3 "Fix this bug" @./bug.py

# Custom model
claude -p --model sonnet-4-20250514 "Write a function"
```

### 3. Best Practice Prompts

**Prompt 1: Code Review**
```
@./src/ Review this code for:
1. Security vulnerabilities
2. Performance issues
3. Code smells
4. Missing error handling
Provide a numbered list of issues with severity (HIGH/MEDIUM/LOW)
```

**Prompt 2: Feature Implementation**
```
Implement a REST API endpoint for user authentication with:
- JWT tokens
- Password hashing (bcrypt)
- Refresh token rotation
- Return working code with tests
```

**Prompt 3: Bug Fix**
```
Debug this error: "TypeError: Cannot read property 'id' of undefined"
@./src/user-service.js
Provide the fix and explain the root cause
```

**Prompt 4: Refactoring**
```
Refactor this class to:
- Use dependency injection
- Make it more testable
- Follow SOLID principles
@./src/services/EmailService.js
```

### 4. File References (@)

```bash
# Single file
claude -p "Review this" @./src/Button.tsx

# Directory (recursive)
claude -p "Add error handling" @./src/api/

# Multiple files
claude -p "Compare implementations" @./src/old.js @./src/new.js

# Glob patterns
claude -p "Review all tests" @./src/**/*.test.ts
```

### 5. CLAUDE.md Template

Create `CLAUDE.md` in project root:

```markdown
# Project Context

## Tech Stack
- Frontend: React 18, TypeScript, Tailwind
- Backend: FastAPI, Python 3.11, PostgreSQL
- Testing: pytest, Vitest

## Coding Standards
- Python: PEP 8, type hints required
- TypeScript: strict mode, no any
- Commits: conventional commits

## Key Commands
- Run tests: pytest && npm test
- Start dev: docker-compose up
- Lint: ruff check . && npm run lint

## Architecture
- /src/api - REST endpoints
- /src/models - Database models
- /src/services - Business logic

## Common Tasks
- Add API route: create in /src/api/
- Add model: use SQLAlchemy base
- Run migrations: alembic upgrade head
```

### 6. Configuration Example

```json
{
  "model": "claude-sonnet-4-20250514",
  "maxTokens": 4096,
  "permissions": {
    "allow": ["Read", "Write", "Bash(git *)"],
    "deny": ["Read(./.env)", "Write(./prod.config)"]
  },
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write(*.py)",
        "hooks": [{"type": "command", "command": "ruff format $file"}]
      }
    ]
  }
}
```

### 7. Resources

- **Docs**: https://docs.anthropic.com/en/docs/claude-code
- **GitHub**: https://github.com/anthropics/claude-code
- **Best Practices**: https://www.eesel.ai/blog/claude-code-best-practices

---

## II. Claude Code Skills

### Installing Skills

```bash
# Via ClawHub
npx clawhub install polyclaw

# Via Claude Code CLI
claude mcp add server-name

# Create custom skill
mkdir -p .claude/skills/my-skill
# Create SKILL.md in that directory
```

### Recommended Skills for Your Goals

| Skill | Command | Use Case |
|-------|---------|----------|
| **PolyClaw** | `npx clawhub install polyclaw` | Polymarket trading |
| **Composio** | `npx clawhub install composio` | 190+ app integrations |
| **Browser Automation** | Built-in | Web scraping, testing |
| **Memory** | Built-in | Context persistence |

### Skill Structure (SKILL.md)

```yaml
---
name: trade-analyzer
description: Analyze trading strategies and suggest improvements
---

# Trade Analyzer Skill

When user asks to analyze trading performance:
1. Load trade history from database
2. Calculate metrics: win rate, P&L, Sharpe ratio
3. Identify patterns in winning/losing trades
4. Suggest optimizations
5. Output as markdown report

Example: "Analyze my Polymarket trades" → detailed performance report
```

### Resources

- **ClawHub**: https://clawhub.ai/skills
- **Skill Docs**: https://docs.anthropic.com/en/docs/claude-code/extended-capabilities/skills

---

## III. MiniMax M2.5 — API & Config

### API Endpoints

| Region | Anthropic SDK | OpenAI SDK |
|--------|--------------|------------|
| **International** | `https://api.minimax.io/anthropic` | `https://api.minimax.io/v1` |
| **China** | `https://api.minimaxi.com/anthropic` | `https://api.minimaxi.com/v1` |

### Python Example (Anthropic SDK)

```python
from anthropic import Anthropic

client = Anthropic(
    api_key="your-minimax-key",
    base_url="https://api.minimax.io/anthropic"
)

response = client.messages.create(
    model="MiniMax-M2.5",
    max_tokens=4096,
    messages=[{"role": "user", "content": "Write a Python function to calculate moving average"}]
)

print(response.content[0].text)
```

### Python Example (OpenAI SDK)

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-minimax-key",
    base_url="https://api.minimax.io/v1"
)

response = client.chat.completions.create(
    model="MiniMax-M2.5",
    messages=[{"role": "user", "content": "Write a REST API"}],
    extra_body={"reasoning_split": True}  # Enable interleaved thinking
)

print(response.choices[0].message.content)
```

### Function Calling Example

```python
from anthropic import Anthropic

client = Anthropic(
    api_key="your-key",
    base_url="https://api.minimax.io/anthropic"
)

# Define tools
tools = [
    {
        "name": "get_stock_price",
        "description": "Get current stock price",
        "input_schema": {
            "type": "object",
            "properties": {
                "symbol": {"type": "string", "description": "Stock ticker"}
            },
            "required": ["symbol"]
        }
    }
]

response = client.messages.create(
    model="MiniMax-M2.5",
    messages=[{"role": "user", "content": "What's the price of AAPL?"}],
    tools=tools
)

# Check for tool use
if response.stop_reason == "tool_use":
    tool_use = response.content[0]
    print(f"Use tool: {tool_use.name}")
    # Execute tool, then append result and call again
```

### Best Practices

1. **Enable Interleaved Thinking**: Use `reasoning_split: True` for agentic tasks
2. **Preserve Full Response**: Keep `response.content` in message history
3. **Be Specific**: Clear instructions = better output
4. **Use Tools**: M2.5 excels at function calling

### Resources

- **Docs**: https://platform.minimax.io/docs/guides/text-m2-function-call
- **Pricing**: https://platform.minimax.io/docs/pricing/overview
- **Coding Plan**: $1/hour — https://platform.minimax.io/subscribe/coding-plan

---

## IV. Qwen — API & Config

### Model Options

| Model | Best For | API |
|-------|----------|-----|
| **Qwen3.5-Plus** | General tasks | Alibaba Cloud |
| **Qwen3.5-Coder-32B** | Code generation | Local/API |
| **Qwen3-Coder-Next** | Production coding | API |

### LiteLLM Example

```python
import litellm

# Using LiteLLM (unified interface)
response = litellm.completion(
    model="qwen/qwen-plus",
    messages=[{"role": "user", "content": "Write a Python decorator"}],
    api_key="your-alibaba-key",
    base_url="https://dashscope-intl.aliyuncs.com/compatible-mode/v1"
)

print(response.choices[0].message.content)
```

### Claude Code + Qwen

```bash
# In Claude Code, configure:
{
  "model": "qwen-plus",
  "api_key": "your-key",
  "api_base": "https://dashscope-intl.aliyuncs.com/compatible-mode/v1"
}

# Then use normally
claude -p "Write a FastAPI app"
```

### Qwen Code (Agentic CLI)

```bash
# Install Qwen Code
npm install -g @qwen/qwen-code

# Run agentic task
qwen-code "Build a todo app with React"

# With specific model
qwen-code --model qwen-coder-32b "Create a REST API"
```

### Resources

- **Qwen3.5 Docs**: https://qwen.readthedocs.io/
- **Qwen Code**: https://github.com/Qwen/Qwen3-Coder
- **Alibaba Cloud**: https://www.alibabacloud.com/product/model-studio

---

## V. OpenClaw (MaxClaw) — Commands

### Spawning Subagents

```bash
# One-shot task (run mode)
sessions_spawn task="Write a Python function" runtime="subagent"

# Persistent session (session mode)
sessions_spawn task="Build the API" runtime="subagent" thread=true mode="session"

# Spawn with specific model
sessions_spawn task="Code review" runtime="subagent" model="claude-sonnet-4"

# Resume previous session
sessions_spawn task="Continue work" resumeSessionId="session-id"
```

### ACP Agents (Claude Code on Mac)

```bash
# Install ACP plugin
openclaw plugins install acpx

# Spawn Claude Code via ACP
/acp spawn claude --mode oneshot

# Persistent Claude Code session
/acp spawn claude --mode persistent --thread auto

# Check status
/acp status

# List sessions
/acp sessions
```

### OpenClaw Slash Commands

| Command | Description |
|---------|-------------|
| `/subagents list` | Show active subagents |
| `/subagents kill` | Stop subagent |
| `/acp spawn` | Spawn ACP agent |
| `/acp cancel` | Cancel running task |
| `/status` | Show session status |

### Configuration

```json
{
  "acp": {
    "enabled": true,
    "allowedAgents": ["claude", "codex", "pi"],
    "defaultAgent": "claude"
  },
  "plugins": {
    "entries": {
      "acpx": {
        "enabled": true,
        "config": {
          "permissionMode": "approve-all"
        }
      }
    }
  }
}
```

### Resources

- **Docs**: https://docs.openclaw.ai
- **GitHub**: https://github.com/openclaw/openclaw
- **Commands**: https://docs.openclaw.ai/tools/subagents

---

## VI. GLM-5 — API & Config

### ⚠️ Important: Not Local

GLM-5 requires 180GB+ RAM — **use via API only**.

### API Configuration

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-zai-key",
    base_url="https://open.bigmodel.cn/api/paas/v4"
)

response = client.chat.completions.create(
    model="glm-5",
    messages=[{"role": "user", "content": "Build a trading bot"}]
)
```

### Claude Code + GLM-5

```bash
# Configure in Claude Code settings
{
  "model": "glm-5",
  "api_key": "your-zai-key",
  "api_base": "https://open.bigmodel.cn/api/paas/v4"
}
```

### Best For

- Complex agentic workflows
- Multi-step reasoning
- Production-grade code generation

### Resources

- **Z.ai**: https://z.ai/
- **API Docs**: https://docs.z.ai/

---

## VII. Action Items

### Today

- [ ] Test CLI commands on your Mac:
  ```bash
  claude -p --dangerously-skip-permissions "Write hello to /tmp/test.txt"
  ```
- [ ] Create `CLAUDE.md` in Nexus Trading project
- [ ] Install Claude Code skills: `npx clawhub install polyclaw`

### This Week

- [ ] Configure MiniMax M2.5 in LiteLLM
- [ ] Set up SSH for MaxClaw → Claude Code integration
- [ ] Test Qwen via LiteLLM

---

## Links Summary

| Resource | URL |
|----------|-----|
| Claude Code Docs | https://docs.anthropic.com/en/docs/claude-code |
| MiniMax Docs | https://platform.minimax.io/docs |
| Qwen Docs | https://qwen.readthedocs.io/ |
| OpenClaw Docs | https://docs.openclaw.ai |
| ClawHub Skills | https://clawhub.ai/skills |
| CLAUDE.md Guide | https://uxplanet.org/claude-md-best-practices |

---

*_Powered by MAX CLAW_*
