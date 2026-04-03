# Jailbreaking LLMs & VLMs — First Unified Survey Spanning Text-Only to Multimodal Jailbreak Attacks, Defenses, and Evaluation

**Source:** [literature/multi-turn-attacks/2601.03594v1.pdf](../../literature/multi-turn-attacks/2601.03594v1.pdf)
**Authors:** Zejian Chen, Chaozhuo Li, Chao Li, Xi Zhang, Litian Zhang, Yiming Hei
**Venue:** arXiv preprint 2601.03594, January 2026
**PDF:** [https://arxiv.org/abs/2601.03594v1](https://arxiv.org/abs/2601.03594v1)
**Accessed:** 2026-04-03

---

## Claim (50 words)

Jailbreak vulnerabilities in LLMs and VLMs are structurally inevitable, arising from incomplete training data, linguistic ambiguity, and generative uncertainty. The paper proposes a three-dimensional survey framework (Attack, Defense, Evaluation) that unifies text-only and multimodal jailbreak research, and offers layered defense principles spanning perception, generation, and parameter layers.

## Background and Rationale (60 words)

Prior surveys address LLM jailbreaks or VLM attacks in isolation. No existing work spans the full spectrum from text-only to multimodal settings while consolidating shared mechanisms and proposing unified defenses. As VLMs are deployed in high-risk domains (autonomous driving, medical imaging), cross-modal attack surfaces multiply. The hallucination-jailbreak distinction remains under-studied, leading to inflated attack success rates and misallocated defense resources.

## Problem Statement (60 words)

Existing jailbreak research is fragmented across modalities, with no unified taxonomy covering LLM and VLM attacks, defenses, and evaluation metrics. Hallucinations are frequently conflated with jailbreaks, distorting security assessments. There is no systematic framework that maps structural causes of jailbreak vulnerabilities to layered defense principles, leaving practitioners without coherent guidance for building robust multimodal AI systems.

## Research Questions

- What are the structural root causes of jailbreak vulnerabilities in LLMs and VLMs?
- How can jailbreak attack methods be systematically categorized across text-only and multimodal settings?
- What defense mechanisms exist, and how can they be unified across modalities and model layers?
- How should jailbreak effectiveness be evaluated, and what metrics apply to multimodal contexts?
- What is the distinction and connection between hallucinations and jailbreaks?

## Theoretical Framework / Methodology

- **3-dimensional survey framework:** Attack, Defense, Evaluation
- **Root cause attribution:** Incomplete training data, linguistic ambiguity (intent classification failure), generative uncertainty (halting problem)
- **Hallucination vs. jailbreak distinction:** Hallucinations lack malicious intent and stem from model inability; jailbreaks are deliberately triggered by adversarial inputs
- **Unified defense principles across three layers:**
  - Perception layer: variant-consistency and gradient-sensitivity detection
  - Generation layer: safety-aware decoding and output review
  - Parameter layer: adversarially augmented preference alignment
- **Comparative survey analysis:** Table I shows this is the only survey covering LLM attacks, LLM defenses, VLM attacks, VLM defenses, and evaluation methods simultaneously

## Methods (point form, 100-200 words)

- **Systematic literature review** consolidating 150+ references across LLM and VLM jailbreak research
- **Attack taxonomy for LLMs** (7 categories): template attacks (role-play, nested fiction, prompt rewriting), steganography attacks (ASCII art, multi-language, cipher substitution), ICL-based attacks (adversarial example manipulation, multi-turn context manipulation, semantic decomposition), adversarial attacks (GCG, AutoDAN, ARCA, COLD-Attack), RL-based attacks (policy-driven mutation, deep RL search, representation-space guidance), LLM-based attacks (GUARD, persuasion, personality modulation, tree-of-thought), fine-tuning attacks (shadow alignment, LoRA, unalignment)
- **Attack taxonomy for VLMs** (3 categories): prompt-based image injection (AutoJailbreak, FigStep, typographic attacks), combined prompt-and-image perturbation (SGA, OT-Attack, CroPA, compositional adversarial attacks), proxy model transfer (substitute VLM generation, momentum iteration, input diversity)
- **Defense taxonomy:** prompt-level (confusion detection, smoothing, paraphrasing) and model-level (RLHF, safety fine-tuning, system prompt optimization) for LLMs; prompt perturbation, response evaluation (ECSO), and model fine-tuning (DRESS, AdaShield) for VLMs
- **Evaluation metrics:** ASR, toxicity score, query/time cost for LLMs; ASR, clean metric (BLEU/CIDEr), OASR, toxicity score for VLMs
- **Benchmark comparison** (Table II): JailBreakV-28K, AdvBench, MM-SafetyBench, SafetyBench, JailbreakBench, MHJ

## Result (tables + bullets)

| Dimension | LLM Coverage | VLM Coverage |
|-----------|-------------|-------------|
| Attack categories | 7 (template, steganography, ICL, adversarial, RL, LLM-based, fine-tuning) | 3 (prompt injection, combined perturbation, proxy transfer) |
| Defense categories | 2 (prompt-level, model-level) | 3 (prompt perturbation, response evaluation, model fine-tuning) |
| Evaluation metrics | 4 (ASR, toxicity, queries, time cost) | 4 (ASR, clean metric, OASR, toxicity) |

| Benchmark | Modality | Size | Scope |
|-----------|----------|------|-------|
| JailBreakV-28K | Multimodal | 28,000 | Diverse attack strategies for MLLMs |
| AdvBench | Text | 500 | LLM jailbreak evaluation |
| MM-SafetyBench | Multimodal | 5,040 | 13 scenarios for multimodal safety |
| SafetyBench | Text | 11,435 | Multiple-choice safety evaluation |
| JailbreakBench | Text | 200 | Harmful and benign behavior prompts |
| MHJ | Text | 2,912 | Real human multi-turn jailbreaks |

- Multi-turn stealthy dialogues (Crescendo, CFA, Contextual Interaction Attack) consistently outperform single-turn baselines
- GCG-family adversarial suffixes achieve near-100% ASR on some models but are detectable via perplexity filters
- Visual instruction tuning induces a "forgetting effect" that weakens LLM safety alignment in VLMs
- Cross-modal attacks (combined text + image perturbation) are harder to defend than unimodal attacks
- BABYBLUE evaluation framework distinguishes truly harmful outputs from hallucinated/misaligned outputs through classification, textual, and functional stages
- Hallucinations can inflate apparent ASR when evaluators cannot distinguish "misaligned harmless" from "malicious" completions

## Gap (point form)

- **No agentic AI coverage:** Survey addresses standalone LLMs/VLMs but not multi-agent systems, tool-using agents, or agentic workflows where jailbreaks cascade through tool chains
- **No multi-turn attack formalization for agents:** ICL-based multi-turn attacks (Crescendo, CFA) are noted but not modeled in the context of stateful agent memory or persistent conversation threads
- **No tool-chain exploitation:** Prompt injection leading to tool invocation (RCE, file exfiltration) is entirely absent; attack taxonomy stops at harmful text/image generation
- **No cross-tenant threat model:** Multi-tenant deployment risks (shared context windows, data leakage between tenants) are not considered
- **No human-AI collaborative red teaming:** MHJ benchmark uses human-crafted jailbreaks, but there is no framework for hybrid human-AI red teaming workflows
- **Defense principles are conceptual:** Unified defense layers (perception, generation, parameter) are proposed without empirical validation or implementation architecture
- **Single-model scope:** All attacks target individual models; no analysis of how jailbreaks propagate across agent-to-agent communication or orchestration layers

## HART Relevance (60 words)

The 3-dimensional Attack-Defense-Evaluation framework parallels HART's 4-layer architecture (Attack, Target, Evaluation, Defence) and validates the structural approach to jailbreak taxonomy. The multi-turn ICL attack coverage (Crescendo, CFA, DrAttack) directly informs RQ1. The hallucination-jailbreak distinction is critical for RQ4's red-teaming evaluation accuracy. However, the absence of tool-chain exploitation and multi-tenant analysis confirms gaps HART must address through RQ2 and RQ3.
