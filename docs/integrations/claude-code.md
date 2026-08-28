# Claude Code Integration Guide for LongCat-2.0

## Quick Start

### Option A: Environment Variables

```bash
# In your shell profile or project .env
export ANTHROPIC_BASE_URL=https://api.longcat.ai/v1
export ANTHROPIC_API_KEY=your-api-key
export ANTHROPIC_MODEL=meituan/longcat-2.0
```

### Option B: Claude Code Settings

```json
// ~/.claude/settings.json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.longcat.ai/v1",
    "ANTHROPIC_API_KEY": "your-api-key",
    "ANTHROPIC_MODEL": "meituan/longcat-2.0"
  },
  "model": "meituan/longcat-2.0"
}
```

### Option C: Command Line

```bash
claude --model meituan/longcat-2.0 --api-key your-api-key --base-url https://api.longcat.ai/v1
```

---

## Thinking Mode

Enable thinking for complex tasks:

```json
// In conversation
/reasoning high

// Or in settings
{
  "reasoning": "high"
}
```

---

## Known Issues

| Issue | Status | Workaround |
|-------|--------|------------|
| Tool args as dict not string | Open | Claude Code adapter handles this |
| No vision support | Known | Use text-only mode |
| Rate limits (API) | Expected | Self-host for production |

---

## Self-Hosted Option

For production use, self-host with SGLang:

```bash
python -m sglang.launch_server \
    --model meituan-longcat/LongCat-2.0 \
    --tp 8 \
    --port 8000
```

Then point Claude Code at `http://localhost:8000/v1`.
