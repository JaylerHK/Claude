# AI & Vibe Coding Daily Briefing

## I. Vibe Coding: Best Practices & Recommendations

**Definition**: Vibe coding uses natural language to guide AI tools to generate code, focusing on describing what you want rather than writing syntax.

### Best Practices (2026)

1. **Start with Intent, Not Instructions**
   - Explain WHY, not just WHAT
   - Describe the user experience first, implementation second

2. **Use Architectural Constraints Upfront**
   - Set strong constraints before generating code
   - Specify: tech stack, performance requirements, security needs

3. **Iterative Refinement**
   - Generate → Review → Iterate
   - Don't expect perfect code in first pass

4. **Structure Your Prompts**
   ```
   Problem → Architecture → Implementation → Review
   ```

5. **Trust but Verify**
   - AI generates 10x more code than humans by 2026
   - Always review before deploying

### Top Tools (2026)

| Tool | Best For |
|------|----------|
| **Claude Code** | Agentic coding, complex projects |
| **Cursor** | IDE integration, fast iteration |
| **Replit** | Quick prototyping |
| **VS Code + Codex** | Full agent mode |

---

## II. Claude Skills 2.0: Boost Your Coding

### What Are Claude Skills?

Modular capabilities that extend Claude's functionality. Each skill packages instructions, metadata, and optional resources.

### Must-Have Skills (2026)

1. **Composio Skills** — 190+ integrations (GitHub, Slack, etc.)
2. **Browser Automation** — agent-browser for web scraping/automation
3. **Memory & Context** — persistent context across sessions
4. **Frontend Design** — UI/UX generation
5. **Remotion** — video generation from code
6. **Multi-Agent Orchestration** — coordinate multiple agents

### Recommended Skills for Your Goals

| Category | Skill | Use Case |
|----------|-------|----------|
| **Trading** | API integrations | Connect to Binance, Polymarket |
| **Scraping** | Browser automation | Collect market data |
| **Multi-Agent** | Agent coordination | Nexus Trading orchestration |
| **Financial** | Data processing | PnL calculations |

### Installation

```bash
# Via ClawHub
npx clawhub@latest install [skill-name]

# Via Claude Code
claude install [skill-name]
```

---

## III. Claude Commands & CLI

### Essential Slash Commands

| Command | Function |
|---------|----------|
| `/help` | Show all commands |
| `/review` | Code review mode |
| `/test` | Generate tests |
| `/doc` | Generate documentation |
| `/compact` | Summarize context for next session |
| `/context` | See current context usage |

### Claude.md Best Practices

Create a `CLAUDE.md` in project root:

```markdown
# Project Context
- Tech stack: React, FastAPI, Python
- Coding standards: TypeScript, PEP8
- Testing: pytest, 80% coverage

# Key Files
- src/api/main.py - Entry point
- src/models/ - Data models

# Common Tasks
- Run tests: pytest
- Start server: uvicorn src.api.main:app
```

### CLI Flags for Non-Interactive Mode

```bash
# Print mode (no TUI)
claude -p --dangerously-skip-permissions "Your prompt"

# With JSON output
claude --print --output-format=json --dangerously-skip-permissions "prompt"

# With custom model
claude -p --model sonnet-4-20250514 "prompt"
```

---

## IV. MiniMax M2.5: Best Practices

### Why M2.5?

- **80.2% on SWE-Bench** — matches Claude Opus
- **$1/hour** — 1/20th the cost of Claude
- **Agentic native** — built for tool use

### Best Practices

1. **Be Clear and Specific**
   - State expected output format
   - Break complex tasks into steps

2. **Use Interleaved Thinking**
   - M2.5 reasons between each tool call
   - Good for multi-step agentic workflows

3. **Function Calling**
   - Native tool use support
   - Use for API integrations, data fetching

### API Configuration

```python
# OpenAI-compatible
from openai import OpenAI

client = OpenAI(
    api_key="your-key",
    base_url="https://api.minimax.chat/v1"
)

response = client.chat.completions.create(
    model="MiniMax-M2.5",
    messages=[{"role": "user", "content": "Your prompt"}],
    tools=[...]  # Function definitions
)
```

### Claude Code Integration

```bash
# In Claude Code, configure in claude_settings:
{
  "model": "MiniMax-M2.5",
  "api_key": "your-minimax-key"
}
```

---

## V. Qwen3.5 & Next Gen: Best Practices

### Qwen Ecosystem

| Model | Best For | Parameters |
|-------|----------|------------|
| **Qwen3.5-Plus** | General agentic tasks | ~30B |
| **Qwen3.5-Coder** | Code generation | 3B-32B |
| **Qwen3-Coder-Next** | Production coding | 480B A35B |

### Qwen3.5 Strengths

- **MoE architecture** — 35B-A3B = 3B active = fast
- **Excellent function calling** — perfect for agents
- **Long context** — up to 128K tokens

### Best Practices

1. **Use Coder Models for Code Tasks**
   ```
   Qwen2.5-Coder-32B matches GPT-4o on coding benchmarks
   ```

2. **Enable Agent Mode**
   ```bash
   qwen-agent --model qwen-coder-32b "Build a REST API"
   ```

3. **Function Calling**
   - Define tools in JSON schema
   - Use `function_call` parameter

### API via LiteLLM

```python
import litellm

response = litellm.completion(
    model="qwen/qwen-plus",
    messages=[{"role": "user", "content": "Write a Python function"}],
    api_key="your-key"
)
```

---

## VI. GLM-5 & Successors: Best Practices

### GLM-5 Overview

- **744B parameters** (MoE, ~40B active)
- **Frontier-level performance** — tops coding benchmarks
- **Agentic engineering** — designed for complex systems

### ⚠️ Local Running?

| Quantization | RAM Needed | Feasible? |
|--------------|------------|-----------|
| 4-bit | ~400GB | ❌ No Mac |
| 8-bit | ~805GB | ❌ No Mac |
| Cloud API | N/A | ✅ Recommended |

**Use GLM-5 via API** — not locally feasible.

### Best Practices

1. **Use for Complex Agentic Tasks**
   - Multi-step reasoning
   - System engineering
   - Production-grade code

2. **API Configuration**
   ```python
   # Via Z.ai API
   response = client.chat.completions.create(
       model="glm-5",
       messages=[...],
       api_key="your-zai-key"
   )
   ```

3. **Claude Code + GLM-5**
   ```bash
   # Configure in Claude Code
   claude --model glm-5 "Your prompt"
   ```

### When to Use GLM-5

| Task | Recommended Model |
|------|-------------------|
| Quick scripts | Qwen3.5-9B |
| Production code | GLM-5 (API) |
| Local offline | GLM-4.7 Flash |
| Agentic workflows | GLM-5 / M2.5 |

---

## VII. Action Items for Jayler

### Today

- [ ] Set up Claude Code SSH access on Mac
- [ ] Configure acpx for MaxClaw → Claude Code integration
- [ ] Install relevant Claude Skills via ClawHub

### This Week

- [ ] Test Claude Code with GLM-5 via --model flag
- [ ] Explore MiniMax M2.5 for agentic tasks
- [ ] Review Nexus Trading code with Claude Code

### Resources

- CLAUDE.md: Create in each project
- Skills: `npx clawhub install [name]`
- Models: Use API for GLM-5, local for Qwen/GLM-4.7

---

*Briefing generated by MaxClaw — 2026*
