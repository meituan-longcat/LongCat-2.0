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
</div>

<div align="center" style="line-height: 1;">
  <a href="https://github.com/meituan-longcat/LongCat-2.0/blob/main/figures/wechat_official_accounts.png" target="_blank" style="margin: 2px;">
    <img alt="Wechat" src="https://img.shields.io/badge/WeChat-LongCat-brightgreen?logo=wechat&logoColor=white" style="display: inline-block; vertical-align: middle;"/>
  </a>
  <!-- <a href="https://discord.gg/EXsG52D8SW">
    <img src="https://img.shields.io/badge/Discord-Join%20Chat-5865F2?logo=discord&logoColor=white" />
  </a> -->
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

Both the full training run and the large-scale deployment are built entirely on **AI ASIC superpods**. Pretraining spans millions of accelerator-hours across more than 35 trillion tokens, with no rollbacks or irrecoverable loss spikes — demonstrating that we have the capability to conduct frontier-scale training on alternative hardware platforms.

To strengthen the model on long-horizon tasks, we introduce LongCat Sparse Attention and train LongCat-2.0 on hundreds of billions of tokens of **1M-context** data. Together with dedicated post-training, this gives LongCat-2.0 strong performance on coding and agentic tasks.

<!-- ### Key Features

#### 🌟 LongCat Sparse Attention
To address the output discontinuity and quadratic scoring bottleneck of the Lightning Indexer in [DSA](https://huggingface.co/deepseek-ai/DeepSeek-V3.2-Exp), we introduce LongCat Sparse Attention (LSA). LSA features three orthogonal, plug-and-play improvements:

- Streaming-aware Indexing (SI) reshapes the token selection budget to combine hardware-aligned contiguous access with dynamic random selection. This turns fragmented memory access into predictable sequential reads, achieving coalesced HBM access and high effective bandwidth.
- Cross-Layer Indexing (CLI) leverages the empirical stability of attention saliency across adjacent layers to amortize indexing cost: a single indexing pass serves several consecutive layers at inference time, made possible by cross-layer distillation during training.
- Hierarchical Indexing (HI) uses a coarse-to-fine, two-stage scoring scheme — first a coarse recall via block-level approximate scoring, then fine-grained token selection within the recalled candidates — shrinking the candidate space the indexer must process per query. In LongCat-2.0, HI is applied in a training-free manner and enabled for selected ultra-long-context tasks.

All strategies seamlessly extend to the 3-step Multi-Token Prediction module for speculative decoding. For CLI, the target model shares an index every 2 layers, while all 3 MTP draft steps share a single pass.

#### 🌟 N-gram Embedding
LongCat-2.0 inherits N-gram Embedding from [LongCat-Flash-Lite](https://huggingface.co/meituan-longcat/LongCat-Flash-Lite), improving parameter utilization efficiency by expanding parameters in sparse dimensions orthogonal to MoE. 135B N-gram Embedding parameters are included in the model, which adheres to the following scaling principles:

- The sparsity of MoE has crossed the sweet spot. 
- The proportion of N-gram Embedding is constrained within an optimal range. 

These two principles guarantee the robust superiority of N-gram Embedding compared to equivalent-sized pure MoE models. For inference, shifting parameters from experts to N-gram Embedding reduces large-batch decoding memory I/O, accelerating generation.

#### 🌟 Scalable Training Infrastructure on Domestic Superpod Accelerators
LongCat-2.0 is pre-trained on over 50K domestic accelerators, introducing significant system-level challenges due to both model and cluster scale. We address these challenges through systematic optimizations, achieving over 35% training throughput improvement while also enhancing reliability compared to a naive implementation.

- Determinism & Reliability: The system ensures training reproducibility through enforced determinism, enhances numerical reliability by optimizing foundational operators, and achieves seamless fault recovery via automated monitoring to fully secure stable production operations.
- Training at Scale: The system effectively overcomes hardware memory bottlenecks and boosts large-scale training efficiency by integrating 6D parallelism with super-node architectures, applying multi-dimensional memory optimizations, and pioneering the large-scale deployment of a customized Muon optimizer.
- Long Context Training: The system tackles large-scale long-context training challenges by implementing optimized in-house operators, scaling context length to 1M via an all-gather-based CP parallelism scheme, and meticulously designing compute-communication overlap to minimize synchronization overhead.

#### 🌟 Inference Optimization on Domestic Superpod Accelerators
Serving a 1.6T-parameter model over a 1M-token context presents a significant challenge, particularly under tight constraints on HBM capacity, HBM I/O bandwidth, and inter-node interconnect bandwidth. We address this challenge through a stack of optimizations at the model, device, and deployment levels.

- Model-Specific Optimization: To optimize Attention and ScMoE, the system mitigates KV-cache overhead and memory pressure through indexer pipelining and KV-cache parallelism, while leveraging explicit per-core control to achieve fully parallel execution of dense and MoE branches.
- Accelerator-Oriented Optimization: The system optimizes overall performance by employing super kernels to minimize execution overhead, leveraging L2 cache for weight prefetching to hide I/O latency, and utilizing high-speed interconnects for efficient scale-up and scale-out distributed processing.
- Deployment & Serving: LongCat-2.0 optimizes serving performance through a prefill-decode disaggregated architecture that leverages tailored parallelism schemes—CPP and SP for prefill, KVP and large EP for decode—alongside asynchronous load balancing to overcome stage-specific bottlenecks efficiently.

#### 🌟 Learning from Multiple Teachers
We introduce a specialized expert-group design in the post-training pipeline, organized into three categories: Agent Experts, Reasoning Experts, and Interaction Experts.

- Agent Experts focus on improving autonomous task execution in complex real-world scenarios. 
- Reasoning Experts extend the model’s depth of logical reasoning and enable adaptive computation based on problem difficulty.
- Interaction Experts focus on human alignment and user experience optimization.

Finally, we adopt the MOPD architecture to integrate the strongest capabilities from these three expert groups. This fusion enables the final model to combine strong agentic execution, deep reasoning, and high-quality interaction, allowing it to accurately understand complex user needs and reliably complete challenging real-world tasks.

For more details, please refer to our [technical blog]().

## Evaluation Results
We evaluate LongCat-2.0 against leading proprietary and open-weight models across agentic, coding, search, productivity and foundational capabilities. Unless noted with *, all scores are measured in-house under a unified harness; per-benchmark best results are shown in bold.

| Benchmark | LongCat-Pro | GPT-5.5 | Claude Opus 4.6 | Claude Opus 4.7 | Claude Opus 4.8 | Gemini 3.1 Pro |
|:----------|:-----------:|:-------:|:---------------:|:---------------:|:---------------:|:--------------:|
| **💻 Code Agent** | | | | | | |
| Terminal-Bench 2.1 | 70.8 | 73.8\* | - | 71.7\* | 78.9\* | 70.7\* |
| SWE-bench Pro | 59.5 | 58.6\* | 57.3\* | 64.3\* | 69.2\* | 54.2\* |
| SWE-bench Multilingual | 78.3 | 73.0 | 77.8\* | 80.5\* | 84.8\* | 72.0 |
| **🤖 General Agent** | | | | | | |
| LongCatClawBench † | 73.2 | 77.8 | 73.2 | 77.6 | 77.2 | 70.3 |
| VitaBench | 28.6 | 40.4 | 45.8 | 43.3 | 38.3 | 12.3 |
| **🔍 Search Agent** | | | | | | |
| BrowseComp | 79.9 | 84.4\* | 84.0\* | 79.3\* | 84.3\* | 85.9\* |
| RWSearch | 78.8 | 85.8 | 81.3 | 79.3 | 77.3 | 76.3 |
| **🧠 Foundational · Reasoning** | | | | | | |
| IMO-AnswerBench | 80.0 | 79.5 | 75.3\* | 81.8 | 75.3 | 90.0 |
| GPQA-diamond | 87.9 | 93.6\* | 91.3\* | 94.2\* | 92.4 | 94.3\* |
| **📏 Foundational · Long Context** | | | | | | |
| MRCR v2 (8 needle) | 63.7 | 85.7 | 88.2 | - | 83.4 | 72.6 |

## Quick Start

## Deployment

## Chat Website
You can chat with LongCat-2.0 on our official website: [https://longcat.ai](https://longcat.ai). -->

<!-- ## License Agreement

The **model weights** are released under the **MIT License**. 

Any contributions to this repository are licensed under the MIT License, unless otherwise stated. This license does not grant any rights to use Meituan trademarks or patents. 

See the [LICENSE](LICENSE) file for the full license text. -->

<!-- ## Usage Considerations 
This model has not been specifically designed or comprehensively evaluated for every possible downstream application. 

Developers should take into account the known limitations of large language models, including performance variations across different languages, and carefully assess accuracy, safety, and fairness before deploying the model in sensitive or high-risk scenarios. 
It is the responsibility of developers and downstream users to understand and comply with all applicable laws and regulations relevant to their use case, including but not limited to data protection, privacy, and content safety requirements. 

Nothing in this Model Card should be interpreted as altering or restricting the terms of the MIT License under which the model is released.  -->

<!-- ## Citation

## Contact
Please contact us at <a href="mailto:longcat-team@meituan.com">longcat-team@meituan.com</a> or join our WeChat Group if you have any questions.

#### WeChat Group
<img src=wechat-assets/Wechat.png width="200px"> -->

---

> [!NOTE]
> 🏋️ **Model weights coming soon** — stay tuned!
