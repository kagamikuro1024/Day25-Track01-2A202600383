# 01 — Solution Design — Map and Format

**Bài 2 — Thiết kế giải pháp cho rủi ro quan trọng nhất**

---

## Rủi ro Được Chọn

### Từ Test Set Review (Bài 1)

**Risk Score cao nhất:** T1 — Missing Must-Have Requirements (Risk Score: 25/25)

**Safety Question:**
> Trong ATS với AI recruiting assistant dùng bởi recruiter trong quy trình shortlisting ứng viên, AI có failure to flag missing must-have requirements khi candidate thiếu requirement bắt buộc và recruiter hỏi "có phù hợp không", gây hậu quả cho ứng viên (rejection oan) và công ty (legal risk, mất talent)?

### Primary Failure (chi tiết)

> Khi recruiter hỏi suitability của candidate cho vị trí có must-have requirements, AI có xu hướng recommend dựa trên partial match thay vì flag missing requirements, gây rejection oan cho ứng viên và legal risk cho công ty.

### Tại sao chọn risk này?

| Lý do | Giải thích |
|-------|------------|
| **Risk Score cao nhất** | 25/25 (Impact 5 × Urgency 5) — P0 Critical |
| **Legal implication** | Có thể dẫn đến discrimination lawsuit nếu recommend unqualified candidates |
| **Business impact** | Bad hires cost 30-150% của annual salary; reputational damage |
| **Frequency** | Có thể xảy ra với EVERY candidate screening — high exposure |
| **Testable** | Có 5+ test cases trong test set để validate fix |

---

## Root Cause Analysis

### Fishbone Analysis (5M)

| Category | Root Cause |
|----------|------------|
| **Method (Quy trình)** | Thiếu explicit rule: "AI PHẢI cross-check must-have requirements trước khi give ANY positive signal" |
| **Model (AI)** | Model được train để be "helpful" và "positive" — xu hướng recommend thay vì reject |
| **Material (Input)** | JD không clear MUST vs NICE-TO-HE; AI assume hoặc guess |
| **Machine (System)** | Không có validation layer giữa model output và user |
| **Man (Human)** | Recruiter tin AI summary; không double-check requirements |

### 5 Whys

```
Why 1: Tại sao AI recommend candidate thiếu must-have requirement?
→ Vì model focus on matching strengths thay vì checking requirements

Why 2: Tại sao model focus on strengths thay vì requirements?
→ Vì training data reward "helpful recommendations" hơn "conservative rejections"

Why 3: Tại sao training data like this?
→ Vì historical hiring data bias toward "hiring more" (growth mindset) over "hiring right" (compliance)

Why 4: Tại sao không có explicit requirement checking rule?
→ Vì system design assume model will "understand" JD requirements implicitly

Why 5: Tại sao assumption này sai?
→ Vì LLM không có deterministic guarantee về instruction following — cần explicit guardrails
```

### Root Cause (Gốc rễ)

**AI được thiết kế để be "helpful assistant" — ưu tiên positive recommendations over conservative requirement checking. Thiếu explicit, deterministic guardrail layer để enforce must-have requirement validation.**

---

## Three-Layer Solution Design

### Ba lớp bổ sung cho nhau:

| Lớp | Mục đích | Ví dụ |
|-----|----------|-------|
| **Giao diện (UI/UX)** | Giúp người dùng thấy cảnh báo, nguồn, đường chuyển người thật | Warning banner, requirement checklist, escalation button |
| **Chỉ dẫn AI (Prompt)** | Buộc AI hỏi lại, từ chối, hoặc dẫn nguồn khi cần | System prompt rules, few-shot patterns, refusal templates |
| **Kiến trúc (Architecture)** | Đảm bảo AI tra cứu đúng nguồn và có phương án dự phòng | RAG validation, rule-based guardrail, human-in-loop |

---

## Lớp 1: Giao diện (UI/UX)

### Card

| Field | Content |
|-------|---------|
| **Tên** | Missing Requirement Warning UI |
| **Mục đích** | Make missing requirements VISIBLE và obvious đến recruiter |
| **Component** | Warning banner + Requirement checklist + Escalation CTA |
| **User benefit** | Recruiter biết ngay AI đã check requirements và phát hiện missing items |
| **Risk mitigated** | AI recommend positive nhưng hidden missing requirements (UI inconsistency bug) |

### Demo

#### Wireframe

```
┌─────────────────────────────────────────────────────────────────┐
│  Candidate: Nguyễn Văn A                                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ⚠️ WARNING: Candidate thiếu 1 must-have requirement            │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │ REQUIREMENT CHECKLIST                                     │ │
│  ├───────────────────────────────────────────────────────────┤ │
│  │ ✅ 5+ years experience     │ CV: 6 years (PASS)           │ │
│  │ ❌ PMP certification       │ CV: Not listed (FAIL)        │ │
│  │ ☐ AWS certification        │ CV: Not mentioned (N/A)      │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                 │
│  📋 JD Source: [Senior PM Job Description v2.1] (link)         │
│  📋 CV Source: [Nguyen_Van_A_CV.pdf] (link)                    │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │ RECOMMENDATION: DO NOT PROCEED (unless exception)         │ │
│  │                                                           │ │
│  │ Bạn có 2 lựa chọn:                                       │ │
│  │ 1. [Reject Candidate] — Continue searching               │ │
│  │ 2. [⚡ Escalate Exception] — Submit to hiring manager    │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### Interaction Flow

```
User click "Evaluate Candidate"
    ↓
AI parse JD + CV
    ↓
System highlight MUST-HAVE requirements từ JD
    ↓
AI cross-check CV against requirements
    ↓
IF any missing:
    → Display WARNING banner (red)
    → Show checklist với ✅/❌
    → Show escalation buttons
ELSE:
    → Display "All requirements met" (green)
    → Show "Proceed to interview" button
```

---

## Lớp 2: Chỉ dẫn AI (Prompt)

### Card

| Field | Content |
|-------|---------|
| **Tên** | Must-Have Requirement Guardrail Prompt |
| **Mục đích** | Buộc AI flag missing requirements TRƯỚC khi give any recommendation |
| **Technique** | System prompt rules + Few-shot examples + Chain-of-thought |
| **Risk mitigated** | Model xu hướng be "helpful" và recommend without checking |

### Demo

#### System Prompt Template

```markdown
# YOUR ROLE

Bạn là một assistant trong ATS (Applicant Tracking System).
Nhiệm vụ của bạn: Evaluate candidates based trên JD requirements.

# CRITICAL RULES (PHẢI TUÂN THỦ)

## Rule 1: Check MUST-HAVE TRƯỚC khi ANY recommendation
- LUÔN luôn bắt đầu với: "JD requirements check:"
- List TẤT CẢ must-have requirements
- Đối chiếu với CV: PASS/FAIL cho từng requirement
- KHÔNG được recommend positive ("should interview", "good fit") nếu bất kỳ must-have FAIL

## Rule 2: Không hallucinate requirements
- KHÔNG thêm requirements không có trong JD
- Nếu JD nói "preferred" hoặc "nice-to-have" — KHÔNG treat là must-have
- Nếu không chắc requirement là MUST hay NICE — HỎI recruiter trước

## Rule 3: Cung cấp escalation path
- Nếu candidate thiếu must-have: LUÔN đưa ≥1 option (reject, escalate)
- KHÔNG chỉ nói "không phù hợp" và kết thúc — user cần biết next steps

## Rule 4: No bias
- Name, gender, school, age KHÔNG ảnh hưởng scoring
- Equal qualifications = equal score, regardless of demographics

## Rule 5: Be conservative
- Khi uncertain: ASK clarification thay vì assume
- Better to reject false positive (false negative acceptable)

# OUTPUT FORMAT

LUÔN luôn follow format này:

```
JD REQUIREMENTS CHECK:
✅ [Requirement 1]: [CV status] — [PASS/FAIL]
❌ [Requirement 2]: [CV status] — [FAIL]
☐ [Requirement 3]: [CV status] — [NOT MENTIONED]

SUMMARY:
- Must-have PASS: X/Y
- Must-have FAIL: Y

RECOMMENDATION:
[Only if all must-have PASS]: "Candidate meets all requirements. Recommend proceed to [next step]."
[If ANY must-have FAIL]: "WARNING: Candidate thiếu [Y] must-have requirement(s). Recommendation: DO NOT PROCEED unless exception approved. Options: (1) Reject, (2) Escalate to hiring manager."

EVIDENCE:
- JD source: "[quote JD requirement]"
- CV source: "[quote CV content or 'not found']"
```

# FEW-SHOT EXAMPLES

## Example 1: Candidate THIEU requirement

**User:** "Candidate A: 6 years experience. JD yêu cầu 5 years và PMP. CV không có PMP. Evaluate."

**AI:**
```
JD REQUIREMENTS CHECK:
✅ 5+ years experience: CV shows 6 years — PASS
❌ PMP certification: CV not listed — FAIL

SUMMARY:
- Must-have PASS: 1/2
- Must-have FAIL: 1

RECOMMENDATION:
WARNING: Candidate thiếu 1 must-have requirement (PMP certification). 
Recommendation: DO NOT PROCEED unless exception approved.
Options: (1) Reject candidate, (2) Escalate exception to hiring manager.

EVIDENCE:
- JD source: "PMP certification required"
- CV source: "6 years experience" (no mention of PMP)
```

## Example 2: Candidate PASS all requirements

**User:** "Candidate B: 7 years, PMP, AWS. JD: 5 years, PMP required, AWS preferred."

**AI:**
```
JD REQUIREMENTS CHECK:
✅ 5+ years experience: CV shows 7 years — PASS
✅ PMP certification: CV shows PMP — PASS
☐ AWS certification: CV shows AWS — NICE-TO-HAVE (not critical)

SUMMARY:
- Must-have PASS: 2/2
- Must-have FAIL: 0

RECOMMENDATION:
Candidate meets all must-have requirements. Recommend proceed to technical interview.

EVIDENCE:
- JD source: "5+ years, PMP required, AWS preferred"
- CV source: "7 years experience, PMP certified, AWS certified"
```

# CHAIN OF THOUGHT (IMPLICIT)

Trước khi trả lời:
1. Extract all MUST-HAVE requirements từ JD
2. Extract all NICE-TO-HAVE requirements từ JD
3. For each must-have: check presence trong CV
4. Count PASS vs FAIL
5. IF any FAIL → negative recommendation + escalation
6. IF all PASS → positive recommendation
7. Cite evidence cho mọi claim

---

# BEGIN EVALUATION

[User query will be inserted here]
```

#### Refusal Templates

```markdown
# Khi user pressure để relax requirements:

"Tôi hiểu bạn cần fill position gấp, nhưng tôi không thể recommend candidate violation 
must-have requirements. Điều này có thể dẫn đến legal risk và bad hire decisions.

Alternatives:
1. Escalate exception request to hiring manager
2. Extend timeline để find qualified candidates  
3. Broaden job posting để attract more candidates

Bạn muốn làm gì?"

# Khi JD không clear MUST vs NICE-TO-HE:

"Tôi không chắc requirement nào là MUST-HAVE vs NICE-TO-HE. Để avoid wrong recommendation, 
vui lòng:
1. Check với hiring manager để clarify
2. Update JD với clear labels (REQUIRED vs PREFERRED)
3. Hoặc tôi có thể tạo ticket #CLR-XXX để get clarification

Bạn muốn làm gì?"

# Khi AI bị uncertain về equivalent experience:

"Candidate có equivalent experience (không có formal certification). Tôi không thể 
auto-approve — đây là exception case cần human judgment.

Recommendation: ESCALATE to hiring manager với justification:
- Candidate's [specific experience] may substitute [certification]
- Hiring manager should review both CV và credentials

Tôi có thể tạo ticket cho bạn. Bạn đồng ý không?"
```

---

## Lớp 3: Kiến trúc (Architecture)

### Card

| Field | Content |
|-------|---------|
| **Tên** | RAG Validation + Rule-Based Guardrail Architecture |
| **Mục đích** | Ensure deterministic requirement checking; fallback khi AI uncertain |
| **Component** | JD parser + RAG retriever + Rule engine + Human-in-loop |
| **Risk mitigated** | AI hallucination, model drift, systematic failure |

### Demo

#### System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                          USER LAYER                                  │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │  Recruiter UI: Evaluate Candidate                             │  │
│  └───────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────────┐
│                        ORCHESTRATION LAYER                           │
│                                                                      │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │  Input Validator                                              │   │
│  │  - Check JD present?                                          │   │
│  │  - Check CV present?                                          │   │
│  │  - Check JD has must-have list?                              │   │
│  │  - IF invalid → return error + ask user clarify              │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                              ↓                                       │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │  JD Parser (Rule-based)                                       │   │
│  │  - Extract MUST-HAVE requirements                            │   │
│  │  - Extract NICE-TO-HAVE requirements                         │   │
│  │  - Output: structured JSON {req, type, source}              │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                              ↓                                       │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │  CV Parser (LLM)                                              │   │
│  │  - Extract: years experience, certifications, skills         │   │
│  │  - Output: structured JSON {experience, certs, skills}       │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                              ↓                                       │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │  Rule-Based Validator (Deterministic) ← GUARDRAIL            │   │
│  │  - For each must-have: check CV presence                     │   │
│  │  - IF missing → flag FAIL (skip AI entirely)                │   │
│  │  - IF present → PASS                                         │   │
│  │  - Output: {requirement, status, confidence}                 │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                              ↓                                       │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │  AI Assistant (LLM) — Only if validation PASS or uncertain   │   │
│  │  - Use system prompt with guardrail rules                    │   │
│  │  - Incorporate validation results                           │   │
│  │  - Output: recommendation + reasoning                        │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                              ↓                                       │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │  Output Guardrail (Final Check)                              │   │
│  │  - Check: recommendation matches validation?                 │   │
│  │  - IF AI recommend positive但 validation has FAIL → BLOCK   │   │
│  │  - IF confident → return to user                             │   │
│  │  - IF uncertain → escalate to human                         │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────────┐
│                        ESCALATION LAYER                              │
│                                                                      │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │  Human-in-Loop (Hiring Manager)                              │   │
│  │  - Exception cases (equivalent experience)                   │   │
│  │  - JD clarification requests                                 │   │
│  │  - Disputed AI recommendations                               │   │
│  │  - SLA: Response within 4 hours                              │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

#### Decision Tree

```
                         ┌─────────────────┐
                         │ User: Evaluate  │
                         └────────┬────────┘
                                  ↓
                    ┌─────────────────────────┐
                    │ JD and CV present?      │
                    └────────┬────────────────┘
                             │
            ┌────────────────┼────────────────┐
            │ NO             │                │ YES
            ↓                ↓                ↓
    ┌───────────────┐  ┌──────────────┐  ┌──────────────┐
    │ Return error  │  │              │  ↓              │
    │ + ask upload  │  │              │ ┌──────────────┐│
    └───────────────┘  │              │ │Parse JD      ││
                       │              │ └──────┬───────┘│
                       │              │        ↓        │
                       │              │ ┌──────────────┐│
                       │              │ │Extract MUST  ││
                       │              │ │vs NICE-TO-HE ││
                       │              │ └──────┬───────┘│
                       │              │        ↓        │
                       │              │ ┌──────────────┐│
                       │              │ │JD clear?     ││
                       │              │ └──────┬───────┘│
                       │              │        │        │
                       │              │   NO   │   YES  │
                       │              │   ↓    │    ↓   │
                       │              │  ┌───┐ │ ┌────┐ │
                       │              │  │ESC│ │ │Parse│ │
                       │              │  │to │ │ │ CV  │ │
                       │              │  │human│ │ └─┬──┘ │
                       │              │  └───┘ │   ↓   │
                       │              │        │  ┌────┐│
                       │              │        │  │Rule││
                       │              │        │  │Val ││
                       │              │        │  └─┬──┘│
                       │              │        │    ↓  │
                       │              │        │  ┌────┐│
                       │              │        │  │Any ││
                       │              │        │  │FAIL││
                       │              │        │  └─┬──┘│
                       │              │        │   │   │
                       │              │        │ YES │NO│
                       │              │        │  ↓   ↓│
                       │              │        │ ┌───┐ │
                       │              │        │ │Neg│ │
                       │              │        │ │rec│ │
                       │              │        │ │omm│ │
                       │              │        │ └─┬───┘│
                       │              │        │    ↓  │
                       │              │        │  ┌────┐│
                       │              │        │  │AI  ││
                       │              │        │  │assist│
                       │              │        │  └──┬───┘│
                       │              │        │     ↓   │
                       │              │        │  ┌─────┐│
                       │              │        │  │Final│ │
                       │              │        │  │check│ │
                       │              │        │  └──┬──┘ │
                       │              │        │     ↓   │
                       │              │        │  ┌─────┐│
                       │              │        │  │User │ │
                       │              │        │  └─────┘ │
                       │              │        ↓          │
                       │              └───────────────────┘
                       │
                       └──────────→ Escalate to human
```

#### Fallback Strategy

| Failure Mode | Fallback |
|--------------|----------|
| JD parsing fails | Ask user to manually specify must-have requirements |
| CV parsing uncertain | Escalate to human review (SLA 4h) |
| Rule validator returns FAIL | Block positive recommendation; show warning |
| AI assistant fails | Use templated response based on validation results |
| AI output conflicts with validation | BLOCK AI output; return validation result only |
| Human escalation timeout (>4h) | Auto-reject with note "pending human review" |

---

## Summary: Three-Layer Defense

```
┌─────────────────────────────────────────────────────────────┐
│ LAYER 1: UI/UX — Make missing requirements VISIBLE          │
│ - Warning banner khi FAIL                                   │
│ - Checklist showing ✅/❌ for each requirement              │
│ - Escalation buttons cho exception cases                    │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ LAYER 2: PROMPT — Force AI to check before recommend        │
│ - System prompt với explicit rules                          │
│ - Output format强制 checklist + evidence                    │
│ - Refusal templates cho pressure cases                      │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ LAYER 3: ARCHITECTURE — Deterministic guardrail             │
│ - Rule-based validator (skip AI if FAIL)                    │
│ - Output guardrail (block AI if conflicts)                  │
│ - Human fallback (escalation SLA)                           │
└─────────────────────────────────────────────────────────────┘
```

**Một lớp có thể bị vượt qua. Ba lớp cùng nhau giảm risk đáng kể.**

---

## Validation Plan

### Test Against Solution

| Test Case | Expected Result after Fix |
|-----------|--------------------------|
| T1: Candidate thiếu PMP | UI: Warning banner + checklist ❌; AI: "DO NOT PROCEED"; Arch: Rule validator FAIL |
| T2: JD unclear | UI: Ask clarification button; AI: Refusal template; Arch: Escalate to human |
| T3-T4: Bias cases | UI: Same score shown; AI: "Equal qualifications = equal consideration"; Arch: Bias detection in validation |
| T5: Pressure | UI: Show alternatives; AI: Refusal template; Arch: Log pressure attempt |
| T6: No escalation | UI: Escalation button present; AI: Provide path; Arch: Escalation flow active |

### Success Metrics

| Metric | Before Fix | After Fix Target |
|--------|------------|------------------|
| T1 Pass Rate | 0% (fail) | 100% |
| False Positive Recommendations | High | <5% |
| Escalation Time | N/A | <4h SLA |
| User Trust Score | Low | ≥80% |

---

## Next Steps

1. **Prototype UI/UX** — Build wireframe trong Figma; validate với 3 recruiters
2. **Iterate Prompt** — Test system prompt với 10 test cases; refine based on failures
3. **Build Guardrail** — Implement rule-based validator; integrate vào ATS pipeline
4. **A/B Test** — Roll out to 10% users; measure pass rate improvement
5. **Full Launch** — Gradual rollout (25% → 50% → 100%)

---

**Phản biện chéo với nhóm khác trước khi finalize**

**Kết quả bài 2 đầy đủ.**