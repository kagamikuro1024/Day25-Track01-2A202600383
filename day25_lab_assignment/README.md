# Day 25 — Track 7: Trợ lý sàng lọc CV và tuyển dụng

## Thành viên nhóm

| # | Mã học viên | Họ tên đầy đủ |
|---|-------------|---------------|
| 1 | A20-2600383 | Lê Văn Quang Trung |

---

## Kết quả cuối

- 🎯 [Bộ kiểm thử cuối](./worksheet/01-test-set-review/3-FINAL-test-set-eval-plan.md)
- 🎯 [Thiết kế 3 lớp giải pháp](./worksheet/02-solution-design/1-map-and-format.md) + [artifact/](./worksheet/02-solution-design/artifact/)

---

## Bài 1 — Rà bộ kiểm thử

### Mục tiêu
Chọn ra 10-15 tình huống đáng kiểm thử nhất và viết kế hoạch chấm rõ ràng.

### Kết quả
- **14 test cases** với đủ các loại: Normal, Critical, Edge, Pressure trap, Escalation
- **Risk Score** từ 4-25 (P0-P3 priorities)
- **Eval Plan** với Pass/Fail/Unclear criteria chi tiết

### File liên quan
- [1-diverge.md](./worksheet/01-test-set-review/1-diverge.md) — Giai đoạn Mở rộng (sự cố thật + brainstorm)
- [2-converge.md](./worksheet/01-test-set-review/2-converge.md) — Giai đoạn Hội tụ (gộp, lọc trùng, chấm rủi ro)
- [3-FINAL-test-set-eval-plan.md](./worksheet/01-test-set-review/3-FINAL-test-set-eval-plan.md) — **Bộ kiểm thử cuối**

---

## Bài 2 — Thiết kế giải pháp

### Rủi ro được chọn
**Missing Must-Have Requirements** (Risk Score: 25/25 — P0 Critical)

**Failure pattern:**
> Khi recruiter hỏi suitability của candidate cho vị trí có must-have requirements, AI có xu hướng recommend dựa trên partial match thay vì flag missing requirements, gây rejection oan cho ứng viên và legal risk cho công ty.

### Ba lớp giải pháp

| Lớp | Thư mục | Mục đích |
|-----|---------|----------|
| **Giao diện (UI/UX)** | [artifact/1-uiux/](./worksheet/02-solution-design/artifact/1-uiux/) | Warning banner, requirement checklist, escalation buttons |
| **Chỉ dẫn AI (Prompt)** | [artifact/2-prompt/](./worksheet/02-solution-design/artifact/2-prompt/) | System prompt với guardrail rules, refusal templates |
| **Kiến trúc (Architecture)** | [artifact/3-architecture/](./worksheet/02-solution-design/artifact/3-architecture/) | Rule-based validator, output guardrail, human escalation |

### File liên quan
- [1-map-and-format.md](./worksheet/02-solution-design/1-map-and-format.md) — **Thiết kế 3 lớp giải pháp**
- [artifact/1-uiux/card.md](./worksheet/02-solution-design/artifact/1-uiux/card.md) — UI/UX design card
- [artifact/1-uiux/demo.md](./worksheet/02-solution-design/artifact/1-uiux/demo.md) — UI/UX wireframes
- [artifact/2-prompt/card.md](./worksheet/02-solution-design/artifact/2-prompt/card.md) — Prompt engineering card
- [artifact/2-prompt/demo.md](./worksheet/02-solution-design/artifact/2-prompt/demo.md) — System prompt templates
- [artifact/3-architecture/card.md](./worksheet/02-solution-design/artifact/3-architecture/card.md) — Architecture design card
- [artifact/3-architecture/demo.md](./worksheet/02-solution-design/artifact/3-architecture/demo.md) — System diagrams & code

---

## Root Cause Analysis

**5 Whys conclusion:**

AI được thiết kế để be "helpful assistant" — ưu tiên positive recommendations over conservative requirement checking. Thiếu explicit, deterministic guardrail layer để enforce must-have requirement validation.

---

## Success Metrics

| Metric | Target |
|--------|--------|
| Overall Pass Rate | ≥90% |
| Critical Cases (T1-T4) Pass Rate | 100% |
| No Bias Demonstrated | 100% |
| No Hallucination | 100% |
| Escalation SLA | <4h |

---

## References

- Air Canada chatbot case: Moffatt v. Air Canada, 2024 BCCRT 149
- COMPAS bias case: ProPublica, May 2016
- Amazon AI recruiting bias: Bloomberg/TechCrunch 2018
- HireVue bias concerns: WSJ, MIT Tech Review 2020-2023
- Chip Huyen, AI Engineering, Chapter 4 (O'Reilly 2024)

---

## Checklist trước khi nộp

- [x] `worksheet/00-context.md` đã điền đủ
- [x] `1-diverge.md` có đủ Phần A, B, C
- [x] `2-converge.md` có bảng gộp, bảng lọc trùng, bảng chấm rủi ro
- [x] `3-FINAL-test-set-eval-plan.md` có 14 cases và kế hoạch chấm
- [x] `1-map-and-format.md` có rủi ro được chọn, nguyên nhân gốc, 3 lớp giải pháp
- [x] `artifact/1-uiux/` có card.md và demo.md
- [x] `artifact/2-prompt/` có card.md và demo.md
- [x] `artifact/3-architecture/` có card.md và demo.md
- [x] Repo công khai, tên đúng cú pháp
- [x] README có bảng thành viên

---