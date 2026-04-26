# Responsible AI Checklist
### AI Governance Framework | Pre-Deployment Compliance

> Aligned to ISO/IEC 42001 AI Management System Standard and NIST AI Risk Management Framework

---

## How To Use This Checklist

This checklist is completed by the Engineering Director and AI Governance Lead before any AI system advances to production deployment (Stage 3 gate).

Each item is marked:
- ✅ **Complete** — criterion is fully met with documented evidence
- ⚠️ **Partial** — criterion is partially met; documented exception and mitigation required
- ❌ **Not Met** — criterion is not met; system cannot advance to production

**Production deployment requires all items marked ✅ or ⚠️ with approved exceptions.**
**Any ❌ is a hard stop.**

---

## Section 1: Governance and Accountability
*Aligned to ISO/IEC 42001 §6 — Planning; NIST AI RMF — GOVERN*

| # | Criterion | Status | Evidence / Notes |
|---|---|---|---|
| 1.1 | A named AI system owner is identified and has accepted accountability | | |
| 1.2 | The system's intended use, scope, and limitations are documented | | |
| 1.3 | Out-of-scope use cases are explicitly defined and communicated to users | | |
| 1.4 | The Engineering Director has reviewed and approved the system for production | | |
| 1.5 | A business stakeholder has confirmed the system's outputs meet business quality standards | | |
| 1.6 | An AI governance review is scheduled within 90 days of production deployment | | |

---

## Section 2: Risk Assessment
*Aligned to ISO/IEC 42001 §6.1 — Risk and Opportunity; NIST AI RMF — MAP*

| # | Criterion | Status | Evidence / Notes |
|---|---|---|---|
| 2.1 | A formal AI risk assessment has been completed for this system | | |
| 2.2 | Identified risks are documented with likelihood, impact, and mitigation | | |
| 2.3 | Residual risks (after mitigation) have been accepted by the Engineering Director | | |
| 2.4 | The risk of AI hallucination is documented with the mitigation approach | | |
| 2.5 | The risk of bias in outputs has been assessed for the intended use case | | |
| 2.6 | Reputational risk of system failure has been assessed and accepted | | |

---

## Section 3: Data Governance
*Aligned to ISO/IEC 42001 §8 — Operation; NIST AI RMF — MAP/MEASURE*

| # | Criterion | Status | Evidence / Notes |
|---|---|---|---|
| 3.1 | The data classification tier for all inputs has been defined and approved | | |
| 3.2 | No data above the approved classification tier is processed by this system | | |
| 3.3 | The vendor's data handling policy has been reviewed and is compliant with organisational standards | | |
| 3.4 | A Data Processing Agreement (DPA) is in place with the AI vendor | | |
| 3.5 | Personal data handling complies with applicable data protection regulations | | |
| 3.6 | Data retention and deletion procedures are documented and tested | | |
| 3.7 | The system does not use input data to train or fine-tune the underlying model (or opt-out is confirmed) | | |

---

## Section 4: Accuracy and Performance
*Aligned to ISO/IEC 42001 §9 — Performance Evaluation; NIST AI RMF — MEASURE*

| # | Criterion | Status | Evidence / Notes |
|---|---|---|---|
| 4.1 | Accuracy has been measured against a structured evaluation set (minimum 100 scenarios) | | |
| 4.2 | Accuracy meets or exceeds the threshold defined at Gate 1 | | |
| 4.3 | Accuracy has been validated in Stage 2 (real data, pilot users) and meets threshold | | |
| 4.4 | The system's known failure modes are documented | | |
| 4.5 | The system's performance on edge cases and adversarial inputs is documented | | |
| 4.6 | Latency meets the SLA defined for this use case | | |
| 4.7 | Monthly accuracy monitoring is scheduled and the process is documented | | |

---

## Section 5: Human-in-the-Loop Controls
*Aligned to ISO/IEC 42001 §8.4 — AI system operation; NIST AI RMF — MANAGE*

| # | Criterion | Status | Evidence / Notes |
|---|---|---|---|
| 5.1 | Confidence score thresholds are defined for auto-respond, review, and escalate | | |
| 5.2 | The HITL routing mechanism is implemented and tested | | |
| 5.3 | Human reviewers are identified, trained, and have accepted their accountability | | |
| 5.4 | HITL escalation response time SLA is defined and operationally achievable | | |
| 5.5 | All HITL interventions are logged with outcome and reviewer identity | | |
| 5.6 | The HITL rate from Stage 2 is within the acceptable threshold | | |

---

## Section 6: Security Controls
*Aligned to ISO/IEC 42001 §8 — Operation; OWASP AI Security Guide*

| # | Criterion | Status | Evidence / Notes |
|---|---|---|---|
| 6.1 | The AI system has passed the Tool Evaluation Scorecard (Domain 1 — Security) | | |
| 6.2 | Access to the AI system is controlled via role-based access control | | |
| 6.3 | Audit logging is enabled for all interactions with the AI system | | |
| 6.4 | Prompt injection risk has been assessed and mitigated | | |
| 6.5 | The system has been tested for data leakage between user sessions | | |
| 6.6 | API keys and credentials used by the system are managed in a secrets vault, not hardcoded | | |
| 6.7 | Network traffic from the system to the AI vendor is encrypted and reviewed | | |

---

## Section 7: Transparency and User Communication
*Aligned to ISO/IEC 42001 §7.4 — Communication; NIST AI RMF — GOVERN*

| # | Criterion | Status | Evidence / Notes |
|---|---|---|---|
| 7.1 | Users are informed that they are interacting with an AI system | | |
| 7.2 | Users are informed of the system's limitations | | |
| 7.3 | Users know how to escalate to a human when the AI output is not adequate | | |
| 7.4 | AI-generated outputs that are delivered without human review are identified as AI-generated | | |
| 7.5 | A user feedback mechanism is in place and actively monitored | | |

---

## Section 8: Operational Readiness
*Aligned to ISO/IEC 42001 §8 — Operation; NIST AI RMF — MANAGE*

| # | Criterion | Status | Evidence / Notes |
|---|---|---|---|
| 8.1 | The production runbook is complete, reviewed, and approved | | |
| 8.2 | The rollback procedure is documented, tested, and executable in ≤ 30 minutes | | |
| 8.3 | Monitoring and alerting is configured for all key production metrics | | |
| 8.4 | The on-call engineer responsible for AI system incidents is identified | | |
| 8.5 | The incident response path for AI-specific incidents is documented | | |
| 8.6 | The operational team has been trained on the HITL protocol and incident procedure | | |

---

## Section 9: Compliance and Audit Readiness
*Aligned to ISO/IEC 42001 §9.2 — Internal audit; NIST AI RMF — GOVERN*

| # | Criterion | Status | Evidence / Notes |
|---|---|---|---|
| 9.1 | All governance documents for this system are stored in a retrievable location | | |
| 9.2 | The Tool Evaluation Scorecard is complete, signed, and filed | | |
| 9.3 | Stage 1 and Stage 2 gate review records are complete and filed | | |
| 9.4 | The AI risk assessment is filed and version-controlled | | |
| 9.5 | The DPA with the AI vendor is filed in the contract repository | | |
| 9.6 | A next compliance review date is scheduled and assigned | | |

---

## Checklist Sign-Off

```
AI SYSTEM: ________________________________
Version: __________________________________
Production Deployment Date: _______________

CHECKLIST SUMMARY

Section 1 — Governance:           __ / 6 complete
Section 2 — Risk Assessment:      __ / 6 complete
Section 3 — Data Governance:      __ / 7 complete
Section 4 — Accuracy:             __ / 7 complete
Section 5 — HITL Controls:        __ / 6 complete
Section 6 — Security:             __ / 7 complete
Section 7 — Transparency:         __ / 5 complete
Section 8 — Operational Readiness:__ / 6 complete
Section 9 — Compliance:           __ / 6 complete

TOTAL: __ / 56 complete

Items marked ⚠️ Partial (list exceptions and mitigations):
1. _______________________________________________
2. _______________________________________________

Items marked ❌ Not Met:
[If any — production deployment is blocked]

DECISION:
[ ] APPROVED FOR PRODUCTION — All items ✅ or ⚠️ with accepted exceptions
[ ] BLOCKED — ❌ items present. Address before re-submitting.

AI Governance Lead: _____________________ Date: _______
Engineering Director: ___________________ Date: _______
Business Stakeholder: __________________ Date: _______
```

---

## Key Principle

> *A checklist does not make AI responsible. Responsible people using a checklist as a shared standard — and being honest when items are not met — make AI responsible. The 30 items here are the minimum bar. The culture of honest self-assessment is what makes the bar meaningful.*

---

*Part of the [AI Governance Framework](./README.md) series.*
