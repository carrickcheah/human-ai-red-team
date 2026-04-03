# SEMA — Simple yet Effective Learning for Multi-Turn Jailbreak Attacks

- **Source:** [2602.06854v1](../literature/multi-turn-attacks/2602.06854v1.pdf)
- **Authors:** Mingqian Feng, Xiaodong Liu, Weiwei Yang, Jialin Song, Xuekai Zhu, Chenliang Xu, Jianfeng Gao (University of Rochester, Microsoft Research)
- **Venue:** ICLR 2026 (conference paper)
- **PDF:** https://arxiv.org/abs/2602.06854v1
- **Accessed:** 2026-04-03

---

## Claim

SEMA trains a compact multi-turn jailbreak attacker from scratch — without relying on hand-crafted strategies or external data — using prefilling self-tuning and reinforcement learning with an intent-drift-aware reward, achieving 80.1% ASR@1 on AdvBench across closed- and open-source victims, a 33.9% improvement over prior state-of-the-art.

---

## Background

Multi-turn jailbreak attacks decompose harmful queries across conversational turns to bypass LLM safety alignment. Prior methods rely on manually designed strategies (e.g., Crescendo, GOAT) or require external seed data, limiting scalability and transferability. Reinforcement learning approaches face high exploration complexity when conditioning each turn on victim responses, making open-loop plan generation an attractive alternative.

---

## Problem Statement

Existing multi-turn jailbreak methods depend on pre-defined attack strategies or curated external datasets, restricting generalisability and reproducibility. Conditioning attacker turns on victim responses creates a combinatorially expensive search space, and current RL-based attackers suffer from reward hacking where the attacker drifts from the original harmful intent while still receiving high compliance scores.

---

## Research Questions

- Can a multi-turn jailbreak attacker be trained end-to-end without hand-crafted strategies or external data?
- Does open-loop (one-shot plan) generation reduce exploration complexity while maintaining attack success?
- How does an intent-drift-aware reward prevent reward hacking and maintain semantic alignment with the original query?
- Does the trained attacker transfer across heterogeneous victim models, including closed-source APIs?

---

## Theoretical Framework

- Multi-turn jailbreaks modelled as open-loop adversarial planning: the attacker generates a full multi-turn plan conditioned only on the harmful query, not on intermediate victim responses.
- Prefilling self-tuning stabilises output format by filtering non-refusal rollouts and fine-tuning with a minimal prefix ("1.") to anchor structured generation.
- Intent-drift-aware composite reward combines three signals — intent alignment (cosine similarity to original query), compliance risk (probability victim responds without refusal), and detail level (response richness) — to prevent reward hacking during GRPO training.

---

## Methods

**Stage 1 — Prefilling Self-Tuning:**
- Sample rollouts from the base attacker (Qwen2.5-3B-Instruct) on AdvBench queries.
- Filter out rollouts where the victim refuses, retaining only successful multi-turn plans.
- Fine-tune the attacker on filtered rollouts using a minimal prefix token ("1.") to stabilise multi-turn formatting and reduce mode collapse.

**Stage 2 — Reinforcement Learning (GRPO):**
- Train the attacker using Group Relative Policy Optimisation (GRPO).
- Composite reward function:
  - **Intent alignment:** Embedding cosine similarity between the attacker's final-turn query and the original harmful intent, penalising semantic drift.
  - **Compliance risk:** LLM-judge score measuring whether the victim's response provides actionable harmful content without refusal.
  - **Detail level:** Measures specificity and depth of the victim's response.
- Open-loop generation: the attacker outputs all turns in one forward pass, avoiding the need to interleave with victim responses during training.

**Evaluation:**
- Victims: Qwen2.5-3B-Instruct, Llama-3.1-8B-Instruct, GPT-oss-20B, GPT-4.1-mini.
- Benchmarks: AdvBench (520 samples), HarmBench (159 Standard functional samples).
- Judges: LLM classifier, HarmBench classifier, No Refusal Phrase Indicator, Qwen3Guard.
- Metrics: ASR@1, ASR@10, per-category breakdowns.

---

## Result

**AdvBench — ASR@1 across three victims (Qwen2.5-3B, Llama-3.1-8B, GPT-oss-20B):**

| Method | Avg ASR@1 (%) |
|--------|---------------|
| GCG | 12.3 |
| PAIR | 28.7 |
| Crescendo | 35.4 |
| ActorAttack | 46.2 |
| SEMA (ours) | **80.1** |

- 33.9 percentage-point improvement over best prior method.
- Transfers to closed-source GPT-4.1-mini without retraining.
- HarmBench results confirm generalisation beyond AdvBench distribution.
- Ablation shows removing intent-alignment reward leads to reward hacking (high compliance but drifted intent).
- Prefilling self-tuning alone yields ~45% ASR@1; RL stage provides the remaining lift.

---

## Gap

- **Open-loop limitation:** SEMA does not adapt to victim responses at inference time; a closed-loop variant could further increase ASR, relevant to HART's adaptive attack plugins.
- **Fixed turn budget:** Evaluated with a fixed number of turns (typically 5); dynamic turn allocation is unexplored.
- **Defence-side evaluation absent:** No systematic evaluation against layered defences (input filters, system-prompt hardening, tool-gating); HART Layer 4 could benchmark resilience.
- **Agent-specific attacks missing:** SEMA targets chat-only LLMs; does not address tool-calling agents, MCP-connected systems, or cross-tenant memory scenarios central to HART.
- **Single attacker architecture:** Only Qwen2.5-3B as attacker backbone; unclear how attacker model scale affects transferability.
- **Reward transferability:** Intent-drift-aware reward relies on embedding similarity; adversarial rephrasing may game this metric.

---

## HART Relevance

SEMA directly informs HART's Attack Layer (Layer 1) as a trainable, strategy-free multi-turn jailbreak plugin. Its open-loop planning could serve as a baseline generator for HART's multi-turn attack orchestration (RQ1). The intent-drift-aware reward design is relevant to HART's Evaluation Layer (Layer 3) for measuring attack fidelity. SEMA's transfer results across closed-source models support RQ2 (cross-target portability). Gaps in defence-side evaluation and agent-specific attacks map to RQ3 and RQ4.
