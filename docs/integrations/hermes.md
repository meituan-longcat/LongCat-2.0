# Hermes Integration Guide for LongCat-2.0

**Model:** `meituan/longcat-2.0`  
**Provider:** OpenAI-compatible API  
**Context:** 1M tokens native  
**License:** MIT

---

## Quick Start

### 1. Get API Access

LongCat-2.0 is available via:
- **LongCat API** (invite-only, apply at [longcat.ai](https://longcat.ai))
- **Self-hosted** via SGLang (GPU) or SGLang-FluentLLM (NPU)
- **OpenRouter** (community listings)

### 2. Hermes Configuration

Add to your Hermes config (`~/.hermes/config.yaml` or project `AGENTS.md`):

```yaml
# ~/.hermes/config.yaml
models:
  longcat-2.0:
    provider: openai
    model: meituan/longcat-2.0
    base_url: ${LONGCAT_BASE_URL:-https://api.longcat.ai/v1}
    api_key: ${LONGCAT_API_KEY}
    context_window: 1000000
    max_tokens: 16384
    supports_tools: true
    supports_vision: false
    reasoning: true  # enables thinking mode
```

### 3. Environment Variables

```bash
export LONGCAT_API_KEY="your-api-key"
export LONGCAT_BASE_URL="https://api.longcat.ai/v1"  # or your self-hosted URL
```

### 4. Usage

```bash
# One-shot chat
hermes -m longcat-2.0 chat "Write a Python function to parse JSON"

# Interactive session
hermes -m longcat-2.0

# With specific toolset
hermes -m longcat-2.0 -t web,terminal "Research and summarize this topic"
```

---

## Thinking Mode

LongCat-2.0 supports **interleaved thinking** (chain-of-thought reasoning):

```yaml
# Enable thinking (default: true for complex tasks)
reasoning: high

# Disable for simple, fast responses
reasoning: off
```

When thinking is enabled:
- Reasoning tokens are returned in `reasoning_content` field
- Tool calls are planned before execution
- Multi-step tasks show intermediate reasoning

---

## Tool Calling

LongCat-2.0 uses **dict-args format** (not string-args):

```python
# CORRECT (LongCat format)
{
  "tool_calls": [{
    "function": {
      "name": "func_add",
      "arguments": {"x1": 1, "x2": 2}  # dict, not string
    }
  }]
}

# WRONG (standard OpenAI format — will fail)
{
  "tool_calls": [{
    "function": {
      "name": "func_add",
      "arguments": '{"x1": 1, "x2": 2}'  # string, not dict
    }
  }]
}
```

Hermes handles this automatically when configured as `provider: openai`.

---

## Self-Hosted Deployment

### SGLang (GPU)

```bash
# Install SGLang
pip install sglang

# Serve LongCat-2.0
python -m sglang.launch_server \
    --model meituan-longcat/LongCat-2.0 \
    --tp 8 \
    --context-length 1000000 \
    --enable-thinking \
    --tool-call-parser longcat \
    --reasoning-parser longcat
```

### SGLang-FluentLLM (NPU)

See [meituan-longcat/SGLang-FluentLLM](https://github.com/meituan-longcat/SGLang-FluentLLM).

---

## Known Limitations

| Limitation | Workaround |
|------------|------------|
| No vision/multimodal | Use text-only workflows |
| API is invite-only (as of 2026-08) | Self-host via SGLang |
| Dict-args tool format | Hermes handles automatically |
| 194 safetensor shards (~3TB BF16) | Use FP8 or quantized variants |

---

## Benchmarks (Hermes Agent Workspace)

| Benchmark | Score |
|-----------|-------|
| SWE-Bench Pro | 59.5 |
| Terminal-Bench 2.1 | 70.8 |
| SWE-Bench Multilingual | 77.3 |
| BrowseComp | 79.9 |
| RWSearch | 78.8 |

---

## Troubleshooting

### "Tool arguments must be a dict"
Ensure your Hermes config uses `provider: openai` (not `anthropic`). LongCat uses dict-args format.

### "Context too long"
LongCat-2.0 supports 1M context, but your API provider may have lower limits. Check with:
```bash
hermes -m longcat-2.0 model info
```

### Thinking mode not working
Verify `reasoning: true` in config. Some API endpoints may not support thinking mode.

---

## See Also

- [LongCat-2.0 GitHub](https://github.com/meituan-longcat/LongCat-2.0)
- [LongCat Blog](https://longcat.chat/blog/longcat-2.0)
- [SGLang Deployment](https://docs.sglang.io/cookbook/autoregressive/Meituan/LongCat-2.0)
- [Hermes Agent Docs](https://hermes-agent.nousresearch.com/docs)
