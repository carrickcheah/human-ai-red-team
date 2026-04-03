# Lessons From Red Teaming 100 Generative AI Products

**Source:** [papers/2025/2501.07238v1.pdf](../2025/2501.07238v1.pdf)
**Authors:** Blake Bullwinkel, Amanda Minnich, Shiven Chawla, Gary Lopez, Martin Pouliot, Whitney Maxwell, Joris de Gruyter, Katherine Pratt, Saphir Qi, Nina Chikanov, Roman Lutz, Raja Sekhar Rao Dheekonda, Bolor-Erdene Jagdagdorj, Eugenia Kim, Justin Song, Keegan Hines, Daniel Jones, Giorgio Severi, Richard Lundeen, Sam Vaughan, Victoria Westerhoff, Pete Bryan, Ram Shankar Siva Kumar, Yonatan Zunger, Chang Kawaguchi, Mark Russinovich (Microsoft)
**Venue:** arXiv preprint 2501.07238, January 2025
**PDF:** [https://arxiv.org/pdf/2501.07238](https://arxiv.org/pdf/2501.07238)
**Accessed:** 2026-04-03

---

## Claim

Breaking AI systems does not require gradient computation or sophisticated ML attacks. Based on 80+ operations red teaming 100+ GenAI products at Microsoft since 2021, simple prompt-based techniques and system-level exploitation are more effective in practice than academic adversarial ML methods. The paper distills 8 operational lessons and a threat model ontology for AI red teaming.

## Background and Rationale

Microsoft's AI Red Team (AIRT) has operated since 2018, scaling from classical ML security to GenAI products including copilots, plugins, apps, and models. The shift from standalone models to agentic systems with tool access has massively expanded the attack surface. Existing red teaming efforts lack standardization and often confuse red teaming with safety benchmarking, missing system-level and real-world context.

## Problem Statement

AI red teaming is a nascent practice with many open questions. Current approaches often focus narrowly on model-level benchmarks rather than end-to-end system attacks. Red teams lack a shared ontology for modeling threats, and there is no consensus on how to balance automation with human creativity, safety harms with security vulnerabilities, or reproducibility with exploratory testing.

## Research Questions

- How should AI red teaming operations be structured to find real-world vulnerabilities?
- What threat model ontology captures both safety and security risks in GenAI systems?
- When should red teams use automation vs. human operators?
- How do traditional security vulnerabilities interact with AI-specific weaknesses?
- What distinguishes AI red teaming from safety benchmarking?

## Theoretical Framework / Methodology

- **AIRT threat model ontology:** Actor → Attack → (leverages TTPs, exploits Weakness) → Impact → occurs in System, mitigated by Mitigation
- **TTPs mapped to MITRE ATT&CK and MITRE ATLAS**
- **Dual-scope:** Both safety (RAI harms) and security (data exfiltration, RCE, privilege escalation)
- **Qualitative lessons-learned approach** from 80+ operations across 100+ products
- **5 case studies** demonstrating ontology application across different attack types

## Methods

- **Scope:** 80+ red teaming operations across 100+ GenAI products at Microsoft (2021-2024). Products include models (24%), copilots (15%), plugins (16%), and apps/features (45%).
- **Dual focus:** Operations probe both safety (RAI) and security. Safety-focused ops increased from ~20% (2021) to ~80% (2024); security ops remain consistent.
- **Ontology components:** System (target product), Actor (adversarial or benign user), TTPs (tactics/techniques/procedures mapped to MITRE), Weakness (vulnerability exploited), Impact (downstream harm).
- **5 case studies:** (1) VLM image jailbreak — malicious instructions overlaid on images bypass text-only safety training, (2) LLM-powered automated scam — jailbroken LLM + TTS creates end-to-end phone scam system, (3) Chatbot distress response — testing how chatbots handle users in mental health crisis, (4) Text-to-image gender bias — systematic bias probing with ungendered prompts, (5) SSRF in video-processing GenAI app — traditional web vulnerability in AI infrastructure (outdated FFmpeg).
- **Automation:** PyRIT (Python Risk Identification Tool) used for automated attack strategies including TAP, PAIR, Crescendo, prompt converters, and multimodal scorers.

## Result

| Lesson | Key Finding |
| ------ | ----------- |
| 1. Understand the system | Start from downstream impact, not attack strategies. Consider both model capabilities and deployment context. |
| 2. No gradients needed | Simple prompts and multi-turn techniques (Crescendo, Skeleton Key) work as well as or better than gradient-based methods. Real attackers prompt engineer, not compute gradients. |
| 3. Not safety benchmarking | Red teaming discovers novel harm categories and system-level vulnerabilities that static benchmarks miss. Complementary but distinct practices. |
| 4. Automation helps scale | PyRIT enables testing at scale, but is a tool — not a replacement for human judgment. |
| 5. Human element is crucial | Subject matter expertise, cultural competence, and emotional intelligence cannot be automated. LLM-as-judge fails on specialized domains (medicine, CBRN, cybersecurity). |
| 6. RAI harms are pervasive but hard to measure | Safety harms are subjective, probabilistic, and context-dependent — unlike reproducible security bugs. Both adversarial and benign users can trigger them. |
| 7. LLMs amplify existing security risks | Traditional vulnerabilities (SSRF, credential leaks, side channels) persist in AI systems. RAG/XPIA introduces new model-level weaknesses. Both must be tested. |
| 8. Never complete | AI security follows economics of cybersecurity — goal is to raise attack cost, not achieve perfect security. Break-fix cycles and regulation are necessary. |

- **Key insight:** Prompt injections today are like buffer overflows of the early 2000s — will be mitigated through defense-in-depth, never fully eliminated
- **Open questions:** How to probe for dangerous capabilities (persuasion, deception)? How to adapt red teaming across languages/cultures? How to standardize practices and findings?

## Gap

1. **No quantitative metrics.** Paper is entirely qualitative — no ASR numbers, no comparative benchmarks, no statistical analysis of attack effectiveness.
2. **Microsoft-centric.** All experience from one organization's red team. May not generalize to smaller teams, different industries, or non-Microsoft products.
3. **No formal framework for multi-turn attacks.** Mentions Crescendo but doesn't systematize multi-turn escalation patterns or provide a taxonomy.
4. **No tool-chain exploitation methodology.** Acknowledges agentic systems expand attack surface but doesn't provide structured approach for testing tool abuse.
5. **No cross-tenant testing.** Multi-tenant AI security not discussed despite Microsoft operating shared AI infrastructure.
6. **PyRIT described at high level only.** No detailed architecture, no performance benchmarks, no comparison with other red teaming frameworks.
7. **Defense recommendations are generic.** "Raise the cost of attacks" and "break-fix cycles" without concrete, implementable defense architectures.

## HART Relevance

Directly validates HART's hybrid human-AI approach (Lesson 5: humans crucial + Lesson 4: automation scales). The AIRT ontology (Actor→Attack→Weakness→Impact) is a simpler precursor to HART's 4-layer architecture. Their open question on standardization (Section 3, Q3) is exactly what HART aims to answer. Key citation for RQ4 — the largest operational red teaming report to date, from the team that built PyRIT.
