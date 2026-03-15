# Global Preferences

> Personal settings for all my projects.

---

## How I Work

- **Always use plan mode** first (`/plan`), then execute
- Run tests after every change
- Ask before git commits over 50 lines
- Explain decisions, don't just make them
- Ask if requirements unclear

---

## Models I Use

| When | Model | How |
|------|-------|-----|
| Quick tasks | Qwen (LM Studio) | Local, fast |
| Complex tasks | GLM-5 | Via LiteLLM |
| Research | Claude Sonnet | Anthropic |

**Setup:**
- LiteLLM: `http://localhost:4000`
- LM Studio: `http://localhost:1234`

---

## Coding Standards

### Python
- PEP 8
- Type hints required
- Use `pydantic` for validation
- Async/await for I/O

### TypeScript
- Strict mode
- No `any`
- Use interfaces over types

---

## What I Won't Do

- ❌ Modify tests without explicit request
- ❌ Touch production without confirmation
- ❌ Use `print()` — use logging
- ❌ Leave secrets in code
- ❌ Skip type hints

---

## Preferred Commands

```bash
# Python
pytest -v --cov=src
ruff check . && ruff format .
mypy src

# JavaScript/TypeScript
npm test
npm run lint

# General
docker-compose up
```

---

## Communication Style

- Direct, not blunt
- High confidence — state positions clearly
- No fluff or filler
- Dry humor when it lands
- Technical precision when matters

---

## Project Memory

When working in a project:
1. Check project's `CLAUDE.md` first
2. Check `.claude/rules/` for path-specific rules
3. Check auto memory: `~/.claude/projects/<project>/memory/`

---

## Skills & Tools

Available via `/skill`:
- Trading analysis
- Code review
- Security review

---

## Reference

- Settings: `~/.claude/settings.json`
- Rules: `~/.claude/rules/`
- Auto memory: `~/.claude/projects/`

---

*Last updated: 2026-03-15*
