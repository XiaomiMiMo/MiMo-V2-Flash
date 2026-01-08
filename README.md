<br/><br/>

<div align="center">
  <picture>
    <source srcset="https://github.com/XiaomiMiMo/MiMo-V2-Flash/raw/main/figures/Xiaomi_MiMo_darkmode.png?raw=true" media="(prefers-color-scheme: dark)">
    <img src="https://github.com/XiaomiMiMo/MiMo-V2-Flash/raw/main/figures/Xiaomi_MiMo.png?raw=true" width="60%" alt="Xiaomi-MiMo" />
  </picture>
</div>

<br/>

<div align="center" style="line-height: 1;">
  |
  <a href="https://huggingface.co/XiaomiMiMo/MiMo-V2-Flash" target="_blank">🤗 HuggingFace</a>
  &nbsp;|
  <a href="https://github.com/XiaomiMiMo/MiMo-V2-Flash/blob/main/paper.pdf" target="_blank">📔 Technical Report </a>
  &nbsp;|
  <a href="https://mimo.xiaomi.com/blog/mimo-v2-flash" target="_blank">📰 Blog </a>
  &nbsp;|
  <br/><br/>
  <strong>Play around!</strong> &nbsp;
  <a href="https://aistudio.xiaomimimo.com" target="_blank">🗨️ Xiaomi MiMo Studio </a>
  &nbsp;
  <a href="https://platform.xiaomimimo.com/" target="_blank">🎨 Xiaomi MiMo API Platform </a>
</div>
<br/>

# MiMo-V2-Flash

**MiMo-V2-Flash** is a Mixture-of-Experts (MoE) language model with **309B total parameters** and **15B active parameters**. Designed for high-speed reasoning and agentic workflows, it utilizes a novel hybrid attention architecture and Multi-Token Prediction (MTP) to achieve state-of-the-art performance while significantly reducing inference costs.

<p align="center">
  <img width="80%" src="https://github.com/XiaomiMiMo/MiMo-V2-Flash/raw/main/figures/MiMo-v2-flash-performance.jpg?raw=true">
</p>

-----

## 1. Introduction

MiMo-V2-Flash creates a new balance between long-context modeling capability and inference efficiency. Key features include:

  * **Hybrid Attention Architecture**: Interleaves Sliding Window Attention (SWA) and Global Attention (GA) with a 5:1 ratio and an aggressive 128-token window. This reduces KV-cache storage by nearly 6x while maintaining long-context performance via learnable **attention sink bias**.
  * **Multi-Token Prediction (MTP)**: Equipped with a lightweight MTP module (0.33B params/block) using dense FFNs. This triples output speed during inference and will be good to accelerates rollout in RL training.
  * **Efficient Pre-Training**: Trained on 27T tokens using FP8 mixed precision and native 32k seq length. The context window supports up to 256k length.
  * **Agentic Capabilities**: Post-training utilizes Multi-Teacher On-Policy Distillation (MOPD) and large-scale agentic RL, achieving superior performance on **SWE-Bench** and complex reasoning tasks.

-----

## 2. Model Downloads

| Model                  | Total Params | Active Params | Context Length |                               Download                                |
| :--------------------- | :----------: | :-----------: | :------------: | :-------------------------------------------------------------------: |
| **MiMo-V2-Flash-Base** |     309B     |      15B      |      256k      | [🤗 HuggingFace](https://huggingface.co/XiaomiMiMo/MiMo-V2-Flash-Base) |
| **MiMo-V2-Flash**      |     309B     |      15B      |      256k      |   [🤗 HuggingFace](https://huggingface.co/XiaomiMiMo/MiMo-V2-Flash)    |

> [!IMPORTANT]
> We also open-source the 3-layer MTP weights to foster community research.

-----

## 3. Evaluation Results

### Base Model Evaluation

MiMo-V2-Flash-Base demonstrates strong performance across standard benchmarks, surpassing models with significantly larger parameter counts.

| Category         | Benchmark               | Setting/Length | MiMo-V2-Flash Base |  Kimi-K2 Base   | DeepSeek-V3.1 Base | DeepSeek-V3.2 Exp Base |
| :--------------- | :---------------------- | :------------- | :----------------: | :-------------: | :----------------: | :--------------------: |
| **Params**       | **#Activated / #Total** | -              |   **15B / 309B**   | **32B / 1043B** |   **37B / 671B**   |     **37B / 671B**     |
| **General**      | BBH                     | 3-shot         |        88.5        |      88.7       |        88.2        |          88.7          |
|                  | MMLU                    | 5-shot         |        86.7        |      87.8       |        87.4        |          87.8          |
|                  | MMLU-Redux              | 5-shot         |        90.6        |      90.2       |        90.0        |          90.4          |
|                  | MMLU-Pro                | 5-shot         |        73.2        |      69.2       |        58.8        |          62.1          |
|                  | DROP                    | 3-shot         |        84.7        |      83.6       |        86.3        |          86.6          |
|                  | ARC-Challenge           | 25-shot        |        95.9        |      96.2       |        95.6        |          95.5          |
|                  | HellaSwag               | 10-shot        |        88.5        |      94.6       |        89.2        |          89.4          |
|                  | WinoGrande              | 5-shot         |        83.8        |      85.3       |        85.9        |          85.6          |
|                  | TriviaQA                | 5-shot         |        80.3        |      85.1       |        83.5        |          83.9          |
|                  | GPQA-Diamond            | 5-shot         |        55.1        |      48.1       |        51.0        |          52.0          |
|                  | SuperGPQA               | 5-shot         |        41.1        |      44.7       |        42.3        |          43.6          |
|                  | SimpleQA                | 5-shot         |        20.6        |      35.3       |        26.3        |          27.0          |
| **Math**         | GSM8K                   | 8-shot         |        92.3        |      92.1       |        91.4        |          91.1          |
|                  | MATH                    | 4-shot         |        71.0        |      70.2       |        62.6        |          62.5          |
|                  | AIME 24&25              | 2-shot         |        35.3        |      31.6       |        21.6        |          24.8          |
| **Code**         | HumanEval+              | 1-shot         |        70.7        |      84.8       |        64.6        |          67.7          |
|                  | MBPP+                   | 3-shot         |        71.4        |      73.8       |        72.2        |          69.8          |
|                  | CRUXEval-I              | 1-shot         |        67.5        |      74.0       |        62.1        |          63.9          |
|                  | CRUXEval-O              | 1-shot         |        79.1        |      83.5       |        76.4        |          74.9          |
|                  | MultiPL-E HumanEval     | 0-shot         |        59.5        |      60.5       |        45.9        |          45.7          |
|                  | MultiPL-E MBPP          | 0-shot         |        56.7        |      58.8       |        52.5        |          50.6          |
|                  | BigCodeBench            | 0-shot         |        70.1        |      61.7       |        63.0        |          62.9          |
|                  | LiveCodeBench v6        | 1-shot         |        30.8        |      26.3       |        24.8        |          24.9          |
|                  | SWE-Bench (AgentLess)   | 3-shot         |        30.8        |      28.2       |        24.8        |          9.4*          |
| **Chinese**      | C-Eval                  | 5-shot         |        87.9        |      92.5       |        90.0        |          91.0          |
|                  | CMMLU                   | 5-shot         |        87.4        |      90.9       |        88.8        |          88.9          |
|                  | C-SimpleQA              | 5-shot         |        61.5        |      77.6       |        70.9        |          68.0          |
| **Multilingual** | GlobalMMLU              | 5-shot         |        76.6        |      80.7       |        81.9        |          82.0          |
|                  | INCLUDE                 | 5-shot         |        71.4        |      75.3       |        77.2        |          77.2          |
| **Long Context** | NIAH-Multi              | 32K            |        99.3        |      99.8       |        99.7        |         85.6*          |
|                  |                         | 64K            |        99.9        |      100.0      |        98.6        |         85.9*          |
|                  |                         | 128K           |        98.6        |      99.5       |        97.2        |         94.3*          |
|                  |                         | 256K           |        96.7        |        -        |         -          |           -            |
|                  | GSM-Infinite Hard       | 16K            |        37.7        |      34.6       |        41.5        |          50.4          |
|                  |                         | 32K            |        33.7        |      26.1       |        38.8        |          45.2          |
|                  |                         | 64K            |        31.5        |      16.0       |        34.7        |          32.6          |
|                  |                         | 128K           |        29.0        |       8.8       |        28.7        |          25.7          |

> \* indicates the model may fail to follow the prompt or format.

### Post-training Model Evaluation

Following our Post-Training Paradigm with MOPD and Agentic RL, the model achieves SOTA reasoning and agentic performance.



| Benchmark                      | MiMo-V2 Flash | Kimi-K2 Thinking | DeepSeek-V3.2 Thinking | Gemini-3.0 Pro | Claude Sonnet 4.5 | GPT-5 High |
| :----------------------------- | :-----------: | :--------------: | :--------------------: | :------------: | :---------------: | :--------: |
| **Reasoning**                  |               |                  |                        |                |                   |            |
| MMLU-Pro                       |     84.9      |       84.6       |          85.0          |      90.1      |       88.2        |    87.5    |
| GPQA-Diamond                   |     83.7      |       84.5       |          82.4          |      91.9      |       83.4        |    85.7    |
| HLE (no tools)                 |     22.1      |       23.9       |          25.1          |      37.5      |       13.7        |    26.3    |
| AIME 2025                      |     94.1      |       94.5       |          93.1          |      95.0      |       87.0        |    94.6    |
| HMMT Feb. 2025                 |     84.4      |       89.4       |          92.5          |      97.5      |       79.2        |    88.3    |
| LiveCodeBench-v6               |     80.6      |       83.1       |          83.3          |      90.7      |       64.0        |    84.5    |
| **General Writing**            |               |                  |                        |                |                   |            |
| Arena-Hard (Hard Prompt)       |     54.1      |       71.9       |          53.4          |      72.6      |       63.3        |    71.9    |
| Arena-Hard (Creative Writing)  |     86.2      |       80.1       |          88.8          |      93.6      |       76.7        |    92.2    |
| **Long Context**               |               |                  |                        |                |                   |            |
| LongBench V2                   |     60.6      |       45.1       |          58.4          |      65.6      |       61.8        |     -      |
| MRCR                           |     45.7      |       44.2       |          55.5          |      89.7      |       55.4        |     -      |
| **Code Agent**                 |               |                  |                        |                |                   |            |
| SWE-Bench Verified             |     73.4      |       71.3       |          73.1          |      76.2      |       77.2        |    74.9    |
| SWE-Bench Multilingual         |     71.7      |       61.1       |          70.2          |       -        |       68.0        |    55.3    |
| Terminal-Bench Hard            |     30.5      |       30.6       |          35.4          |      39.0      |       33.3        |    30.5    |
| Terminal-Bench 2.0             |     38.5      |       35.7       |          46.4          |      54.2      |       42.8        |    35.2    |
| **General Agent**              |               |                  |                        |                |                   |            |
| BrowseComp                     |     45.4      |        -         |          51.4          |       -        |       24.1        |    54.9    |
| BrowseComp (w/ Context Manage) |     58.3      |       60.2       |          67.6          |      59.2      |         -         |     -      |
| $\tau^2$-Bench                 |     80.3      |       74.3       |          80.3          |      85.4      |       84.7        |    80.2    |

-----

## 4. Model Architecture

<p align="center">
  <img width="80%" src="https://github.com/XiaomiMiMo/MiMo-V2-Flash/raw/main/figures/MiMo-v2-flash-arch.png?raw=true">
</p>

### Hybrid Sliding Window Attention

MiMo-V2-Flash addresses the quadratic complexity of long contexts by interleaving Local Sliding Window Attention (SWA) and Global Attention (GA).

  * **Configuration**: Stacks of $M=8$ hybrid blocks. Each block contains $N=5$ SWA layers followed by 1 GA layer.
  * **Efficiency**: SWA layers use a window size of 128 tokens, reducing KV cache significantly.
  * **Sink Bias**: Learnable attention sink bias is applied to maintain performance despite the aggressive window size.

### Lightweight Multi-Token Prediction (MTP)

Unlike traditional speculative decoding, our MTP module is natively integrated for training and inference.

  * **Structure**: Uses a dense FFN (instead of MoE) and SWA (instead of GA) to keep the parameter count low (0.33B per block).
  * **Performance**: Facilitates self-speculative decoding, tripling generation speed and mitigating GPU idleness during small-batch RL training.

-----

## 5. Post-Training Technical Highlights

MiMo-V2-Flash leverages a post-training pipeline designed to maximize reasoning and agentic capabilities through innovative distillation and reinforcement learning strategies.

### 5.1 Multi-Teacher On-Policy Distillation (MOPD)

We introduce **Multi-Teacher On-Policy Distillation (MOPD)**, a new paradigm that formulates knowledge distillation as a reinforcement learning process.
* **Dense Token-Level Guidance**: Unlike methods relying on sparse sequence-level feedback, MOPD utilizes domain-specific expert models (teachers) to provide supervision at every token position.
* **On-Policy Optimization**: The student model learns from its own generated responses rather than a fixed dataset. This eliminates exposure bias and ensures smaller, more stable gradient updates.
* **Inherent Reward Robustness**: Rewards are derived from the distribution divergence between student and teacher, making the process naturally resistant to reward hacking.

### 5.2 Scaling Agentic RL

We significantly scale up the agentic training environments to improve intelligence and generalization.
* **Massive Code Agent Environments**: We utilize real-world GitHub issues to create over 100,000 verifiable tasks. Our automated pipeline maintains a Kubernetes cluster capable of running over 10,000 concurrent pods with a 70% environment setup success rate.
* **Multimodal Verifier for WebDev**: For web development tasks, we employ a vision-based verifier that evaluates code execution via recorded videos rather than static screenshots. This reduces visual hallucination and ensures functional correctness.
* **Cross-Domain Generalization**: Our experiments show that large-scale RL training on code agents effectively generalizes to other domains, boosting performance in Math and General Agent tasks.

### 5.3 Advanced RL Infrastructure

To support high-throughput RL training for large-scale MoE models, we implemented several infrastructure optimizations on top of SGLang and Megatron-LM.
* **Rollout Routing Replay (R3)**: Addresses numerical precision inconsistencies in MoE routing between inference and training. R3 reuses the exact routed experts from rollout during the training pass, ensuring consistency with negligible overhead.
* **Request-Level Prefix Cache**: In multi-turn agent training, this cache stores KV states and routed experts from prior turns. It avoids re-computation and ensures sampling consistency across turns.
* **Fine-Grained Data Scheduler**: We extend the rollout engine to schedule fine-grained sequences instead of micro-batches. Combined with partial rollout, this significantly reduces GPU idleness caused by long-tail stragglers.
* **Toolbox & Tool Manager**: A two-layer design using Ray actor pools to handle resource contention. It eliminates cold-start delays for tool execution and isolates task logic from system policies.

-----

## 6. Inference & Deployment

MiMo-V2-Flash supports FP8 mixed precision inference. We recommend using **SGLang** for optimal performance.

### Quick Start with SGLang

Following https://lmsys.org/blog/2025-12-16-mimo-v2-flash/, please use the compatible SGLang version as follows.

```bash
pip install sglang==0.5.6.post2.dev8005+pr.15207.g39d5bd57a \
  --index-url https://sgl-project.github.io/whl/pr/ \
  --extra-index-url https://pypi.org/simple

#Launch the server
SGLANG_ENABLE_SPEC_V2=1 python3 -m sglang.launch_server \
        --model-path XiaomiMiMo/MiMo-V2-Flash \
        --served-model-name mimo-v2-flash \
        --pp-size 1 \
        --dp-size 2 \
        --enable-dp-attention \
        --tp-size 8 \
        --moe-a2a-backend deepep \
        --page-size 1 \
        --host 0.0.0.0 \
        --port 9001 \
        --trust-remote-code \
        --mem-fraction-static 0.75 \
        --max-running-requests 128 \
        --chunked-prefill-size 16384 \
        --reasoning-parser qwen3 \
        --tool-call-parser mimo \
        --context-length 262144 \
        --attention-backend fa3 \
        --speculative-algorithm EAGLE \
        --speculative-num-steps 3 \
        --speculative-eagle-topk 1 \
        --speculative-num-draft-tokens 4 \
        --enable-mtp

# Send request
curl -i http://localhost:9001/v1/chat/completions \
    -H 'Content-Type:application/json' \
    -d  '{
            "messages" : [{
                "role": "user",
                "content": "Nice to meet you MiMo"
            }],
            "model": "mimo-v2-flash",
            "max_tokens": 4096,
            "temperature": 0.8,
            "top_p": 0.95,
            "stream": true,
            "chat_template_kwargs": {
                "enable_thinking": true
            }
        }'
```

### Notifications

#### 1. System prompt

> [!IMPORTANT]
> The following system prompts are **HIGHLY** recommended, please choose from English and Chinese version.

English

```plaintext
You are MiMo, an AI assistant developed by Xiaomi.

Today's date: {date} {week}. Your knowledge cutoff date is December 2024.
```

Chinese

```plaintext
你是MiMo（中文名称也是MiMo），是小米公司研发的AI智能助手。

今天的日期：{date} {week}，你的知识截止日期是2024年12月。
```

#### 2. Sampling parameters

> [!IMPORTANT]
> Recommended sampling parameters:
>
> `top_p=0.95`
> 
> `temperature=0.8` for math, writing, web-dev
> 
> `temperature=0.3` for agentic taks (e.g., vibe-coding, tool-use)

#### 3. Tool-use practice

> [!IMPORTANT]
> In the thinking mode with multi-turn tool calls, the model returns a `reasoning_content` field alongside `tool_calls`. To continue the conversation, the user must persist all history `reasoning_content` in the `messages` array of each subsequent request.

-----

## 7. Citation

If you find our work helpful, please cite our technical report:

```bibtex
@misc{xiao2026mimov2flashtechnicalreport,
      title={MiMo-V2-Flash Technical Report}, 
      author={LLM-Core Xiaomi},
      year={2026},
      eprint={2601.02780},
      archivePrefix={arXiv},
      primaryClass={cs.CL},
      url={https://arxiv.org/abs/2601.02780}, 
}
```

## 8. Contact

Please contact us at [mimo@xiaomi.com](mailto:mimo@xiaomi.com), join our WeChat group below or open an issue if you have any questions.

<p align="center">
  <img src="https://github.com/XiaomiMiMo/MiMo-V2-Flash/raw/main/figures/wechat_group/wechat1.jpg?raw=true" width="20%" />
  <img src="https://github.com/XiaomiMiMo/MiMo-V2-Flash/raw/main/figures/wechat_group/wechat2.jpg?raw=true" width="20%" />
  <img src="https://github.com/XiaomiMiMo/MiMo-V2-Flash/raw/main/figures/wechat_group/wechat3.jpg?raw=true" width="20%" />
  <img src="https://github.com/XiaomiMiMo/MiMo-V2-Flash/raw/main/figures/wechat_group/wechat4.jpg?raw=true" width="20%" />
</p>
