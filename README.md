# HART — Human-AI Red Teaming

An open-source framework for red teaming agentic AI systems. Multi-turn attacks, tool-chain exploitation, and cross-tenant leakage testing — with a standardised scoring system that complements existing standards like OWASP and MITRE ATLAS.

## Architecture

```
Attack Layer    →  Plugin-based attack strategies (Crescendo, GOAT, GALA, GCG, TAP, MCP exploitation)
Target Layer    →  Connectors to any AI agent (HTTP API, MCP, function calling)
Evaluation Layer →  Multi-judge grading, OWASP + MITRE ATLAS severity mapping, HART score
Defence Layer   →  Tiered threat response (L0-L4), tool-gating, memory isolation
```

## Stack

- **Core API + Plugins:** TypeScript + Bun + Hono
- **ML Attack Engine:** Python + uv (PyTorch, HuggingFace)
- **Bridge:** HTTP/gRPC between TS and Python

## Research

PhD project at University of Malaya — "Human-AI Red Teaming for Agentic AI Security."

| RQ | Question |
|----|----------|
| RQ1 | Taxonomy of multi-turn adversarial attacks on agentic AI |
| RQ2 | Tool-chain exploitation (prompt injection to RCE) |
| RQ3 | Cross-tenant data leakage in multi-tenant AI deployments |
| RQ4 | Hybrid human-AI red teaming framework design |

## Status

Research phase — literature review and attack taxonomy development.

## License

[Apache 2.0](LICENSE)
