# LongCat-2.0

<div align="center">
  <img src="figures/longcat_logo.svg" width="45%" alt="LongCat-2.0" />
</div>
<hr>

<div align="center" style="line-height: 1;">
  <!-- <a href="https://longcat.ai/" target="_blank" style="margin: 2px;">
    <img alt="Chat" src="https://img.shields.io/badge/🤖%20Chat-LongCat--Flash--Chat-ADFF2F?color=29E154&logoColor=white"  fill-opacity="1" style="display: inline-block; vertical-align: middle;"/>
  </a> -->
  <a href="https://huggingface.co/meituan-longcat" target="_blank" style="margin: 2px;">
    <img alt="Hugging Face" src="https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-LongCat-ffc107?color=ffc107&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
  <a href="https://www.modelscope.cn/models/meituan-longcat/LongCat-2.0" target="_blank" style="margin: 2px;">
    <img alt="ModelScope" src="https://img.shields.io/badge/%F0%9F%A4%96%20ModelScope-LongCat-624AFF?logo=modelscope&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
</div>

<div align="center" style="line-height: 1;">
  <a href="https://github.com/meituan-longcat/LongCat-2.0/blob/main/figures/wechat_official_accounts.png" target="_blank" style="margin: 2px;">
    <img alt="Wechat" src="https://img.shields.io/badge/WeChat-LongCat-brightgreen?logo=wechat&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
  <a href="https://discord.com/invite/jmuv5TQzKU" target="_blank" style="margin: 2px;">
    <img alt="Discord" src="https://img.shields.io/badge/Discord-Join%20Chat-5865F2?logo=discord&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
  <a href="https://x.com/Meituan_LongCat" target="_blank" style="margin: 2px;">
    <img alt="Twitter Follow" src="https://img.shields.io/badge/Twitter-LongCat-white?logo=x&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
</div>

<div align="center" style="line-height: 1;">
  <a href="LICENSE" style="margin: 2px;">
    <img alt="License" src="https://img.shields.io/badge/License-MIT-f5de53?&color=f5de53" style="display: inline-block; vertical-align: middle;"/>
  </a>
</div>

<p align="center">
  <a href="https://longcat.chat/blog/longcat-2.0"><b>Tech Blog</b>&nbsp;📄</a>
</p>

## Model Introduction
We introduce LongCat-2.0, a large-scale MoE language model with **1.6 trillion total parameters** and ~48 billion activated per token — a substantial step up from previous LongCat models, accompanied by several architectural improvements.

Both the full training run and the large-scale deployment are built entirely on **AI ASIC superpods**. Pretraining spans millions of accelerator-days across more than 35 trillion tokens, with no rollbacks or irrecoverable loss spikes — demonstrating that we have the capability to conduct frontier-scale training on alternative hardware platforms.

To strengthen the model on long-horizon tasks, we introduce LongCat Sparse Attention and train LongCat-2.0 on hundreds of billions of tokens of **1M-context** data. Together with dedicated post-training, this gives LongCat-2.0 strong performance on coding and agentic tasks.

LongCat-2.0 is deeply integrated with mainstream harnesses such as Claude Code, OpenClaw, and Hermes, delivering strong performance across code understanding, repository-level edits, automated task execution, and agentic workflows — providing developers with a more stable and efficient collaborative experience.

<div align="center">
  <img src="figures/longcat-pro-benchmark-charts-2026-06-29.svg" width="100%" alt="LongCat-2.0 Benchmark Charts" />
</div>

### Key Features

#### 🌟 LongCat Sparse Attention
To address the output discontinuity and quadratic scoring bottleneck of the Lightning Indexer in [DSA](https://huggingface.co/deepseek-ai/DeepSeek-V3.2-Exp), we introduce LongCat Sparse Attention (LSA). LSA features three orthogonal improvements:

- Streaming-aware Indexing (SI) reshapes the token selection budget to combine hardware-aligned contiguous access with dynamic random selection. This turns fragmented memory access into predictable sequential reads, achieving coalesced HBM access and high effective bandwidth.
- Cross-Layer Indexing (CLI) leverages the empirical stability of attention saliency across adjacent layers to amortize indexing cost: a single indexing pass serves several consecutive layers at inference time, made possible by cross-layer distillation during training.
- Hierarchical Indexing (HI) uses a coarse-to-fine, two-stage scoring scheme — first a coarse recall via block-level approximate scoring, then fine-grained token selection within the recalled candidates — shrinking the candidate space the indexer must process per query.

All strategies seamlessly extend to the 3-step Multi-Token Prediction module for speculative decoding. For CLI, the target model shares an index every 2 layers, while all 3 MTP draft steps share a single pass.

#### 🌟 N-gram Embedding
LongCat-2.0 inherits N-gram Embedding from [LongCat-Flash-Lite](https://huggingface.co/meituan-longcat/LongCat-Flash-Lite), improving parameter utilization efficiency by expanding parameters in sparse dimensions orthogonal to MoE. 135B N-gram Embedding parameters are included in the model, which adheres to the following scaling principles:

- The sparsity of MoE has crossed the sweet spot. 
- The proportion of N-gram Embedding is constrained within an optimal range. 

These two principles guarantee the robust superiority of N-gram Embedding compared to equivalent-sized pure MoE models.

**For more details please refer to our [blog](https://longcat.chat/blog/longcat-2.0/).**

## Evaluation Results
We evaluate LongCat-2.0 against leading proprietary models across agentic, coding, search, productivity and foundational capabilities. Unless noted with `*`, all scores are measured in-house under a unified harness.

<table>
<thead>
<tr>
<th align="center"><div align="center">Benchmark</div></th>
<th align="center"><div align="center">LongCat-2.0</div></th>
<th align="center"><div align="center">Gemini 3.1 Pro</div></th>
<th align="center"><div align="center">GPT-5.5</div></th>
<th align="center"><div align="center">Claude Opus 4.6</div></th>
<th align="center"><div align="center">Claude Opus 4.7</div></th>
<th align="center"><div align="center">Claude Opus 4.8</div></th>
</tr>
</thead>
<tbody>
<tr><td colspan="7"><div align="center"><strong>Code Agent</strong></div></td></tr>
<tr>
<td align="center"><div align="center">Terminal-Bench 2.1</div></td>
<td align="center"><div align="center">70.8</div></td>
<td align="center"><div align="center">70.7*</div></td>
<td align="center"><div align="center">73.8*</div></td>
<td align="center"><div align="center">-</div></td>
<td align="center"><div align="center">71.7*</div></td>
<td align="center"><div align="center">78.9*</div></td>
</tr>
<tr>
<td align="center"><div align="center">SWE-bench Pro</div></td>
<td align="center"><div align="center">59.5</div></td>
<td align="center"><div align="center">54.2*</div></td>
<td align="center"><div align="center">58.6*</div></td>
<td align="center"><div align="center">57.3*</div></td>
<td align="center"><div align="center">64.3*</div></td>
<td align="center"><div align="center">69.2*</div></td>
</tr>
<tr>
<td align="center"><div align="center">SWE-bench Multilingual</div></td>
<td align="center"><div align="center">77.3</div></td>
<td align="center"><div align="center">76.9*</div></td>
<td align="center"><div align="center">-</div></td>
<td align="center"><div align="center">77.8*</div></td>
<td align="center"><div align="center">80.5*</div></td>
<td align="center"><div align="center">84.8*</div></td>
</tr>
<tr><td colspan="7"><div align="center"><strong>General Agent</strong></div></td></tr>
<tr>
<td align="center"><div align="center">FORTE <a href="https://github.com/AGI-Eval-Official/FORTE">↗</a></div></td>
<td align="center"><div align="center">73.2</div></td>
<td align="center"><div align="center">70.3</div></td>
<td align="center"><div align="center">77.8</div></td>
<td align="center"><div align="center">73.2</div></td>
<td align="center"><div align="center">77.6</div></td>
<td align="center"><div align="center">77.2</div></td>
</tr>
<tr>
<td align="center"><div align="center">BrowseComp</div></td>
<td align="center"><div align="center">79.9</div></td>
<td align="center"><div align="center">85.9*</div></td>
<td align="center"><div align="center">84.4*</div></td>
<td align="center"><div align="center">84.0*</div></td>
<td align="center"><div align="center">79.3*</div></td>
<td align="center"><div align="center">84.3*</div></td>
</tr>
<tr>
<td align="center"><div align="center">RWSearch <a href="https://github.com/AGI-Eval-Official/RW-Search">↗</a></div></td>
<td align="center"><div align="center">78.8</div></td>
<td align="center"><div align="center">76.3</div></td>
<td align="center"><div align="center">85.3</div></td>
<td align="center"><div align="center">81.3</div></td>
<td align="center"><div align="center">79.3</div></td>
<td align="center"><div align="center">77.3</div></td>
</tr>
<tr><td colspan="7"><div align="center"><strong>Foundational</strong></div></td></tr>
<tr>
<td align="center"><div align="center">IFEval</div></td>
<td align="center"><div align="center">90.0</div></td>
<td align="center"><div align="center">96.1</div></td>
<td align="center"><div align="center">95.0</div></td>
<td align="center"><div align="center">92.2</div></td>
<td align="center"><div align="center">88.7</div></td>
<td align="center"><div align="center">86.0</div></td>
</tr>
<tr>
<td align="center"><div align="center">Writing Bench</div></td>
<td align="center"><div align="center">83.8</div></td>
<td align="center"><div align="center">83.7</div></td>
<td align="center"><div align="center">84.7</div></td>
<td align="center"><div align="center">-</div></td>
<td align="center"><div align="center">85.3</div></td>
<td align="center"><div align="center">85.2</div></td>
</tr>
<tr>
<td align="center"><div align="center">IMO-AnswerBench</div></td>
<td align="center"><div align="center">81.8</div></td>
<td align="center"><div align="center">90.0</div></td>
<td align="center"><div align="center">79.5</div></td>
<td align="center"><div align="center">75.3*</div></td>
<td align="center"><div align="center">81.8</div></td>
<td align="center"><div align="center">75.3</div></td>
</tr>
<tr>
<td align="center"><div align="center">GPQA-diamond</div></td>
<td align="center"><div align="center">88.9</div></td>
<td align="center"><div align="center">94.3*</div></td>
<td align="center"><div align="center">93.6*</div></td>
<td align="center"><div align="center">91.3*</div></td>
<td align="center"><div align="center">94.2*</div></td>
<td align="center"><div align="center">92.4</div></td>
</tr>
</tbody>
</table>

Notes: `*` — cited from the model's official report; `-` — no comparable public score.

## Chat Website
You can chat with LongCat-2.0 on our official website: [https://longcat.ai/](https://longcat.ai/).

## Deployment

LongCat-2.0 can be deployed on both **GPU** and **NPU** platforms.

### GPU

We have implemented adaptations in SGLang ([PR](https://github.com/sgl-project/sglang/pull/30042)) to support the deployment of LongCat-2.0. Hierarchical indexing is not supported for simplicity.

We recommend deploying with 16x H20 using a combination of Tensor Parallelism and Expert Parallelism.

Compile and update sgl-kernel first.

```shell
cd sgl-kernel
python3 -m uv build --wheel --color=always --no-build-isolation \
        -Ccmake.define.SGL_KERNEL_ENABLE_SM90A=1 \
        -Ccmake.define.CMAKE_POLICY_VERSION_MINIMUM=3.5 \
        -Cbuild-dir=build .
pip3 install dist/sgl_kernel-0.3.21-cp310-abi3-linux_x86_64.whl --force-reinstall
```

Then launch the server.

```py
python -m sglang.launch_server \
  --model meituan-longcat/LongCat-2.0-FP8 \
  --trust-remote-code \
  --host 0.0.0.0 \
  --port 13423 \
  --tp 16 \
  --ep 16 \
  --max-running-requests 64 \
  --mem-fraction-static 0.92 \
  --chunked-prefill-size 2048 \
  --nsa-prefill-backend fa3 \
  --kv-cache-dtype bfloat16 \
  --nnodes 2 \
  --node-rank 0 \
  --dist-init-addr 33.32.48.42:20000 \
  2>&1 | tee sgl.log
```

### NPU

For NPU deployment, please refer to [SGLang-FluentLLM](https://github.com/meituan-longcat/SGLang-FluentLLM/blob/npu/npu_test/ReadMe.md).

## Chat Template

We provide a chat template for LongCat-2.0 in the `tokenizer_config.json` file, which can be used to encode a list of messages into a single string for model input. 

Here is a brief example of how to use the template:

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("meituan-longcat/LongCat-2.0", trust_remote_code=True)

tools = [
    {
        "type": "function",
        "function": {
            "name": "func_add",
            "description": "Calculate the sum of two numbers",
            "parameters": {
                "type": "object",
                "properties": {
                    "x1": {"type": "number", "description": "The first number to add"},
                    "x2": {"type": "number", "description": "The second number to add"},
                },
                "required": ["x1", "x2"],
            },
        },
    },
    {
        "type": "function",
        "function": {
            "name": "func_multiply",
            "description": "Calculate the product of two numbers",
            "parameters": {
                "type": "object",
                "properties": {
                    "x1": {"type": "number", "description": "The first number to multiply"},
                    "x2": {"type": "number", "description": "The second number to multiply"},
                },
                "required": ["x1", "x2"],
            },
        },
    },
]

messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Calculate 1+1"},
    {
        "role": "assistant",
        "reasoning_content": "Calling func_add to calculate 1+1",
        # Note: unlike the standard OpenAI format, we expect `arguments` to be a dict rather than a string.
        "tool_calls": [
            {"type": "function", "function": {"name": "func_add", "arguments": {"x1": 1, "x2": 1}}},
        ],
    },
    {"role": "tool", "name": "func_add", "content": '{"ans": 2}'},
    {"role": "assistant", "reasoning_content": "The result is 2", "content": "2"},
    {"role": "user", "content": "Check your answer, is it correct?"},
]

# thinking mode on
prompt_think = tokenizer.apply_chat_template(
    messages,
    tools=tools,
    tokenize=False,
    enable_thinking=True,
    add_generation_prompt=True
)

# thinking mode on, keeping all reasoning content for better performance
prompt_full = tokenizer.apply_chat_template(
    messages,
    tools=tools,
    tokenize=False,
    enable_thinking=True,
    add_generation_prompt=True,
    save_reasoning_content=True
)

# thinking mode off, for better token efficiency
prompt_no_think = tokenizer.apply_chat_template(
    messages,
    tools=tools,
    tokenize=False,
    enable_thinking=False,
    add_generation_prompt=True
)
```

## License Agreement

The **model weights** are released under the **MIT License**. 

Any contributions to this repository are licensed under the MIT License, unless otherwise stated. This license does not grant any rights to use Meituan trademarks or patents. 

See the [LICENSE](LICENSE) file for the full license text.

## Usage Considerations 
This model has not been specifically designed or comprehensively evaluated for every possible downstream application. 

Developers should take into account the known limitations of large language models, including performance variations across different languages, and carefully assess accuracy, safety, and fairness before deploying the model in sensitive or high-risk scenarios. 
It is the responsibility of developers and downstream users to understand and comply with all applicable laws and regulations relevant to their use case, including but not limited to data protection, privacy, and content safety requirements. 

Nothing in this Model Card should be interpreted as altering or restricting the terms of the MIT License under which the model is released.

## Contact
Please contact us at <a href="mailto:longcat-team@meituan.com">longcat-team@meituan.com</a> or open an issue if you have any questions.

---