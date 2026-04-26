# AI Tool Evaluation Scorecard
### AI Governance Framework | Tool Approval

---

## Purpose

Every AI tool proposed for use in an engineering programme must be evaluated before it enters the Approved Tool Register. This scorecard is the evaluation instrument.

It serves three purposes:
1. **Consistency** — every tool is evaluated against the same criteria, by the same standard
2. **Accountability** — the evaluation is documented, signed off, and retained for audit
3. **Speed** — a structured scorecard is faster than an ad hoc review; engineers know what to prepare

The scorecard covers five evaluation domains. A tool must meet the minimum score in each domain — a high score in one domain cannot compensate for a failing score in another.

---

## Evaluation Domains

### Domain 1: Security Posture (Weight: 30%)

Security is non-negotiable. A tool that fails security cannot be approved regardless of its capability or business value.

| Criterion | Score 0 | Score 1 | Score 2 | Score 3 |
|---|---|---|---|---|
| **Data transmission security** | No encryption documented | Encryption mentioned but not verified | TLS 1.2 confirmed | TLS 1.3 confirmed; independent audit available |
| **Data retention policy** | Unknown or indefinite | Retains data; duration unclear | Retains with documented deletion schedule | Zero retention or immediate deletion confirmed |
| **Model training on inputs** | Inputs used for training; no opt-out | Inputs used for training; opt-out available | No input training by default | Contractual guarantee; independently verified |
| **Access controls** | Shared accounts only | User-level authentication | MFA available | SSO integration + MFA + audit log |
| **Vendor security certifications** | None | Self-attested | SOC 2 Type I | SOC 2 Type II + ISO 27001 |
| **Penetration testing** | None documented | Internal only | Third-party; results not shared | Third-party; results available on request |

**Domain 1 Minimum to Pass: 12 / 18**
**Domain 1 Auto-Fail Conditions:** Score of 0 on Data Transmission Security OR Model Training criterion.

---

### Domain 2: Data Handling and Privacy (Weight: 25%)

| Criterion | Score 0 | Score 1 | Score 2 | Score 3 |
|---|---|---|---|---|
| **Data Processing Agreement available** | No DPA | DPA available but non-standard | Standard DPA available | Custom DPA negotiable |
| **GDPR / data regulation compliance** | No compliance documentation | Self-declared | Third-party verified | Certified + regular audit cycle |
| **Geographic data residency** | Unknown | Data may leave jurisdiction | Configurable | Guaranteed in-jurisdiction option |
| **Incident notification SLA** | None | > 72 hours | ≤ 72 hours (GDPR standard) | ≤ 24 hours |
| **Right to deletion** | Not supported | Manual process only | API-supported | Automated with confirmation |

**Domain 2 Minimum to Pass: 8 / 15**
**Domain 2 Auto-Fail Conditions:** Score of 0 on Data Processing Agreement OR GDPR compliance.

---

### Domain 3: Accuracy and Reliability (Weight: 20%)

| Criterion | Score 0 | Score 1 | Score 2 | Score 3 |
|---|---|---|---|---|
| **Published accuracy benchmarks** | None | Marketing claims only | Independent benchmark referenced | Independent benchmark + reproducible methodology |
| **Hallucination rate documentation** | Not disclosed | Acknowledged but unquantified | Quantified for general use | Quantified per use case |
| **Confidence scoring available** | Not available | Available but non-configurable | Configurable threshold | Configurable + API-accessible for HITL routing |
| **Uptime SLA** | None | < 99% | 99%–99.5% | > 99.9% with credits |
| **Model version control** | No versioning | Versions exist; uncontrolled updates | Versioned; 30-day notice of changes | Versioned; pinnable; change log published |

**Domain 3 Minimum to Pass: 8 / 15**
**Domain 3 Auto-Fail Conditions:** No confidence scoring capability for tools intended for production deployment.

---

### Domain 4: Integration and Operability (Weight: 15%)

| Criterion | Score 0 | Score 1 | Score 2 | Score 3 |
|---|---|---|---|---|
| **API availability** | No API; UI only | API in beta | Stable API with documentation | Stable API + SDK + webhook support |
| **Audit logging** | None | Manual export only | System-generated logs | Structured logs with API access |
| **Role-based access control** | All-or-nothing | Basic role separation | Full RBAC | RBAC + attribute-based access |
| **Rollback / disable capability** | Not possible | Manual intervention required | Admin-level disable | Automated disable + data purge |
| **Support and SLA** | Community only | Email support | Dedicated support + SLA | Enterprise SLA + named support contact |

**Domain 4 Minimum to Pass: 7 / 15**

---

### Domain 5: Business Value and Strategic Fit (Weight: 10%)

| Criterion | Score 0 | Score 1 | Score 2 | Score 3 |
|---|---|---|---|---|
| **Use case alignment** | No clear use case defined | Broad potential but undefined | Specific use case identified | Multiple high-value use cases with ROI estimate |
| **Engineer adoption likelihood** | Low — significant behaviour change required | Moderate — some workflow change | High — integrates into existing workflow | Very high — reduces workflow steps |
| **Vendor stability** | Unknown or early-stage | < 2 years operating | 2–5 years; stable revenue | > 5 years or enterprise-grade backing |
| **Cost per outcome** | Unit economics unknown | High cost; unclear ROI | Acceptable cost; positive ROI projected | Strong cost-to-value ratio with evidence |

**Domain 5 Minimum to Pass: 6 / 12**

---

## Scoring Summary Sheet

```
TOOL EVALUATION SCORECARD
Tool Name: ________________________________
Evaluation Date: ___________________________
Evaluator: _________________________________
Requestor: _________________________________

DOMAIN SCORES

Domain 1 — Security Posture (max 18, pass ≥ 12)
  Score: ______ / 18    Pass / Fail

Domain 2 — Data Handling and Privacy (max 15, pass ≥ 8)
  Score: ______ / 15    Pass / Fail

Domain 3 — Accuracy and Reliability (max 15, pass ≥ 8)
  Score: ______ / 15    Pass / Fail

Domain 4 — Integration and Operability (max 15, pass ≥ 7)
  Score: ______ / 15    Pass / Fail

Domain 5 — Business Value and Strategic Fit (max 12, pass ≥ 6)
  Score: ______ / 12    Pass / Fail

WEIGHTED TOTAL
  Domain 1 (×0.30): ______
  Domain 2 (×0.25): ______
  Domain 3 (×0.20): ______
  Domain 4 (×0.15): ______
  Domain 5 (×0.10): ______
  TOTAL WEIGHTED SCORE: ______ / 100

AUTO-FAIL CONDITIONS TRIGGERED: Yes / No
  (If Yes, tool is Rejected regardless of total score)

DECISION

[ ] APPROVED — All domains pass. No conditions.
[ ] APPROVED CONDITIONAL — All domains pass. Conditions:
    ________________________________________________
[ ] UNDER REVIEW — Evaluation incomplete. Missing:
    ________________________________________________
[ ] REJECTED — Domain failure or auto-fail condition.
    Reason: ________________________________________

Approved by (AI Governance Lead): _________________ Date: _______
Approved by (Engineering Director): _______________ Date: _______
```

---

## Approved Tool Register Template

Once approved, the tool is added to the register:

```markdown
## Approved Tool Register

Last updated: [Date]
Owner: [AI Governance Lead name]

| Tool | Status | Approved Use Cases | Data Tiers Permitted | Last Review | Next Review |
|---|---|---|---|---|---|
| [Tool Name] | Approved | [Use cases] | Public, Internal | [Date] | [Date] |
| [Tool Name] | Conditional | [Use cases] | Public only | [Date] | [Date] |
| [Tool Name] | Rejected | N/A | N/A | [Date] | N/A |
```

---

## Quarterly Re-Evaluation Trigger Conditions

Tools in the Approved Register are re-evaluated quarterly. Additionally, an immediate re-evaluation is triggered by any of the following:

- Vendor announces a change to data handling or training policies
- A security incident is reported involving the vendor or tool
- The tool's primary use case changes (new feature set requiring reassessment)
- A data incident occurs involving the tool within the organisation
- Regulatory requirements change in a way that affects the tool's compliance status

When a re-evaluation is triggered, the tool's status moves to **Under Review** until the evaluation is complete. Teams are notified within 24 hours.

---

## Key Principle

> *The purpose of the scorecard is not to block AI adoption. It is to ensure that every tool that enters the organisation has been assessed with the same rigour — so that when the audit happens, or when the incident occurs, there is a documented, defensible decision behind every tool in use.*

Speed of adoption matters. So does the ability to answer: *"Why did we approve this tool?"*

---

*Part of the [AI Governance Framework](./README.md) series.*
