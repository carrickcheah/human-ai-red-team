# iMIST -- Jailbreaking Large Language Models through Iterative Multi-step Progressive Tool-disguised Attacks via Reinforcement Learning

- **Source:** [2601.05466v1](../literature/multi-turn-attacks/2601.05466v1.pdf)
- **Authors:** Zhaoqi Wang, Zijian Zhang, Daqing He, Pengtao Kou, Xin Li, Jiamou Liu, Jincheng An, Yong Liu
- **Venue:** arXiv preprint 2601.05466, January 2026
- **PDF:** [arXiv](https://arxiv.org/abs/2601.05466v1)
- **Accessed:** 2026-04-03

---

## Claim

iMIST is a novel jailbreak method that disguises malicious queries as legitimate tool/function call invocations, exploiting the gap where LLMs are less restricted when acting as function-calling agents. Combined with an RL-driven interactive progressive optimisation loop, it outperforms all black-box baselines across DeepSeek-V3, Qwen3-32B, and GPT-OSS-120B while evading LlamaGuard, ShieldLLM, and perplexity-based defences.

## Background

LLM safety relies on alignment (RLHF, DPO) and output filtering (LlamaGuard, ShieldLLM). Existing jailbreak attacks -- template rewriting (ArtPrompt, FlipAttack), semantic obfuscation (WordGame, SATA), RL-based prompt mutation (RL-Jack, PASS) -- operate on natural language inputs and are increasingly caught by content-based detectors. Tool calling has become a standard LLM capability, yet alignment training focuses on direct text generation, leaving a gap when models operate as function-calling agents processing structured JSON parameters.

## Problem Statement

Current alignment and filtering mechanisms are trained on natural language harmful content but do not adequately cover the tool-calling interface. When an LLM acts as a function-calling agent, it fills structured JSON parameters rather than generating free-form text, making it difficult for alignment methods to distinguish legitimate from malicious tool invocations. No prior work has exploited this tool-calling gap for jailbreak attacks, and static attack strategies cannot adapt to model-specific safety behaviours in real time.

## Research Questions

- Can malicious queries be disguised as legitimate tool/function call invocations to bypass alignment mechanisms that focus on direct text generation?
- Can reinforcement learning dynamically select multi-turn optimisation strategies (retry, fallback, inject guidance, refine arguments, reconstruct toolset) to escalate response harmfulness in real time?
- How do tool-disguised attacks evade existing defence mechanisms (LlamaGuard, ShieldLLM, perplexity filters)?

## Theoretical Framework

- **Tool-Disguised Invocation (TDI):** Exploits the alignment gap between text generation and function-calling mode. Harmful queries are decomposed into sub-tasks, each mapped to fictitious toolsets conforming to OpenAI function-calling schema. The LLM fills tool parameters (structured JSON) rather than generating harmful text directly. Mock tool responses maintain the illusion of a legitimate execution environment. Parameters are then reconstructed into harmful natural language content.
- **Interactive Progressive Optimisation (IPO):** An RL agent (PPO-trained actor-critic) dynamically selects from five intervention actions based on a 15-dimensional state vector capturing JADES scores, tool usage, content features, and progress indicators. The loop iterates until harmfulness score reaches a target threshold (0.75) or maximum steps are exceeded.

## Methods

- **Query decomposition:** A decomposition prompt splits harmful query q into m sub-tasks (up to 5). Each sub-task is assigned a category-specific fictitious toolset conforming to OpenAI function-calling JSON schema.
- **TDI pipeline (Algorithm 1):** (1) Rewrite tool disguises query as role-based compliant version; (2) target LLM is instructed to act as function-calling agent with provided toolset; (3) LLM invokes one tool per iteration, building a call chain; (4) mock tool responses ("Tool executed successfully") sustain the illusion; (5) tool parameters are extracted and translated back to natural language via a reconstruction function.
- **IPO pipeline (Algorithm 2):** For each sub-task, the RL agent observes a 15-dimensional state (7 score features, 2 tool usage features, 2 content features, 4 progress features). Five actions available: retry, rollback, inject guidance, refine arguments, reconstruct toolset. Action mask enforces validity constraints.
- **Reward function:** Composite reward r_total = r_s + r_t + r_e + r_d. Score reward r_s uses tiered structure (-5 to +10 based on JADES score). Refusal penalty r_t = -1 for detected refusals. Efficiency reward r_e encourages early success. Information density reward r_d measures substantive content proportion.
- **RL training:** PPO with online updates, actor-critic architecture with shared feature extractor (128-64 hidden layers), GAE for advantage estimation. Batch size 8, learning rate 0.0003, discount factor 0.99, clip parameter 0.2.
- **Evaluation:** Black-box setting, no access to model internals. Tested on DeepSeek-V3 (671B MoE), Qwen3-32B, GPT-OSS-120B. Datasets: JailbreakBench (100 harmful behaviours, 10 misuse categories) and HarmfulQA (50 questions). Metrics: JADES score, StrongREJECT score, rejection rate. Defence evasion tested against LlamaGuard, ShieldLLM, perplexity filtering.

## Result

### Table I: Attack Performance (JADES/StrongREJECT/Rejection Rate)

| Method | DeepSeek-V3 HarmfulQA | DeepSeek-V3 JBB | Qwen3-32B HarmfulQA | Qwen3-32B JBB | GPT-OSS-120B HarmfulQA | GPT-OSS-120B JBB |
|---|---|---|---|---|---|---|
| Baseline | 0.06/0.04/70% | 0.14/0.07/59% | 0.13/0.07/42% | 0.10/0.09/65% | 0.00/0.00/100% | 0.01/0.01/99% |
| FlipAttack | 0.41/0.57/2% | 0.26/0.10/0% | 0.30/0.48/20% | 0.16/0.34/28% | 0.04/0.08/88% | 0.01/0.01/99% |
| PAIR | 0.29/0.07/4% | 0.30/0.45/10% | 0.35/0.22/18% | 0.36/0.24/15% | 0.05/0.04/58% | 0.03/0.06/92% |
| TAP | 0.37/0.52/8% | 0.11/0.08/19% | 0.36/0.41/14% | 0.40/0.42/12% | 0.02/0.10/84% | 0.02/0.04/94% |
| PASS | 0.24/0.27/14% | 0.28/0.56/9% | 0.24/0.18/8% | 0.23/0.20/15% | 0.10/0.16/64% | 0.08/0.16/72% |
| **iMIST** | **0.56/0.60/2%** | **0.50/0.64/4%** | **0.48/0.58/4%** | **0.46/0.54/0%** | **0.41/0.38/32%** | **0.38/0.30/36%** |

### Table II: Defence Evasion -- Unsafe Detection Rates on DeepSeek-V3 (JBB)

| Method | LlamaGuard | ShieldLLM | PPL |
|---|---|---|---|
| FlipAttack | 100.00% | 96.15% | 24.73 |
| PASS | 95.77% | 81.61% | 30.52 |
| **iMIST** | **17.65%** | **14.71%** | 28.19 |

- iMIST consistently achieves the highest JADES and StrongREJECT scores across all model-dataset combinations
- On DeepSeek-V3 HarmfulQA, iMIST achieves JADES 0.56 vs best baseline FlipAttack at 0.41
- On Qwen3-32B JBB, iMIST achieves 0% rejection rate while maintaining JADES 0.46
- GPT-OSS-120B is most robust (deliberative alignment), yet iMIST still reaches JADES 0.41 on HarmfulQA vs best baseline 0.22 (SATA)
- Defence evasion: iMIST detected by LlamaGuard at only 17.65% and ShieldLLM at 14.71%, far below most baselines (e.g., FlipAttack at 100%/96.15%)
- t-SNE analysis shows iMIST-attacked prompts positioned between benign and harmful clusters, explaining evasion of alignment-based detection

## Gap

- **No real tool execution:** iMIST uses mock tool responses only; does not explore attacks where tools actually execute (e.g., code execution, database access, file system operations), which would amplify real-world impact
- **Single-agent target only:** Does not address multi-agent systems, agent-to-agent attacks, or cross-tenant scenarios where tool-calling exploitation could cascade across trust boundaries
- **No defence proposal:** Identifies the tool-calling alignment gap but proposes no mitigation; HART Defence Layer could provide tool-gating (toolGate) countermeasures
- **Static toolset categories:** Fictitious toolsets are pre-defined per misuse category; does not explore adaptive tool discovery against real MCP/API ecosystems
- **Evaluation limited to three models:** Does not test against models with tool-use-specific alignment or tool-calling guardrails
- **No cross-turn memory exploitation:** Sub-tasks are processed independently; does not investigate how conversation memory or context window poisoning could enhance tool-disguised attacks

## HART Relevance

iMIST is directly relevant to RQ2 (tool-chain exploitation: prompt injection to RCE) by demonstrating that the function-calling interface is a fundamentally under-defended attack surface. The TDI mechanism maps precisely to HART Attack Layer plugins for tool-disguised jailbreaks, while the IPO loop provides a blueprint for HART's RL-guided multi-turn attack orchestration. The defence evasion results (17.65% LlamaGuard detection) validate HART's need for tool-gating at the Defence Layer (toolGate) rather than relying on content-based filtering alone.
