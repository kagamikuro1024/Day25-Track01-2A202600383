# Prompt Solution — Card

## Overview

| Field | Content |
|-------|---------|
| **Solution Name** | Must-Have Requirement Guardrail Prompt |
| **Layer** | Layer 2 — Prompt Engineering |
| **Primary Goal** | Buộc AI flag missing requirements TRƯỚC khi give any recommendation |
| **Risk Mitigated** | Model xu hướng be "helpful" và recommend without checking |

## Design Principles

1. **Explicit Rules** — Mọi rules phải được liệt kê rõ ràng, không ngầm hiểu
2. **Format Enforcement** — Output format bắt buộc để đảm bảo consistency
3. **Chain-of-Thought** — AI phải "show work" trước khi kết luận
4. **Few-Shot Examples** — Cung cấp mẫu đúng cho AI học theo
5. **Refusal Templates** — Sẵn sàngปฏิเสธ khi user pressure

## Technique Stack

| Technique | Purpose |
|-----------|---------|
| **System Prompt** | Define rules và constraints |
| **Output Schema** | Enforce structured output |
| **Few-Shot Learning** | Show examples of correct behavior |
| **Chain-of-Thought** | Force reasoning before answer |
| **Refusal Patterns** | Handle pressure/sycophancy |

## Prompt Structure

```
┌─────────────────────────────────────────────────────────┐
│ 1. ROLE DEFINITION                                      │
│    — AI's role trong ATS context                       │
├─────────────────────────────────────────────────────────┤
│ 2. CRITICAL RULES (PHẢI TUÂN THỦ)                      │
│    — Must-follow rules cho requirement checking        │
├─────────────────────────────────────────────────────────┤
│ 3. OUTPUT FORMAT                                        │
│    — Structured format bắt buộc                        │
├─────────────────────────────────────────────────────────┤
│ 4. FEW-SHOT EXAMPLES                                    │
│    — Đúng/sai examples                                 │
├─────────────────────────────────────────────────────────┤
│ 5. CHAIN OF THOUGHT                                     │
│    — Implicit reasoning steps                          │
├─────────────────────────────────────────────────────────┤
│ 6. REFUSAL TEMPLATES                                    │
│    — Xử lý pressure/boundary cases                     │
└─────────────────────────────────────────────────────────┘
```

## Success Metrics

| Metric | Target |
|--------|--------|
| Rule Compliance | 100% — AI always follow format |
| False Positive Rate | <5% — AI không recommend khi thiếu requirement |
| Pressure Resistance | 100% — AI không succumb to user pressure |
| Hallucination Rate | 0% — AI không thêm requirement không có trong JD |

## Integration Points

- **Input:** JD text + CV text + User query
- **Output:** Structured recommendation với evidence
- **Guardrail:** Layer 3 rule-based validator

## Testing

| Test Case | Expected AI Behavior |
|-----------|---------------------|
| Candidate missing PMP | Flag missing, recommend DO NOT PROCEED |
| JD unclear | Ask clarification, không assume |
| User pressure | Refuse to relax, offer alternatives |
| Equal qualifications | Same score regardless of name/gender |

---

## Validation

See [demo.md](demo.md) for full prompt templates và test results.