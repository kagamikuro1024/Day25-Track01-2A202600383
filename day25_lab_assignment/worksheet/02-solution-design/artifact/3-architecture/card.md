# Architecture Solution — Card

## Overview

| Field | Content |
|-------|---------|
| **Solution Name** | RAG Validation + Rule-Based Guardrail Architecture |
| **Layer** | Layer 3 — System Architecture |
| **Primary Goal** | Đảm bảo AI tra cứu đúng nguồn và có phương án dự phòng |
| **Risk Mitigated** | AI hallucination, model drift, systematic failure |

## Architecture Principles

1. **Deterministic First** — Rule-based validation BEFORE AI
2. **Defense in Depth** — Multiple guardrail layers
3. **Fail Safe** — Default to rejection when uncertain
4. **Human-in-Loop** — Escalation path for edge cases
5. **Audit Trail** — Log all decisions for compliance

## System Components

| Component | Purpose | Technology |
|-----------|---------|------------|
| Input Validator | Check JD/CV presence and format | Node.js/Python |
| JD Parser | Extract structured requirements from JD | Rule-based + LLM |
| CV Parser | Extract candidate qualifications | LLM |
| Rule Validator | Deterministic requirement matching | Rule engine |
| AI Assistant | Generate recommendations | LLM (with guardrails) |
| Output Guardrail | Final validation before user | Rule-based |
| Escalation Service | Human review workflow | Ticketing system |

## Data Flow

```
User Request → Input Validation → JD Parse → CV Parse → Rule Validation → AI (if needed) → Output Guardrail → User Response
```

## Failure Handling

| Failure Mode | Fallback |
|--------------|----------|
| JD parse fails | Ask user to manually specify requirements |
| CV parse uncertain | Escalate to human review |
| Rule validation FAIL | Block positive recommendation |
| AI assistant fails | Use templated response |
| Output conflicts | Block AI, use rule result |

## SLA Requirements

| Component | SLA |
|-----------|-----|
| Input validation | <100ms |
| JD/CV parsing | <2s |
| Rule validation | <500ms |
| AI response | <5s |
| Human escalation | <4h |

## Compliance

- All decisions logged with timestamps
- Evidence preserved for audit
- Bias detection monitoring
- GDPR/privacy compliant

---

See [demo.md](demo.md) for detailed architecture diagrams and flow charts.