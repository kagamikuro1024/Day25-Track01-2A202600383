# UI/UX Solution — Demo

## Wireframe 1: Candidate Evaluation Result (Missing Requirement)

```
┌──────────────────────────────────────────────────────────────────────────┐
│  ← Back to Search         Candidate: Nguyễn Văn A               [⋮]    │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │ ⚠️ WARNING: Candidate thiếu 1 must-have requirement                │ │
│  │                                                                     │ │
│  │ AI đã check CV của applicant against JD requirements. Một số       │ │
│  │ requirement bắt buộc không được đáp ứng.                           │ │
│  │                                                                     │ │
│  │ [Xem chi tiết ↓]                                                   │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │ REQUIREMENT CHECKLIST                                              │ │
│  ├────────────────────────────────────────────────────────────────────┤ │
│  │                                                                    │ │
│  │ MUST-HAVE REQUIREMENTS                                             │ │
│  │ ┌────────────────────────────────────────────────────────────────┐ │ │
│  │ │ ✅ 5+ years experience                                         │ │ │
│  │ │    JD: "Minimum 5 years professional experience"               │ │ │
│  │ │    CV: "6 years at Company X, Y, Z"                            │ │ │
│  │ │    Status: PASS                                                │ │ │
│  │ └────────────────────────────────────────────────────────────────┘ │ │
│  │                                                                    │ │
│  │ ┌────────────────────────────────────────────────────────────────┐ │ │
│  │ │ ❌ PMP Certification                                           │ │ │
│  │ │    JD: "PMP certification required (non-negotiable)"           │ │ │
│  │ │    CV: No mention of PMP                                       │ │ │
│  │ │    Status: FAIL — Missing                                      │ │ │
│  │ └────────────────────────────────────────────────────────────────┘ │ │
│  │                                                                    │ │
│  │ NICE-TO-HAVE                                                     │ │
│  │ ┌────────────────────────────────────────────────────────────────┐ │ │
│  │ │ ☐ AWS Certification                                            │ │ │
│  │ │    JD: "AWS certification preferred"                           │ │ │
│  │ │    CV: Not mentioned                                           │ │ │
│  │ │    Status: Not required                                        │ │ │
│  │ └────────────────────────────────────────────────────────────────┘ │ │
│  │                                                                    │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │ SUMMARY                                                             │ │
│  ├────────────────────────────────────────────────────────────────────┤ │
│  │  ✅ Must-have requirements PASS: 1/2                               │ │
│  │  ❌ Must-have requirements FAIL: 1                                 │ │
│  │                                                                     │ │
│  │  📋 JD Reference: [Senior PM Job Description v2.1](link)           │ │
│  │  📄 CV Reference: [Nguyen_Van_A_CV.pdf](link)                      │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │ RECOMMENDATION                                                      │ │
│  ├────────────────────────────────────────────────────────────────────┤ │
│  │                                                                     │ │
│  │  **DO NOT PROCEED** (unless exception approved)                     │ │
│  │                                                                     │ │
│  │  Candidate không meet must-have requirement (PMP certification).   │ │
│  │  Đề xuất: Không đưa vào vòng phỏng vấn trừ khi có exception.       │ │
│  │                                                                     │ │
│  │  Your Options:                                                      │ │
│  │                                                                     │ │
│  │  ┌──────────────────────┐  ┌──────────────────────────────┐        │ │
│  │  │ 🚫 Reject Candidate  │  │ ⚡ Escalate Exception Request │        │ │
│  │  │ Continue searching   │  │ Submit to hiring manager     │        │ │
│  │  └──────────────────────┘  └──────────────────────────────┘        │ │
│  │                                                                     │ │
│  │  ┌────────────────────────────────────────┐                         │ │
│  │  │ 📝 Request JD Clarification            │                         │ │
│  │  │ Ask if PMP can be waived               │                         │ │
│  │  └────────────────────────────────────────┘                         │ │
│  │                                                                     │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │ AI REASONING (expandable)                                          │ │
│  ├────────────────────────────────────────────────────────────────────┤ │
│  │                                                                     │ │
│  │ This recommendation based on:                                      │ │
│  │ 1. JD explicitly states "PMP required (non-negotiable)"            │ │
│  │ 2. CV không có any mention of PMP certification                    │ │
│  │ 3. AI cannot assume equivalent experience without evidence         │ │
│  │                                                                     │ │
│  │ Evidence:                                                           │ │
│  │ - JD excerpt (page 3): "PMP certification required"                │ │
│  │ - CV excerpt (page 1-2): No PMP mentioned                          │ │
│  │                                                                     │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Wireframe 2: Candidate Evaluation Result (All Requirements Met)

```
┌──────────────────────────────────────────────────────────────────────────┐
│  ← Back to Search         Candidate: Trần Thị B                 [⋮]    │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │ ✅ SUCCESS: Candidate meets all must-have requirements             │ │
│  │                                                                     │ │
│  │ AI đã verify tất cả requirements. Candidate qualify cho vị trí.    │ │
│  │                                                                     │ │
│  │ [Xem chi tiết ↓]                                                   │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │ REQUIREMENT CHECKLIST                                              │ │
│  ├────────────────────────────────────────────────────────────────────┤ │
│  │                                                                    │ │
│  │ MUST-HAVE REQUIREMENTS                                             │ │
│  │ ┌────────────────────────────────────────────────────────────────┐ │ │
│  │ │ ✅ 5+ years experience                                         │ │ │
│  │ │    JD: "Minimum 5 years professional experience"               │ │ │
│  │ │    CV: "7 years at Company A, B"                               │ │ │
│  │ │    Status: PASS                                                │ │ │
│  │ └────────────────────────────────────────────────────────────────┘ │ │
│  │                                                                    │ │
│  │ ┌────────────────────────────────────────────────────────────────┐ │ │
│  │ │ ✅ PMP Certification                                           │ │ │
│  │ │    JD: "PMP certification required (non-negotiable)"           │ │ │
│  │ │    CV: "PMP certified (PMI, 2022)"                             │ │ │
│  │ │    Status: PASS                                                │ │ │
│  │ └────────────────────────────────────────────────────────────────┘ │ │
│  │                                                                    │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │ RECOMMENDATION                                                      │ │
│  ├────────────────────────────────────────────────────────────────────┤ │
│  │                                                                     │ │
│  │  **PROCEED TO INTERVIEW**                                          │ │
│  │                                                                     │ │
│  │  Candidate fully qualified. Recommended next steps:                │ │
│  │                                                                     │ │
│  │  ┌──────────────────────┐  ┌──────────────────────────────┐        │ │
│  │  │ 📞 Schedule Interview │  │ 📧 Send Interview Invitation  │        │ │
│  │  │ Technical round      │  │ With prepared questions       │        │ │
│  │  └──────────────────────┘  └──────────────────────────────┘        │ │
│  │                                                                     │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Wireframe 3: JD Unclear — Escalation Flow

```
┌──────────────────────────────────────────────────────────────────────────┐
│  ← Back to Search         JD Clarification Needed               [⋮]    │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────────┐ │
│  │ ⚠️ JD REQUIREMENTS NOT CLEAR                                       │ │
│  │                                                                     │ │
│  │ AI cannot determine which requirements are MUST-HAVE vs NICE-TO-HAVE│ │
│  │                                                                     │ │
│  │ JD text: "5+ years, PMP, AWS. Preferred: Python, Agile."           │ │
│  │                                                                     │ │
│  │ ❓ Không rõ: "Preferred" apply cho tất cả hay chỉ AWS?             │ │
│  │                                                                     │ │
│  └────────────────────────────────────────────────────────────────────┘ │
│                                                                          │
│  Your Options:                                                           │
│                                                                          │
│  ┌──────────────────────────────────────────────────────────────────┐   │
│  │ 📝 Request JD Clarification                                       │   │
│  │                                                                   │   │
│  │ This will:                                                        │   │
│  │ • Create ticket #CLR-00123                                        │   │
│  │ • Notify hiring manager: john.doe@company.com                    │   │
│  │ • Expected response: within 4 hours                               │   │
│  │                                                                   │   │
│  │ [Submit Request] [Cancel]                                         │   │
│  └──────────────────────────────────────────────────────────────────┘   │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Interaction Flow

```
                         ┌─────────────────┐
                         │ User: Evaluate  │
                         │  Candidate      │
                         └────────┬────────┘
                                  │
                                  ▼
                    ┌─────────────────────────┐
                    │ AI: Parse JD + CV       │
                    │   Cross-check reqs      │
                    └────────┬────────────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
         ┌────────┐    ┌──────────┐   ┌─────────┐
         │ PASS   │    │  FAIL    │   │ UNCLEAR │
         └───┬────┘    └────┬─────┘   └────┬────┘
             │              │              │
             │              │              │
             ▼              ▼              ▼
    ┌───────────────┐ ┌───────────┐ ┌──────────────┐
    │ Green Banner  │ │Red Banner │ │ Yellow Banner│
    │ Checklist ✅  │ │Checklist ❌│ │Ask Clarify  │
    │ Recommend     │ │Don't Prog │ │ Escalate CTA │
    │ Proceed CTA   │ │ Escalate  │ │              │
    └───────────────┘ └───────────┘ └──────────────┘
```

---

## Design Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `color.warning.red` | `#DC2626` | Missing must-have |
| `color.success.green` | `#16A34A` | All requirements met |
| `color.info.yellow` | `#CA8A04` | Unclear/needs clarification |
| `color.neutral.gray` | `#6B7280` | Nice-to-have |
| `font.size.warning` | `16px` | Warning banner text |
| `spacing.card` | `16px` | Card padding |
| `border.radius` | `8px` | Card rounded corners |

---

## Accessibility Notes

- All color combinations meet **WCAG 2.1 AA** contrast ratio (4.5:1)
- Icons have `aria-label` for screen readers
- Keyboard navigation: Tab through checklist items
- High contrast mode available in settings

---

## React Component Snippet

```jsx
function RequirementChecklist({ requirements }) {
  const passCount = requirements.filter(r => r.status === 'PASS').length;
  const failCount = requirements.filter(r => r.status === 'FAIL').length;
  const totalCount = requirements.length;

  return (
    <div className="requirement-checklist">
      <div className={`warning-banner ${failCount > 0 ? 'warning' : 'success'}`}>
        {failCount > 0 ? (
          <WarningIcon />
        ) : (
          <SuccessIcon />
        )}
        {failCount > 0 ? (
          <p>Candidate thiếu {failCount} must-have requirement(s)</p>
        ) : (
          <p>Candidate meets all must-have requirements</p>
        )}
      </div>

      <div className="checklist">
        {requirements.map(req => (
          <RequirementItem key={req.id} requirement={req} />
        ))}
      </div>

      <RecommendationCard
        status={failCount > 0 ? 'block' : 'proceed'}
        onReject={handleReject}
        onEscalate={handleEscalate}
      />
    </div>
  );
}
```

---

## Testing Checklist

- [ ] Warning banner visible when requirements fail
- [ ] Checklist shows all requirements with correct status
- [ ] Source links work (JD and CV)
- [ ] Escalation buttons functional
- [ ] Color contrast meets WCAG AA
- [ ] Screen reader announces status correctly
- [ ] Keyboard navigation works
- [ ] Responsive on mobile/tablet