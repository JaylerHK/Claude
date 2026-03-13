# AI & Vibe Coding Daily Briefing — DEEP ACTIONABLE EDITION

> Real commands, copy-paste prompts, code examples, outputs, and configs.

---

## I. Claude Code — COMPLETE COMMAND REFERENCE

### 1. All Slash Commands

```bash
/help              # Show all commands + custom commands
/config            # Configure settings interactively
/allowed-tools     # Configure tool permissions
/hooks             # Configure hooks
/mcp               # Manage MCP servers
/agents            # Manage subagents
/vim               # Enable vim-style editing
/terminal-setup    # Install terminal shortcuts
/install-github-app # Set up GitHub Actions
/model             # Switch Claude model
/review            # Code review mode (BUILT-IN)
/test              # Generate tests (BUILT-IN)
/doc               # Generate documentation (BUILT-IN)
/compact           # Summarize context for next session
```

### 2. CLI Commands (Copy-Paste)

```bash
# === BASIC USAGE ===
claude "query"                           # Interactive with prompt
claude -p "query"                        # Print mode, exit after
claude -p --output-format=json "query"  # JSON for automation

# === SESSION MANAGEMENT ===
claude -c                                 # Continue last session
claude -c -p "continue task"             # Continue in print mode
claude -r session-id "task"               # Resume specific session

# === WITH FILES ===
claude "review" @./src/main.py           # Reference single file
claude "review" @./src/                  # Reference directory
claude "compare" @./a.js @./b.js         # Multiple files
claude "review" @./src/**/*.test.ts      # Glob patterns

# === PERMISSIONS & MODELS ===
claude -p --dangerously-skip-permissions "write file"  # Skip prompts
claude -p --model sonnet-4-20250514 "task"              # Specific model
claude -p --max-turns 3 "quick task"                   # Limit turns

# === WITH SHELL OUTPUT ===
claude "explain this error" !npm test    # Run command, include output
```

### 3. Real Prompt Templates (Copy-Paste)

**🔴 Prompt 1: Full Code Review**
```
@./src/

Perform a comprehensive code review:

1. SECURITY
   - SQL injection, XSS, CSRF vulnerabilities
   - Hardcoded secrets, insecure randomness
   - Path traversal, command injection

2. PERFORMANCE
   - N+1 queries, missing indexes
   - Memory leaks, unnecessary allocations
   - Sync blocking in async code

3. CODE QUALITY
   - SOLID violations
   - Code smells
   - Missing error handling

4. TESTING
   - Test coverage gaps
   - Edge cases not covered

For each issue, provide:
- File and line number
- Severity: CRITICAL/HIGH/MEDIUM/LOW
- Explanation
- Recommended fix
```

**🔴 Prompt 2: Feature Implementation**
```
Create a REST API for [FEATURE] with:

REQUIREMENTS:
- Endpoints: CRUD operations
- Auth: JWT with refresh tokens
- Validation: pydantic models
- Error handling: custom exceptions
- Logging: structured JSON logs
- Tests: pytest, 80% coverage

OUTPUT:
- All files with complete code
- No placeholders or TODOs
- Working unit tests

Tech stack: FastAPI, SQLAlchemy, PostgreSQL
```

**🔴 Prompt 3: Bug Investigation**
```
Debug this error:

ERROR:
[PASTE ERROR MESSAGE]

FILE:
@./src/buggy-file.py

TASK:
1. Identify root cause
2. Explain why it's happening
3. Provide fix with code
4. Suggest prevention
```

**🔴 Prompt 4: Refactoring**
```
Refactor this code @./src/old-code.py to:

1. Use dependency injection
2. Make it testable (mockable)
3. Follow SOLID principles
4. Add type hints
5. Improve naming

Keep functionality identical. Output complete refactored code.
```

**🔴 Prompt 5: Test Generation**
```
@./src/utils.js

Generate comprehensive unit tests using vitest:

1. Test all exported functions
2. Include edge cases
3. Mock external dependencies
4. Use describe/it format
5. Aim for 90% coverage

Output: Complete test file, no placeholders.
```

### 4. Custom Slash Commands (Create Your Own)

```bash
# Create command file
mkdir -p .claude/commands
cat > .claude/commands/review-security.md << 'EOF'
---
allowed-tools: Read,GlobTool,GrepTool,Bash
description: Security audit for code
---
Perform security audit on the provided code:
1. Check for SQL injection
2. Check for XSS vulnerabilities  
3. Check for hardcoded secrets
4. Check for weak crypto
Provide findings with severity and fix.
EOF

# Now use it:
/review-security
```

### 5. CLAUDE.md Template (Production Ready)

```markdown
# PROJECT CONTEXT

## Identity
- Name: [Project Name]
- Type: [Web App / API / Library]
- Core functionality: [What it does]

## Tech Stack
- Frontend: [React 18, TypeScript]
- Backend: [FastAPI, Python 3.11]
- Database: [PostgreSQL 15]
- Cache: [Redis]
- Queue: [Celery]

## Code Standards
- Python: PEP 8, type hints required, pydantic for validation
- TypeScript: strict mode, no 'any'
- Commits: conventional commits (feat, fix, docs)

## Architecture
```
/src
  /api          # HTTP handlers
  /models       # DB models (SQLAlchemy)
  /schemas      # Pydantic models
  /services     # Business logic
  /core         # Config, security
```

## Commands
- Dev: docker-compose up
- Test: pytest -v
- Lint: ruff check . && mypy src
- Migrate: alembic upgrade head

## Common Patterns
- Error handling: custom exception classes
- Auth: JWT with refresh tokens
- Logging: structlog JSON

## DO NOT
- Use print() - use logging
- Commit secrets to git
- Skip type hints
- Write async without reason
```

### 6. Tools Available in Claude Code

| Tool | Use |
|------|-----|
| `Read` | View file contents |
| `Write` | Create/overwrite files |
| `Edit` | Modify specific sections |
| `Bash` | Run shell commands |
| `GlobTool` | Find files by pattern |
| `GrepTool` | Search in files |
| `dispatch_agent` | Spawn research agent |

---

## II. MiniMax M2.5 — COMPLETE API REFERENCE

### 1. Basic API Call (Python)

```python
from anthropic import Anthropic

client = Anthropic(
    api_key="your-minimax-key",
    base_url="https://api.minimax.io/anthropic"
)

response = client.messages.create(
    model="MiniMax-M2.5",
    max_tokens=4096,
    messages=[{
        "role": "user", 
        "content": "Write a Python function to calculate Fibonacci numbers"
    }]
)

print(response.content[0].text)
```

**Sample Output:**
```python
def fibonacci(n: int) -> int:
    """Calculate nth Fibonacci number using iteration."""
    if n < 0:
        raise ValueError("Input must be non-negative")
    if n <= 1:
        return n
    
    a, b = 0, 1
    for _ in range(n - 1):
        a, b = b, a + b
    return b

# Example usage:
print(fibonacci(10))  # Output: 55
```

### 2. Function Calling (Tool Use)

```python
from anthropic import Anthropic
import json

client = Anthropic(
    api_key="your-key",
    base_url="https://api.minimax.io/anthropic"
)

# Define tools
tools = [
    {
        "name": "get_weather",
        "description": "Get current weather for a city",
        "input_schema": {
            "type": "object",
            "properties": {
                "city": {"type": "string", "description": "City name"},
                "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
            },
            "required": ["city"]
        }
    },
    {
        "name": "calculate",
        "description": "Perform calculations",
        "input_schema": {
            "type": "object",
            "properties": {
                "expression": {"type": "string"}
            },
            "required": ["expression"]
        }
    }
]

# First call - model decides to use tool
response = client.messages.create(
    model="MiniMax-M2.5",
    messages=[{"role": "user", "content": "What's 15 * 23?"}],
    tools=tools
)

# Check if tool was called
if response.stop_reason == "tool_use":
    tool_call = response.content[0]
    print(f"Using tool: {tool_call.name}")
    print(f"Input: {tool_call.input}")
    
    # Execute tool (example)
    result = {"result": 15 * 23}
    
    # Continue conversation with tool result
    response = client.messages.create(
        model="MiniMax-M2.5",
        messages=[
            {"role": "user", "content": "What's 15 * 23?"},
            response.content,
            {
                "role": "tool",
                "tool_use_id": tool_call.id,
                "content": json.dumps(result)
            }
        ],
        tools=tools
    )
    
    print(response.content[0].text)
    # Output: 15 multiplied by 23 equals 345.
```

### 3. Interleaved Thinking (Advanced)

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-key",
    base_url="https://api.minimax.io/v1"
)

response = client.chat.completions.create(
    model="MiniMax-M2.5",
    messages=[{
        "role": "user",
        "content": "Design a REST API for a todo app"
    }],
    extra_body={
        "reasoning_split": True  # Enables interleaved thinking
    }
)

# Response includes reasoning_details field
message = response.choices[0].message

# Reasoning (how model thinks)
if hasattr(message, 'reasoning_details'):
    print("Reasoning:", message.reasoning_details)

# Final response
print("Response:", message.content)
```

### 4. LiteLLM Integration

```python
import litellm

# Single unified interface for all models
response = litellm.completion(
    model="MiniMax/M2.5",
    messages=[{"role": "user", "content": "Write a function"}],
    api_key="your-key"
)

print(response.choices[0].message.content)
```

### 5. Environment Variables

```bash
# Set API key
export MINIMAX_API_KEY="your-key"

# In China
export MINIMAX_API_BASE="https://api.minimaxi.com/v1"

# International  
export MINIMAX_API_BASE="https://api.minimax.io/v1"
```

---

## III. OpenClaw (MaxClaw) — COMPLETE COMMANDS

### 1. Spawning Subagents

```python
# === ONE-SHOT TASK ===
sessions_spawn(
    task="Write a hello world Python script",
    runtime="subagent"  # or "acp" for Claude Code
)

# === PERSISTENT SESSION ===
sessions_spawn(
    task="Build a trading bot",
    runtime="subagent",
    thread=True,
    mode="session"
)

# === WITH SPECIFIC MODEL ===
sessions_spawn(
    task="Code review",
    runtime="subagent",
    model="claude-sonnet-4-20250514"
)

# === RESUME PREVIOUS ===
sessions_spawn(
    task="Continue implementation",
    runtime="subagent",
    resumeSessionId="agent:main:subagent:abc123"
)
```

### 2. ACP Agents (Claude Code on Your Mac)

```bash
# === INSTALL ACPX ===
openclaw plugins install acpx

# === CONFIGURE PERMISSIONS ===
openclaw config set plugins.entries.acpx.config.permissionMode approve-all
openclaw config set plugins.entries.acpx.config.nonInteractivePermissions fail

# === SPAWN CLAUDE CODE ===
/acp spawn claude --mode oneshot
/acp spawn claude --mode persistent --thread auto

# === MANAGE SESSIONS ===
/acp status                    # Show running sessions
/acp sessions                  # List recent sessions
/acp cancel <session-id>       # Cancel running task
/acp close                     # Close current session
```

### 3. Configuration JSON

```json
{
  "acp": {
    "enabled": true,
    "backend": "acpx",
    "defaultAgent": "claude",
    "allowedAgents": ["claude", "codex", "pi", "opencode"],
    "maxConcurrentSessions": 8,
    "runtime": {
      "ttlMinutes": 120
    }
  },
  "plugins": {
    "entries": {
      "acpx": {
        "enabled": true,
        "config": {
          "permissionMode": "approve-all",
          "nonInteractivePermissions": "fail",
          "command": "claude",
          "expectedVersion": "any"
        }
      }
    }
  }
}
```

### 4. Sessions Send (Cross-Session Messaging)

```python
# Send message to another session
sessions_send(
    sessionKey="agent:main:subagent:abc123",
    message="Please analyze the trade results and suggest improvements"
)

# With announcement to chat
sessions_send(
    sessionKey="agent:main:subagent:abc123", 
    message="Update on trading bot status",
    announcementMode="step"  # Announces each step
)
```

### 5. Subagent Management

```bash
/subagents list                # Show all subagents
/subagents kill <id>          # Stop subagent
/subagents steer <id> "task"  # Send instructions
```

---

## IV. Qwen — API & CONFIGURATION

### 1. LiteLLM Quick Start

```python
import litellm

# Qwen3.5-Plus
response = litellm.completion(
    model="qwen/qwen-plus",
    messages=[{"role": "user", "content": "Write a Python decorator"}],
    api_key="your-alibaba-key",
    base_url="https://dashscope-intl.aliyuncs.com/compatible-mode/v1"
)

print(response.choices[0].message.content)
```

### 2. Claude Code + Qwen

```bash
# Configure in ~/.claude/settings.json
{
  "model": "qwen-plus",
  "api_key": "your-key",
  "api_base": "https://dashscope-intl.aliyuncs.com/compatible-mode/v1"
}

# Then use normally
claude -p "Write a FastAPI app"
```

### 3. Qwen Code CLI (Agentic)

```bash
# Install
npm install -g @qwen/qwen-code

# Run agentic task
qwen-code "Build a todo app with React"

# With specific model
qwen-code --model qwen-coder-32b "Create REST API"

# Interactive mode
qwen-code -i
```

---

## V. Resources & Links

| Resource | URL |
|----------|-----|
| **Claude Code Docs** | https://docs.anthropic.com/en/docs/claude-code |
| **Claude Code GitHub** | https://github.com/anthropics/claude-code |
| **CLAUDE.md Guide** | https://uxplanet.org/claude-md-best-practices |
| **Claude Prompts Collection** | https://github.com/langgptai/awesome-claude-prompts |
| **MiniMax Docs** | https://platform.minimax.io/docs |
| **M2.5 Tool Calling** | https://github.com/MiniMax-AI/MiniMax-M2.5 |
| **OpenClaw Docs** | https://docs.openclaw.ai |
| **ACP Agents** | https://docs.openclaw.ai/tools/acp-agents |
| **ClawHub Skills** | https://clawhub.ai/skills |

---

## VI. Today's Action Items

```bash
# 1. Test Claude Code
claude -p --dangerously-skip-permissions "Write hello to /tmp/test.txt"

# 2. Create CLAUDE.md
cat > CLAUDE.md << 'EOF'
# My Project
- Tech: React, FastAPI
- Run: docker-compose up
EOF

# 3. Install skills
npx clawhub install polyclaw

# 4. Configure MiniMax in LiteLLM
pip install litellm
export MINIMAX_API_KEY="your-key"
```

---

*_Powered by MAX CLAW_*
