# OpenClaw Integration Guide for LongCat-2.0

## Quick Start

### Configure Provider

```bash
# Add LongCat as a custom provider
openclaw providers add longcat \
    --base-url https://api.longcat.ai/v1 \
    --api-key your-api-key \
    --model meituan/longcat-2.0
```

### Use in Your Agent

```python
from openclaw import Agent

agent = Agent(
    provider="longcat",
    model="meituan/longcat-2.0",
    reasoning=True,  # thinking mode
    max_tokens=16384
)

response = agent.run("Write a Python function")
```

---

## Environment Variables

```bash
export LONGCAT_BASE_URL=https://api.longcat.ai/v1
export LONGCAT_API_KEY=your-api-key
```

---

## Thinking Mode

OpenClaw supports LongCat's reasoning mode:

```python
response = agent.run(
    "Complex task",
    reasoning="high",  # or "low", "off"
    stream=True
)
```

---

## Tool Calling

LongCat-2.0 uses **dict-args format**. OpenClaw's adapter handles this automatically.

---

## Self-Hosted

For self-hosted deployments, point OpenClaw at your SGLang endpoint:

```bash
openclaw providers add longcat-local \
    --base-url http://localhost:8000/v1 \
    --api-key dummy \
    --model meituan/longcat-2.0
```
