# The Echo Chamber: A Multi-Turn LLM Jailbreak

**Source:** [../literature/multi-turn-attacks/2601.05742v1.pdf](../literature/multi-turn-attacks/2601.05742v1.pdf)
**Authors:** Ahmad Alobaid, Marti Jorda Roca, Carlos Castillo, Joan Vendrell (NeuralTrust; ICREA and UPF)
**Venue:** arXiv preprint 2601.05742, January 2026
**PDF:** https://arxiv.org/abs/2601.05742v1
**Accessed:** 2026-04-03

---

## Claim

The Echo Chamber attack exploits LLM consistency bias through a multi-turn, self-reinforcing loop where the model progressively elaborates on its own prior outputs. By planting suggestive seeds and steering the conversation toward a target objective, an attacker can bypass safety alignment without requiring model internals, achieving high success rates across major frontier models.

## Background

Large language models employ safety alignment (RLHF, system prompts, content filters) to refuse harmful requests. Single-turn jailbreaks are increasingly mitigated, but multi-turn attacks exploit conversational context to gradually erode refusal behaviour. Prior work such as Crescendo demonstrated escalation-based multi-turn attacks. Echo Chamber extends this line by leveraging the model's own generated content as the persuasion vector, exploiting the tendency of LLMs to remain consistent with previously generated text.

## Problem Statement

Current LLM safety mechanisms are primarily optimised against single-turn adversarial inputs and fail to account for multi-turn consistency bias. When a model generates content -- even mildly suggestive content -- it tends to continue in that direction across subsequent turns. No systematic attack framework existed to exploit this self-reinforcing property, nor had it been evaluated across multiple frontier models as a general vulnerability class.

## Research Questions

- Can LLM consistency bias be systematically exploited to bypass safety alignment through multi-turn self-reinforcement?
- How effective is the Echo Chamber attack across diverse frontier models?
- Can the attack be fully automated using an attacker LLM and an evaluator LLM?
- How does Echo Chamber compose with existing multi-turn attacks (e.g., Crescendo) for harder objectives?

## Theoretical Framework

- **Consistency bias:** LLMs trained via RLHF exhibit a tendency to maintain coherence with their own prior outputs, even when that trajectory drifts toward unsafe content.
- **Self-reinforcing loops:** Asking a model to elaborate, expand, or rephrase its own output creates a feedback loop that progressively deepens alignment with the attacker's objective.
- **Black-box threat model:** The attack requires only API-level access (prompt/response), no gradient access, logits, or model internals.
- **Compositional attack design:** Echo Chamber can be layered on top of other multi-turn techniques (e.g., Crescendo) for objectives that resist a single strategy.

## Methods

The Echo Chamber attack follows a 5-step pipeline:

1. **Poisonous Seeds** -- Plant suggestive but non-violating concepts related to the attack objective in early turns. These are framed innocuously to pass content filters while priming the model's context.
2. **Steering Seeds** -- Guide the model toward the desired output format or domain (e.g., "write a persuasive essay", "list arguments") without revealing the true objective.
3. **Invoking Poisonous Content** -- Request multiple answers or variations to increase the probability that at least one response contains an exploitable fragment that edges closer to the target.
4. **Path Selection** -- From the generated responses, select the one most aligned with the attack objective. This becomes the anchor for subsequent turns.
5. **Persuasion Cycle (Echo Chamber)** -- Iteratively ask the model to elaborate, expand, or deepen its own selected output. Each cycle reinforces the harmful direction, exploiting consistency bias until the model produces the target content.

**Manual evaluation:** Three harmful tasks (racist manifesto, anti-vaccine misinformation, Molotov cocktail instructions) tested on five frontier models. **Automated version:** An attacker LLM generates prompts following the 5-step strategy; an evaluator LLM scores whether the target model's response achieves the objective, enabling fully autonomous red teaming loops.

## Result

### Manual Attack Results (3 tasks x 5 models)

| Model | Racist Manifesto | Unsafe Vaccines | Molotov Cocktail |
|-------|:---:|:---:|:---:|
| DeepSeek R1 | Success | Success | Success |
| Qwen3 32B | Success | Success | Success |
| Gemini 2.5 Pro | Success | Success | Success |
| GPT-4.1 | Success | Success | Success |
| Grok 4 | Success | Success | Success |

- Near-universal success across all tested frontier models and task categories.
- The attack typically requires 5-15 conversational turns to reach full objective completion.
- Automated version achieves comparable results without human intervention.
- Composing Echo Chamber with Crescendo enables success on tasks where either attack alone fails.
- The self-reinforcing nature means later turns require less attacker effort as the model increasingly commits to the harmful trajectory.

## Gap (for HART)

- **No standardised scoring:** The paper uses binary success/failure; HART's multi-judge evaluation layer could provide graded severity assessment mapped to OWASP/MITRE ATLAS.
- **No defence evaluation:** The paper does not test against layered defences (e.g., conversation-level monitoring, turn-by-turn content scoring, consistency-drift detectors).
- **Limited automation framework:** The automated version is ad hoc; HART's plugin architecture could integrate Echo Chamber as a reusable Attack Layer plugin with configurable seed strategies.
- **No agentic AI scope:** Testing is limited to chat LLMs; Echo Chamber's self-reinforcing loop may be amplified in agentic systems where tool calls and memory persistence extend the attack surface.
- **No compositional attack taxonomy:** The paper mentions combining with Crescendo but lacks a systematic framework for composing multi-turn attacks -- a gap HART's plugin chaining could fill.

## HART Relevance

Echo Chamber directly validates HART's Attack Layer design (RQ1) by demonstrating a plugin-worthy multi-turn strategy exploiting consistency bias. Its 5-step pipeline maps cleanly to a HART attack plugin. The self-reinforcing loop pattern is relevant to RQ2 (agentic amplification via tool/memory persistence). The lack of defence evaluation (RQ3) and standardised scoring (RQ4) are gaps HART's Evaluation and Defence Layers are designed to address.
