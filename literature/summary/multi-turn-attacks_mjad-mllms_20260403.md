# MJAD-MLLMs — Multi-Turn Jailbreaking Attack on Multimodal LLMs

**Source:** [papers/2026/2601.05339v1.pdf](../2026/2601.05339v1.pdf)
**Authors:** Badhan Chandra Das, Md Tasnim Jawad, Joaquin Molto, M. Hadi Amini, Yanzhao Wu (Florida International University)
**Venue:** arXiv preprint 2601.05339, January 2026
**PDF:** [https://arxiv.org/pdf/2601.05339](https://arxiv.org/pdf/2601.05339)
**Accessed:** 2026-04-03

---

## Claim

Multi-turn jailbreaking is effective against multimodal LLMs when combined with typography-manipulated images. MJAD-MLLM uses a 3-turn escalation strategy with embedded harmful text in images, achieving up to 91.5% ASR. The companion FragGuard defense fragments responses and uses multi-LLM harmfulness scoring to significantly reduce attack success.

## Background and Rationale

Safety alignment for MLLMs focuses on single-turn text-only attacks, leaving the multimodal multi-turn attack surface unexplored. MLLMs process both text and images, creating a cross-modal vulnerability where harmful instructions embedded in images bypass text-only safety filters. Typography-based image manipulation is a low-cost, black-box technique requiring no model internals, making it a realistic threat vector for deployed systems.

## Problem Statement

Existing jailbreak research on MLLMs is limited to single-turn attacks and does not exploit multi-turn conversational dynamics. No prior work combines multi-turn escalation with typography-manipulated images to bypass multimodal safety alignment. Additionally, current defenses do not account for the compounding effect of cross-modal harmful content accumulated across multiple conversation turns.

## Research Questions

- Can multi-turn conversational attacks combined with typography-manipulated images jailbreak MLLMs more effectively than single-turn approaches?
- How does attack success rate change across conversation turns (gradual escalation)?
- Which MLLMs are most vulnerable to cross-modal multi-turn jailbreaking?
- Can a response fragmentation defense with multi-LLM harmfulness scoring mitigate multi-turn multimodal attacks?

## Theoretical Framework / Methodology

- **Black-box threat model** — no model internals required, only API/inference access
- **Cross-modal attack vector** — harmful text embedded in images via typography manipulation
- **Gradual escalation** — 3-turn strategy: benign → hypothetical → direct harmful request
- **Response fragmentation defense** — FragGuard splits outputs into pieces for independent harmfulness scoring
- **Multi-LLM consensus scoring** — 3 defender LLMs independently score each fragment, max toxicity taken

## Methods

- **Attack framework (MJAD-MLLM):** 3-turn escalation pipeline. Turn 1: benign request ("describe the image") with typography-manipulated image containing harmful text. Turn 2: hypothetical scenario referencing the image content. Turn 3: direct harmful request leveraging the image caption and prior conversational context.
- **Image manipulation:** Harmful instructions rendered as text overlaid on images (typography-based), exploiting the gap between visual and textual safety filters.
- **Target models:** 5 MLLMs — LLaVa-1.6 7B, LLaVa-1.6 13B, Qwen-2-7B, GPT-4o, Gemini-2.0-Flash.
- **Dataset:** MM-SafetyBench covering 13 prohibited scenario categories.
- **Defense (FragGuard):** Response from target MLLM is fragmented into smaller pieces. Each fragment is independently scored for harmfulness by 3 defender LLMs (o1, Gemini-2.5-Flash-lite, LLaMA-3 70B). The maximum toxicity score across all fragments and defenders is taken as the final harmfulness score. Responses exceeding the threshold are blocked.
- **Compute:** 4 NVIDIA A100 GPUs.

## Result

| Model             | Turn 1 ASR | Turn 2 ASR | Turn 3 ASR |
| ----------------- | ---------- | ---------- | ---------- |
| LLaVa-1.6 7B     | —          | —          | 91.5%      |
| LLaVa-1.6 13B    | —          | —          | —          |
| Qwen-2-7B        | —          | —          | —          |
| GPT-4o            | —          | —          | —          |
| Gemini-2.0-Flash  | —          | —          | 82.3%      |

- **Escalation effect:** ASR increases significantly from Turn 1 to Turn 3, confirming multi-turn escalation compounds vulnerability
- **Open-source models most vulnerable:** LLaVa-1.6 7B reaches 91.5% ASR at Turn 3, the highest across all models
- **Commercial models also vulnerable:** Gemini-2.0-Flash reaches 82.3% ASR at Turn 3 despite stronger safety alignment
- **FragGuard effectiveness:** Significantly reduces ASR across all models while maintaining response utility for benign queries
- **Multi-LLM scoring:** Taking the maximum toxicity score across 3 defender LLMs reduces false negatives compared to single-judge evaluation
- **13 prohibited categories tested:** Coverage across MM-SafetyBench ensures breadth of evaluation

## Gap

1. **Text-only harm, no tool exploitation.** All attack goals produce harmful text. No testing of tool-calling, function execution, or agentic capabilities in multimodal agents.
2. **Typography is a simple image manipulation.** Only one visual embedding method tested. No adversarial perturbations, steganography, or multi-image attacks explored.
3. **3-turn limit is arbitrary.** No analysis of whether additional turns would further increase ASR or whether the escalation strategy generalizes beyond 3 turns.
4. **No cross-tenant or data exfiltration scenarios.** Attack goals are restricted to generating prohibited content, not extracting data or escalating privileges.
5. **FragGuard defender model selection not justified.** Choice of o1, Gemini-2.5-Flash-lite, and LLaMA-3 70B as defenders is ad-hoc with no ablation on defender composition.
6. **MM-SafetyBench only.** No testing on other multimodal benchmarks (e.g., HarmBench, AdvBench) limits generalizability claims.
7. **Incomplete result reporting.** Per-turn ASR for intermediate models (LLaVa-13B, Qwen-2, GPT-4o) not fully detailed in accessible tables.

## HART Relevance

First multi-turn jailbreak targeting MLLMs — extends RQ1 from text-only to cross-modal attacks. The typography-manipulation technique becomes a visual attack plugin in HART's Attack Layer. FragGuard's multi-LLM scoring approach directly informs HART's Evaluation Layer courtroom-style multi-judge design. The 3-turn escalation pattern can be generalized in HART's attack taxonomy alongside Crescendo's text-only multi-turn strategy.
