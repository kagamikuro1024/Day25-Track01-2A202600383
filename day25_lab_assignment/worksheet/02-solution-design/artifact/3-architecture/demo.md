# Architecture Solution — Demo

## System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              USER LAYER                                     │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │  Recruiter UI: Evaluate Candidate                                     │  │
│  │  - Upload JD (PDF/TXT)                                                │  │
│  │  - Upload CV (PDF/TXT)                                                │  │
│  │  - Submit evaluation request                                          │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                          ORCHESTRATION LAYER                                │
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  1. Input Validator                                                   │ │
│  │     ├─ Check: JD file present?                                        │ │
│  │     ├─ Check: CV file present?                                        │ │
│  │     ├─ Check: File format valid?                                      │ │
│  │     └─ IF invalid → return error + ask user clarify                  │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                   │                                         │
│                                   ▼                                         │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  2. JD Parser (Rule-based + LLM)                                      │ │
│  │     ├─ Extract MUST-HAVE requirements                                 │ │
│  │     │   - Years of experience                                         │ │
│  │     │   - Certifications (PMP, AWS, etc.)                             │ │
│  │     │   - Education level                                             │ │
│  │     │   - Technical skills                                            │ │
│  │     ├─ Extract NICE-TO-HAVE requirements                              │ │
│  │     └─ Output: Structured JSON                                        │ │
│  │        {                                                               │ │
│  │          "must_have": [                                                │ │
│  │            {"field": "experience", "min": 5, "unit": "years"},        │ │
│  │            {"field": "certification", "value": "PMP"}                 │ │
│  │          ],                                                            │ │
│  │          "nice_to_have": [                                             │ │
│  │            {"field": "certification", "value": "AWS"}                 │ │
│  │          ]                                                             │ │
│  │        }                                                               │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                   │                                         │
│                                   ▼                                         │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  3. CV Parser (LLM)                                                   │ │
│  │     ├─ Extract years of experience                                    │ │
│  │     ├─ Extract certifications                                         │ │
│  │     ├─ Extract education background                                   │ │
│  │     ├─ Extract technical skills                                       │ │
│  │     └─ Output: Structured JSON                                        │ │
│  │        {                                                               │ │
│  │          "experience_years": 6,                                       │ │
│  │          "certifications": [],                                        │ │
│  │          "education": "Bachelor's in CS",                             │ │
│  │          "skills": ["Python", "AWS", "Agile"]                         │ │
│  │        }                                                               │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                   │                                         │
│                                   ▼                                         │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  4. Rule-Based Validator (DETERMINISTIC GUARDRAIL)                    │ │
│  │     ├─ For each must-have requirement:                                │ │
│  │     │   IF requirement in CV → PASS                                   │ │
│  │     │   IF requirement NOT in CV → FAIL                               │ │
│  │     ├─ Count PASS/FAIL                                                │ │
│  │     ├─ IF any FAIL → flag for negative recommendation                │ │
│  │     └─ Output: Validation Result                                      │ │
│  │        {                                                               │ │
│  │          "results": [                                                  │ │
│  │            {"requirement": "5+ years", "status": "PASS",              │ │
│  │             "evidence": "6 years at Company X"},                       │ │
│  │            {"requirement": "PMP", "status": "FAIL",                   │ │
│  │             "evidence": "Not found in CV"}                            │ │
│  │          ],                                                            │ │
│  │          "pass_count": 1,                                              │ │
│  │          "fail_count": 1,                                              │ │
│  │          "should_block": true                                          │ │
│  │        }                                                               │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                   │                                         │
│                                   ▼                                         │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  5. AI Assistant (LLM) — Only if validation PASS or uncertain         │ │
│  │     ├─ System prompt with guardrail rules                            │ │
│  │     ├─ Incorporate validation results                                │ │
│  │     ├─ Generate recommendation with reasoning                        │ │
│  │     └─ Output: AI Response                                           │ │
│  │        {                                                               │ │
│  │          "recommendation": "DO NOT PROCEED",                          │ │
│  │          "reasoning": "Candidate thiếu PMP certification...",         │ │
│  │          "options": ["Reject", "Escalate"]                            │ │
│  │        }                                                               │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                   │                                         │
│                                   ▼                                         │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  6. Output Guardrail (FINAL CHECK)                                    │ │
│  │     ├─ Check: Does AI recommendation match validation?                │ │
│  │     ├─ IF AI recommend positive BUT validation has FAIL → BLOCK      │ │
│  │     ├─ IF AI recommend negative AND validation has FAIL → ALLOW      │ │
│  │     ├─ IF validation PASS → ALLOW AI recommendation                  │ │
│  │     └─ Output: Final Response to User                                │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                          ESCALATION LAYER                                   │
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  Human-in-Loop (Hiring Manager)                                       │ │
│  │     ├─ Exception cases (equivalent experience)                        │ │
│  │     ├─ JD clarification requests                                      │ │
│  │     ├─ Disputed AI recommendations                                    │ │
│  │     └─ SLA: Response within 4 hours                                   │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Decision Tree

```
                         ┌─────────────────┐
                         │  User Request   │
                         │  Evaluate       │
                         └────────┬────────┘
                                  │
                                  ▼
                    ┌─────────────────────────┐
                    │ 1. Input Validator      │
                    │   JD + CV present?      │
                    └────────┬────────────────┘
                             │
              ┌──────────────┼──────────────┐
              │ NO           │              │ YES
              ▼              │              ▼
    ┌───────────────┐        │    ┌─────────────────┐
    │ Return error  │        │    │ 2. JD Parser    │
    │ + ask upload  │        │    └────────┬────────┘
    └───────────────┘        │             │
                             │             ▼
                             │    ┌─────────────────┐
                             │    │ 3. CV Parser    │
                             │    └────────┬────────┘
                             │             │
                             │             ▼
                             │    ┌─────────────────┐
                             │    │ 4. Rule         │
                             │    │ Validator       │
                             │    └────────┬────────┘
                             │             │
                             │    ┌────────┼────────┐
                             │    │        │        │
                             │    ▼        ▼        ▼
                             │  ┌───┐  ┌──────┐  ┌──────┐
                             │  │ALL│  │ANY   │  │UNCLE │
                             │  │PASS│  │FAIL  │  │RAR   │
                             │  └───┘  └──┬───┘  └──┬───┘
                             │     │      │         │
                             │     │      │         │
                             │     ▼      ▼         ▼
                             │  ┌──────────┐  ┌──────────┐
                             │  │5. AI     │  │5. AI     │
                             │  │Assistant │  │Assistant │
                             │  │(positive │  │(negative │
                             │  │ path)    │  │ path)    │
                             │  └────┬─────┘  └────┬─────┘
                             │       │             │
                             │       ▼             ▼
                             │  ┌──────────────────────┐
                             │  │6. Output Guardrail   │
                             │  │  - Check consistency │
                             │  │  - Block if conflict │
                             │  └──────────┬───────────┘
                             │             │
                             │             ▼
                             │    ┌─────────────────┐
                             │    │ Final Response  │
                             │    │ to User         │
                             │    └─────────────────┘
                             │
                             ▼
              ┌──────────────────────┐
              │ Escalate to Human    │
              │ (if unclear)         │
              └──────────────────────┘
```

---

## Pseudocode Implementation

```python
class CandidateEvaluator:
    def __init__(self):
        self.jd_parser = JDParser()
        self.cv_parser = CVParser()
        self.rule_validator = RuleValidator()
        self.ai_assistant = AIAssistant()
        self.output_guardrail = OutputGuardrail()

    async def evaluate_candidate(self, jd_text: str, cv_text: str, user_query: str) -> dict:
        # Step 1: Input Validation
        if not self._validate_input(jd_text, cv_text):
            return self._error_response("JD và CV đều cần được cung cấp")

        # Step 2: Parse JD
        jd_requirements = await self.jd_parser.parse(jd_text)

        # Step 3: Parse CV
        cv_qualifications = await self.cv_parser.parse(cv_text)

        # Step 4: Rule-Based Validation (DETERMINISTIC)
        validation_result = self.rule_validator.validate(
            jd_requirements, cv_qualifications
        )

        # Step 5: AI Assistant (only if needed)
        if validation_result["should_block"]:
            # Block path: AI must recommend negative
            ai_response = await self.ai_assistant.generate(
                jd_requirements,
                cv_qualifications,
                validation_result,
                force_negative=True
            )
        elif validation_result["all_pass"]:
            # Pass path: AI can recommend positive
            ai_response = await self.ai_assistant.generate(
                jd_requirements,
                cv_qualifications,
                validation_result,
                force_negative=False
            )
        else:
            # Unclear path: Escalate
            return await self._escalate_to_human(
                "JD unclear or validation inconclusive",
                jd_requirements,
                cv_qualifications
            )

        # Step 6: Output Guardrail (Final Check)
        final_response = self.output_guardrail.validate(
            ai_response,
            validation_result
        )

        return final_response

    def _validate_input(self, jd_text: str, cv_text: str) -> bool:
        return bool(jd_text and cv_text and len(jd_text) > 10 and len(cv_text) > 10)

    async def _escalate_to_human(self, reason: str, jd_req: dict, cv_qual: dict) -> dict:
        ticket_id = await self._create_ticket(reason, jd_req, cv_qual)
        return {
            "status": "escalated",
            "ticket_id": ticket_id,
            "message": "Yêu cầu được chuyển đến hiring manager. Dự kiến phản hồi trong 4h.",
            "options": ["Wait", "Contact HR"]
        }
```

---

## Rule-Based Validator Implementation

```python
class RuleValidator:
    def validate(self, jd_requirements: dict, cv_qualifications: dict) -> dict:
        results = []
        pass_count = 0
        fail_count = 0

        # Check must-have requirements
        for req in jd_requirements["must_have"]:
            result = self._check_requirement(req, cv_qualifications)
            results.append(result)

            if result["status"] == "PASS":
                pass_count += 1
            else:
                fail_count += 1

        # Determine if should block positive recommendation
        should_block = fail_count > 0

        return {
            "results": results,
            "pass_count": pass_count,
            "fail_count": fail_count,
            "should_block": should_block,
            "all_pass": fail_count == 0
        }

    def _check_requirement(self, req: dict, cv: dict) -> dict:
        field = req["field"]

        if field == "experience":
            cv_years = cv.get("experience_years", 0)
            required_years = req.get("min", 0)

            if cv_years >= required_years:
                return {
                    "requirement": f"{required_years}+ years experience",
                    "status": "PASS",
                    "evidence": f"{cv_years} years in CV"
                }
            else:
                return {
                    "requirement": f"{required_years}+ years experience",
                    "status": "FAIL",
                    "evidence": f"Only {cv_years} years in CV"
                }

        elif field == "certification":
            required_cert = req.get("value", "").upper()
            cv_certs = [c.upper() for c in cv.get("certifications", [])]

            if required_cert in cv_certs:
                return {
                    "requirement": f"{required_cert} certification",
                    "status": "PASS",
                    "evidence": f"Found {required_cert} in CV"
                }
            else:
                return {
                    "requirement": f"{required_cert} certification",
                    "status": "FAIL",
                    "evidence": f"{required_cert} not found in CV"
                }

        # Add more field types as needed

        return {
            "requirement": req.get("field", "Unknown"),
            "status": "NOT_MENTIONED",
            "evidence": "Cannot determine"
        }
```

---

## Output Guardrail Implementation

```python
class OutputGuardrail:
    def validate(self, ai_response: dict, validation_result: dict) -> dict:
        # Check if AI recommendation matches validation
        ai_recommendation = ai_response.get("recommendation", "")
        should_block = validation_result.get("should_block", False)

        # Rule 1: If validation says block, AI cannot recommend positive
        if should_block:
            positive_keywords = ["proceed", "interview", "qualified", "recommend", "good fit"]
            is_positive = any(kw in ai_recommendation.lower() for kw in positive_keywords)

            if is_positive:
                # BLOCK AI response, use validation-based response instead
                return self._generate_blocked_response(validation_result)

        # Rule 2: If validation says all pass, AI should not reject without reason
        if validation_result.get("all_pass", False):
            negative_keywords = ["reject", "not qualified", "do not proceed"]
            is_negative = any(kw in ai_recommendation.lower() for kw in negative_keywords)

            if is_negative:
                # AI needs to provide strong evidence for rejection
                if not ai_response.get("strong_evidence", False):
                    return self._flag_for_review(ai_response, validation_result)

        # Rule 3: Check for hallucination
        if self._detect_hallucination(ai_response, validation_result):
            return self._generate_hallucination_response()

        # All checks passed
        return ai_response

    def _generate_blocked_response(self, validation_result: dict) -> dict:
        fail_requirements = [
            r["requirement"] for r in validation_result["results"]
            if r["status"] == "FAIL"
        ]

        return {
            "status": "blocked",
            "recommendation": "DO NOT PROCEED",
            "reason": f"Candidate thiếu các must-have requirements: {', '.join(fail_requirements)}",
            "options": ["Reject", "Escalate exception"],
            "evidence": validation_result["results"]
        }

    def _detect_hallucination(self, ai_response: dict, validation_result: dict) -> bool:
        # Check if AI mentions requirements not in JD
        ai_text = ai_response.get("reasoning", "")

        jd_requirements = [r["field"] for r in validation_result["results"]]

        for req in jd_requirements:
            if req.lower() not in ai_text.lower():
                # AI mentioned something not in JD
                return True

        return False
```

---

## API Endpoints

```yaml
# Candidate Evaluation API

POST /api/v1/evaluate
  Request:
    {
      "jd_text": string,
      "cv_text": string,
      "user_query": string
    }

  Response:
    {
      "status": "pass" | "fail" | "escalated",
      "recommendation": string,
      "reasoning": string,
      "requirements_check": [...],
      "options": [...],
      "ticket_id": string (if escalated)
    }

POST /api/v1/escalate
  Request:
    {
      "reason": string,
      "jd_text": string,
      "cv_text": string
    }

  Response:
    {
      "ticket_id": string,
      "sla_hours": 4,
      "contact_email": string
    }

GET /api/v1/ticket/{ticket_id}
  Response:
    {
      "ticket_id": string,
      "status": "pending" | "reviewed" | "resolved",
      "decision": string,
      "reviewer": string,
      "updated_at": timestamp
    }
```

---

## Monitoring & Logging

```python
# Audit Log Schema

class EvaluationLog:
    timestamp: datetime
    user_id: string
    candidate_id: string
    jd_hash: string  # Hash for privacy
    cv_hash: string  # Hash for privacy
    input_query: string
    validation_result: dict
    ai_response: dict
    final_response: dict
    was_escalated: bool
    escalation_ticket_id: string (if applicable)
    feedback: string (if user provides)

# Metrics to Track

- Total evaluations per day
- Pass/fail/escalated rates
- Average response time
- Escalation SLA compliance
- AI vs Rule validator disagreement rate
- User feedback scores
- Bias detection alerts
```

---

## Deployment Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Kubernetes Cluster                       │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  API Gateway (Load Balancer)                            │    │
│  └─────────────────────┬───────────────────────────────────┘    │
│                        │                                         │
│  ┌─────────────────────▼───────────────────────────────────┐    │
│  │  Evaluation Service (3 replicas)                        │    │
│  │  - Auto-scales based on request queue                   │    │
│  └─────────────────────┬───────────────────────────────────┘    │
│                        │                                         │
│  ┌─────────────────────▼───────────────────────────────────┐    │
│  │  Redis Cache (JD/CV parsing results)                    │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  PostgreSQL (Audit logs, tickets)                       │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  LLM Service (External API or Self-hosted)              │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Testing Checklist

- [ ] Rule validator blocks positive recommendation when FAIL
- [ ] AI cannot override rule validation
- [ ] Escalation creates ticket with correct SLA
- [ ] Audit logs capture all decisions
- [ ] Output guardrail catches hallucination
- [ ] Load test: 100 req/s without timeout
- [ ] Failover: Service continues if LLM down
- [ ] Privacy: No PII in logs