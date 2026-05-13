# UI/UX Solution — Card

## Overview

| Field | Content |
|-------|---------|
| **Solution Name** | Missing Requirement Warning UI |
| **Layer** | Layer 1 — User Interface |
| **Primary Goal** | Make missing must-have requirements VISIBLE và obvious đến recruiter |
| **Risk Mitigated** | AI recommend positive nhưng hidden/invisible missing requirements |

## Design Principles

1. **Visibility First** — Missing requirements phải hiện ra ngay lập tức, không hidden trong text
2. **Color-Coded** — Red/Yellow/Green cho quick scanning
3. **Action-Oriented** — Mỗi warning必须有 actionable next steps
4. **Source-Linked** — Mọi claim phải có link đến JD và CV
5. **No Surprise** — User không thể claim "tôi không thấy warning"

## Component Breakdown

### 1. Warning Banner

**Purpose:** Immediate visual signal khi có missing requirement

**States:**
- 🔴 **RED (Critical)** — One or more must-have requirements FAIL
- 🟡 **YELLOW (Warning)** — Nice-to-have missing or uncertain
- 🟢 **GREEN (Safe)** — All must-have requirements PASS

**Content:**
```
[ICON] WARNING/SUCCESS: [Summary message]
[Expand/Collapse]
```

### 2. Requirement Checklist

**Purpose:** Detailed breakdown của từng requirement

**Format:**
```
┌─────────────────────────────────────────────────────┐
│ REQUIREMENT CHECKLIST                               │
├─────────────────────────────────────────────────────┤
│ ✅ [Req Name]        │ [CV Value]   │ PASS         │
│ ❌ [Req Name]        │ Not found    │ FAIL         │
│ ☐  [Req Name]        │ Not mentioned│ N/A          │
└─────────────────────────────────────────────────────┘
```

**Features:**
- Icon-based quick scanning (✅❌☐)
- Side-by-side JD vs CV comparison
- Hover untuk view source evidence

### 3. Evidence Panel

**Purpose:** Show source code cho AI's判断

**Content:**
```
📋 JD Source: "PMP certification required" (link to JD section)
📋 CV Source: "Experience: 6 years" (link to CV page 2)
```

### 4. Recommendation Card

**Purpose:** Clear decision và next steps

**Format:**
```
┌─────────────────────────────────────────────────────┐
│ RECOMMENDATION: [DO NOT PROCEED / PROCEED]         │
├─────────────────────────────────────────────────────┤
│ [Explanation]                                       │
│                                                     │
│ Your Options:                                       │
│ [Button 1] [Button 2]                               │
└─────────────────────────────────────────────────────┘
```

### 5. Escalation CTA

**Purpose:** Provide path when exception needed

**Options:**
1. **Reject Candidate** — Continue searching
2. **Escalate Exception** — Submit to hiring manager
3. **Request Clarification** — Ask about JD requirements

## Accessibility

- **WCAG 2.1 AA** compliant colors
- **Screen reader** friendly labels
- **Keyboard navigation** support
- **High contrast** mode available

## Responsive Design

| Breakpoint | Layout |
|------------|--------|
| Desktop (>1024px) | Full 2-column layout |
| Tablet (768-1024px) | Stacked layout |
| Mobile (<768px) | Simplified view with expand |

---

## Mockup Screenshots

See [demo.md](demo.md) for detailed wireframes and interaction flows.