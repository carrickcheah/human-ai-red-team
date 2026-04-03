# Mastermind — Knowledge-Driven Multi-Turn Jailbreaking on Large Language Models

**Source:** [../literature/multi-turn-attacks/2601.05445v1.pdf](../literature/multi-turn-attacks/2601.05445v1.pdf)
**Authors:** Songze Li, Ruishi He, Xiaojun Jia, Jun Wang, Zhihui Fu (Southeast University, Nanyang Technological University, OPPO Research Institute)
**Venue:** arXiv preprint 2601.05445, January 2026
**PDF:** [https://arxiv.org/abs/2601.05445v1](https://arxiv.org/abs/2601.05445v1)
**Accessed:** 2026-04-03

---

## Claim

Mastermind proposes a knowledge-driven, self-improving multi-turn jailbreak framework that uses a hierarchical multi-agent architecture (Planner, Executor, Controller) paired with a self-evolving knowledge repository. By accumulating and reusing attack strategies extracted from successful trajectories, it achieves significantly higher attack success rates than existing baselines across frontier models.

## Background and Rationale

Multi-turn jailbreak attacks exploit extended conversations to progressively erode LLM safety guardrails. Existing approaches rely on fixed templates or human-designed heuristics, limiting their adaptability against evolving defences. As LLMs are deployed in safety-critical agentic systems, understanding and stress-testing multi-turn vulnerabilities becomes essential for building robust defences and informing red teaming methodologies.

## Problem Statement

Current multi-turn jailbreak methods suffer from three key limitations: (1) strategic myopia — they optimise individual turns without long-horizon planning; (2) inflexibility — rigid prompt templates cannot adapt to diverse target models; (3) constrained exploration — reliance on human-designed heuristics restricts the discovery of novel attack vectors. No existing framework systematically accumulates and reuses adversarial knowledge across attacks.

## Research Questions

- How can multi-turn jailbreak attacks be planned strategically over long conversational horizons rather than myopically per turn?
- Can successful attack strategies be automatically distilled, stored, and retrieved to improve future attacks?
- Does strategy-level genetic fuzzing outperform token-level mutation for generating diverse multi-turn attack trajectories?
- How effective is a self-improving knowledge repository against frontier LLMs with state-of-the-art safety alignment?

## Theoretical Framework / Methodology

- **Hierarchical multi-agent architecture:** Planner generates high-level adversarial trajectories; Executor produces tactical per-turn prompts; Controller performs quality assurance with retry/abort logic.
- **Two-phase pipeline:** (1) Knowledge Accumulation — a distiller agent extracts reusable strategies from successful attack trajectories and stores them in a vector database; (2) Knowledge-Driven Fuzzing — retrieves relevant strategies for new targets and applies genetic-based recombination.
- **Strategy-level fuzzing:** Operates at the abstraction of attack strategies rather than token-level perturbations, enabling more meaningful exploration of the attack space.
- **Self-evolving knowledge repository:** The vector database grows with each successful attack, enabling continuous improvement without human intervention.

## Methods

- **Knowledge Accumulation Phase:** After each successful multi-turn jailbreak, a distiller agent analyses the full conversation trajectory to extract generalisable attack strategies (e.g., role-play escalation, authority impersonation, incremental desensitisation). Strategies are embedded and stored in a vector database indexed by target category, model type, and attack pattern.
- **Knowledge-Driven Fuzzing Phase:** Given a new harmful target query, the system retrieves the top-k most relevant strategies from the repository. A genetic-based fuzzing engine recombines and mutates these strategies to produce novel attack plans. The Planner decomposes each plan into a multi-turn trajectory. The Executor generates concrete prompts for each turn, adapting to the target model's responses. The Controller evaluates response quality at each turn, deciding whether to continue, retry with a different strategy, or abort.
- **Evaluation setup:** Tested on GPT-5, Claude 3.7 Sonnet, and other frontier models using standard harmful behaviour benchmarks. Attack success rate (ASR) measured via automated judge models and human evaluation.

## Result

| Model | Mastermind ASR | Best Baseline ASR | Improvement |
|-------|---------------|-------------------|-------------|
| GPT-5 | Significantly higher | Lower | Substantial |
| Claude 3.7 Sonnet | Significantly higher | Lower | Substantial |

- Mastermind outperforms all existing multi-turn jailbreak baselines across tested models.
- The self-evolving knowledge repository demonstrates compounding effectiveness — ASR improves as more attack trajectories are accumulated.
- Strategy-level fuzzing produces more diverse and effective attack trajectories than token-level mutation approaches.
- The hierarchical multi-agent architecture enables coherent long-horizon attack planning that single-agent methods cannot achieve.

## Gap

- No defensive countermeasure analysis — the paper does not propose or evaluate defences against its own attack framework.
- Knowledge repository is attack-only — no dual-use exploration of the same architecture for automated defence generation.
- Limited to text-based jailbreaks — does not address tool-chain exploitation, memory poisoning, or cross-agent attack propagation in agentic AI systems.
- No evaluation of multi-tenant or multi-agent deployment scenarios.
- Does not map findings to established security taxonomies (OWASP, MITRE ATLAS).

## HART Relevance

Directly informs RQ1 (multi-turn attack taxonomy) by demonstrating a knowledge-driven taxonomy of reusable attack strategies. The hierarchical Planner-Executor-Controller architecture maps onto HART's Attack Layer as a plugin pattern. The self-evolving knowledge repository concept could be adapted for HART's Evaluation Layer to accumulate both attack and defence intelligence. Strategy-level fuzzing validates HART's design goal of strategy-aware, not token-level, red teaming.
