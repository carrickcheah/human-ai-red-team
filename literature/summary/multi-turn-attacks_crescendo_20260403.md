# Crescendo — Multi-Turn LLM Jailbreak Attack

**Source:** [papers/2025/2404.01833v3.pdf](../2025/2404.01833v3.pdf)
**Authors:** Mark Russinovich, Ahmed Salem, Ronen Eldan (Microsoft)
**Venue:** USENIX Security 2025
**PDF:** [http://arxiv.org/pdf/2404.01833v3](http://arxiv.org/pdf/2404.01833v3)
**Accessed:** 2026-04-02

---

## Claim

Multi-turn jailbreaking using benign-looking prompts is far more effective than single-turn attacks. Crescendo exploits the model's own outputs to gradually escalate toward harmful content — no adversarial suffixes, no malicious inputs required. The automated version (Crescendomation) outperforms all existing single-turn jailbreaks by 29-61% on GPT-4 and 49-71% on Gemini-Pro.

## Background and Rationale

LLMs are safety-aligned to refuse harmful requests, but existing jailbreak defenses focus on single-turn attacks. Single-turn methods use adversarial suffixes or crafted prompts that are detectable by input filters. Crescendo introduces a fundamentally different approach: multi-turn conversations using only benign, human-readable prompts that exploit the model's tendency to follow conversational context — mirroring the "foot-in-the-door" psychological manipulation technique.

## Problem Statement

All current LLM safety benchmarks and alignment strategies evaluate only single-turn interactions. While these defenses successfully block direct harmful requests, they fail against gradual multi-turn escalation where each individual prompt appears harmless. Building adaptive multi-turn datasets for benchmarking is complicated because each turn depends on the specific model's response, making static datasets insufficient.

## Research Questions

- Can multi-turn conversational jailbreaks using only benign prompts bypass safety alignment across major LLMs?
- How does multi-turn ASR compare to state-of-the-art single-turn techniques?
- Can Crescendo be automated while maintaining effectiveness?
- Do Crescendo prompts transfer across different LLMs?
- How effective are existing defenses (Self-Reminder, Goal Prioritization) against multi-turn attacks?

## Theoretical Framework / Methodology

- **Black-box threat model** — no model internals needed, only API access
- **Foot-in-the-door psychology** — small benign requests escalate to larger harmful ones
- **LLM-as-Judge evaluation** — Judge LLM + Secondary Judge + external API validation
- **Comparative benchmarking** — against 4 SOTA single-turn baselines (MSJ, PAIR, CoA, CIA)
- **Automated red teaming** — attacker LLM generates prompts, feedback loop refines strategy

## Methods

- **Manual Crescendo:** Tested against 5 LLM families (ChatGPT/GPT-4, Gemini Pro/Ultra, Claude-2/3, LLaMA-2/3 70b) on 15 harmful tasks across 8 categories (illegal activities, self-harm, misinformation, pornography, profanity, sexism, hate speech, violence). Max 4 attempts per task with backtracking on refusal.
- **Crescendomation (automated):** Uses GPT-4 as attacker LLM. Feeds target responses back to generate next turn. Includes backtracking mechanism (retract and rephrase on refusal, up to 10 times), feedback loop with Judge + Secondary Judge, and conversation summary to keep attacker on track.
- **3-layer evaluation:** (1) Judge LLM (GPT-4) scores success via boolean flag + 0-100 score, (2) Secondary Judge reviews primary judge's reasoning for false negatives, (3) External APIs — Google Perspective API (toxicity) + Azure Content Filter (hate/violence/sexual/self-harm, scale 0-7).
- **Dataset:** AdvBench subset (50 tasks) + HarmBench (100 tasks).
- **Baselines:** Many-Shot Jailbreak (MSJ), PAIR, Chain of Attack (CoA), Contextual Interaction Attack (CIA).
- **Settings:** Temperature 0.5, max 10 rounds, max 10 backtracking steps per round, 10 independent runs per task.

## Result

| Metric                          | GPT-4             | Gemini-Pro         |
| ------------------------------- | ----------------- | ------------------ |
| Average ASR (Crescendomation)   | 56.2              | 82.6               |
| Binary ASR (at least 1 success) | 98% (49/50 tasks) | 100% (50/50 tasks) |
| Best baseline (MSJ)             | 37.0 (86%)        | 35.4 (88%)         |
| Improvement over best baseline  | +29-61%           | +49-71%            |

- **Turns required:** Most tasks jailbroken in <5 turns (Table 5). Some as few as 1-2 turns for easy tasks (Climate, Denial)
- **Manual Crescendo:** Successfully jailbroke nearly all tasks on all models (Table 2) — including Claude-2, Claude-3, LLaMA
- **Multimodal:** Also works on image generation (ChatGPT, Gemini) — can produce images the model would normally refuse
- **Transferability:** Crescendo prompts from one model partially transfer to others (Election task: 90%+ ASR on both GPT-4 and Gemini when transferred)
- **Against defenses:** Self-Reminder and Goal Prioritization defenses reduce ASR on some tasks but don't eliminate it — tasks like Election, Climate, Stabbing remain at high ASR even with defenses active
- **HarmBench (100 tasks):** Crescendo avg ASR 63.2% vs MSJ 38.9%; binary ASR 91% vs 70%
- **Model size:** LLaMA-2 7b and 70b show similar vulnerability — model size does not correlate with resistance

## Gap

1. **Text-only harm, no tool exploitation tested.** All tasks target harmful text generation. Zero testing of tool-calling, function execution, or agentic capabilities.
2. **No cross-tenant or data exfiltration scenarios.** Attack goals are about generating prohibited content, never about extracting data or escalating privileges.
3. **Judge reliability is questionable.** Judge LLM has false positives/negatives. Binary ASR definition (any run succeeds = success) inflates results to near 100%.
4. **Models are outdated.** Tested on GPT-3.5, GPT-4, Claude-2/3, Gemini Pro, LLaMA-2 70b — all pre-2025.
5. **No systematic taxonomy of escalation strategies.** "Foot-in-the-door" psychology described but not formally classified.
6. **Defense evaluation is shallow.** Only 2 defenses tested. No system-level defenses (input filters, rate limiting, conversation monitoring).
7. **GPT-4 used as both attacker and judge** — potential bias when attacking GPT-4 as target.

## HART Relevance

Core evidence for RQ1: primary citation for "multi-turn > single-turn." HART extends Crescendo from text-harm to tool-exploitation and data exfiltration. Crescendomation's algorithm (Algorithm 1, p.8) becomes a plugin in HART's Attack Layer. Their 3-layer evaluation is a precursor to HART's courtroom-style multi-judge grading. Open-sourced via [PyRIT](https://github.com/microsoft/PyRIT).
