# Staged AI Deployment Model
### AI Governance Framework | Deployment Governance

---

## Why Staged Deployment

The most common AI deployment failure is not a technical failure. It is a governance failure.

A system that works perfectly in a controlled experiment fails in production — not because the model is wrong, but because:

- The production data distribution differs from the test set
- The user population generates query types not covered in testing
- The operational team is not trained to handle HITL escalations
- The rollback procedure was never documented or tested
- Stakeholders were not prepared for the system's limitations

Staged deployment solves this by introducing the system to real-world conditions incrementally — with a gate at each stage that must be passed before the next stage begins.

The philosophy: **move fast where risk is low. Move deliberately where risk is high.**

---

## The Three-Stage Model

```
┌─────────────────────────────────────────────────────────────────┐
│                    STAGE 1: INTERNAL                            │
│                                                                 │
│  Who: Engineering team + AI Governance Lead only               │
│  Data: Synthetic / anonymised only                             │
│  Duration: 2–4 weeks                                           │
│  Goal: Prove the system works as designed                      │
│                                                                 │
└────────────────────────────┬────────────────────────────────────┘
                             │
                      ▼ GATE 1 REVIEW
                             │
┌─────────────────────────────────────────────────────────────────┐
│                    STAGE 2: LIMITED                             │
│                                                                 │
│  Who: Defined pilot user group (10–20% of target users)        │
│  Data: Real data within approved classification                │
│  Duration: 4–8 weeks                                           │
│  Goal: Prove the system works under real conditions            │
│                                                                 │
└────────────────────────────┬────────────────────────────────────┘
                             │
                      ▼ GATE 2 REVIEW
                             │
┌─────────────────────────────────────────────────────────────────┐
│                    STAGE 3: PRODUCTION                          │
│                                                                 │
│  Who: Full target user base                                    │
│  Data: Full production data within approved classification     │
│  Duration: Ongoing                                             │
│  Goal: Sustained performance at scale                          │
│                                                                 │
└────────────────────────────┬────────────────────────────────────┘
                             │
                      ▼ QUARTERLY REVIEW
```

---

## Stage 1: Internal Deployment

### Objective

Validate that the system performs as designed under controlled conditions. Identify failure modes before real data or real users are involved.

### Scope

- **Users:** Engineering team members and the AI Governance Lead only
- **Data:** Synthetic datasets and anonymised historical data
- **Environment:** Isolated — no connection to production systems or real user data
- **Monitoring:** Full — every query, every output, every confidence score logged

### Activities

| Week | Activity | Owner |
|---|---|---|
| Week 1 | System deployment in internal environment. Baseline accuracy measurement. | Engineering Lead |
| Week 1–2 | Team usage: structured test scenarios covering all intended use cases | All team members |
| Week 2–3 | Edge case testing: adversarial inputs, out-of-scope queries, boundary conditions | AI Governance Lead |
| Week 3–4 | Performance testing: volume, latency, concurrent user simulation | Engineering Lead |
| Week 4 | Gate 1 documentation and review preparation | Engineering Director |

### Gate 1 Criteria

All of the following must be met to advance to Stage 2:

| Criterion | Threshold | Measurement Method |
|---|---|---|
| Accuracy on intended use cases | ≥ 85% | Structured evaluation set: 100 test scenarios |
| Confidence score calibration | Scores correlate with accuracy (r ≥ 0.7) | Score vs. outcome analysis |
| No data classification breach | Zero incidents | Log review |
| Rollback procedure tested | Documented and tested successfully | Runbook + test evidence |
| HITL routing functional | All sub-threshold outputs correctly routed | Functional test results |
| Team trained on HITL protocol | 100% of operational team | Training completion records |

**Gate 1 is a Go / No-Go decision.** All criteria must pass. A partial pass is a No-Go.

**Gate 1 review attendees:** Engineering Director, AI Governance Lead, Engineering Lead for the system.

---

## Stage 2: Limited Deployment

### Objective

Validate that the system performs reliably under real-world conditions — real users, real queries, real data — with a controlled and reversible scope.

### Scope

- **Users:** Defined pilot group — maximum 20% of intended target user base
- **Data:** Real production data within the approved data classification tier
- **Environment:** Production-grade infrastructure (not a sandbox) — but with deployment scope limited to pilot group
- **Monitoring:** Full — with weekly review of key metrics

### The Pilot User Group

The pilot group should be:
- **Representative:** Covering the range of use cases the full deployment will encounter
- **Informed:** Users know they are in a pilot and are expected to provide feedback
- **Supported:** Direct access to a feedback channel and a named point of contact
- **Not self-selected:** Avoid selecting only enthusiastic adopters; include sceptics

### Activities

| Period | Activity | Owner |
|---|---|---|
| Week 1 | Pilot launch. Onboarding of pilot users. Feedback channel open. | Engineering Lead |
| Week 1–4 | Daily metric monitoring. Weekly review of HITL rate, accuracy, feedback. | AI Governance Lead |
| Week 4 | Mid-pilot review. Adjustments made if metrics are off-track. | Engineering Director |
| Week 5–8 | Continued monitoring. Feedback synthesis. Gate 2 documentation. | AI Governance Lead |
| Week 8 | Gate 2 review. Go / No-Go for production. | Engineering Director |

### Gate 2 Criteria

| Criterion | Threshold | Measurement Method |
|---|---|---|
| Accuracy maintained from Stage 1 | Within 5% of Stage 1 baseline | Structured evaluation set: same 100 scenarios |
| HITL escalation rate | ≤ 25% of all outputs | Production log analysis |
| User satisfaction (pilot group) | ≥ 70% positive feedback | Structured pilot survey |
| Zero critical incidents | Zero P1 AI incidents in Stage 2 | Incident log |
| Latency within SLA | ≤ defined response time for 95th percentile | Performance monitoring |
| Monitoring and alerting operational | All key metrics triggering alerts at thresholds | Alert test results |
| Rollback executed successfully (drill) | Full rollback and restore completed in ≤ 30 minutes | Drill documentation |

**Gate 2 review attendees:** Engineering Director, AI Governance Lead, Engineering Lead, Business Stakeholder.

**The business stakeholder's role at Gate 2:** They confirm that the system's output quality meets the business standard. Technical metrics passing is necessary but not sufficient — the business stakeholder must agree the outputs are fit for purpose.

---

## Stage 3: Production Deployment

### Objective

Full deployment to the target user base, with sustained monitoring, continuous improvement, and quarterly governance review.

### Scope

- **Users:** Full target population
- **Data:** Full production data within approved classification
- **Environment:** Production
- **Monitoring:** Ongoing — automated alerting on key metrics

### Production Monitoring Framework

| Metric | Threshold | Alert Condition | Response |
|---|---|---|---|
| Accuracy (weekly sample) | ≥ Stage 2 baseline | Drop > 5% from baseline | Immediate investigation; consider rollback if > 10% |
| HITL rate | ≤ 25% | > 30% for 3 consecutive days | Model review; possible retraining trigger |
| Latency (p95) | ≤ SLA | Breach for > 1 hour | P2 incident raised |
| Error rate | ≤ 1% | > 2% | P2 incident raised |
| User-reported issues | < 5 per week | > 10 in a week | Structured review; user communication |

### Continuous Improvement Cycle

Production AI systems are not static. They require ongoing investment to maintain and improve performance.

**Monthly activities:**
- Review of HITL logs: what query patterns are triggering escalation? Do they indicate a model gap?
- User feedback synthesis: are users getting value? Are there emerging use cases?
- Accuracy sample testing: run the 100-scenario evaluation set monthly to track drift

**Quarterly activities:**
- Full governance review: does the system still meet all Gate 2 criteria?
- Data classification review: has the data being processed changed in nature?
- Tool vendor review: any changes to the vendor's data handling, model, or terms?
- ROI validation: are the FTE-hours savings being realised as projected?

### Rollback Conditions

A production AI system is rolled back when any of the following occur:

| Condition | Rollback Type |
|---|---|
| P1 AI incident (system producing harmful or seriously incorrect outputs at scale) | Immediate full rollback |
| Accuracy drop > 15% from Stage 2 baseline | Rollback within 4 hours; investigation before re-deployment |
| HITL rate > 40% sustained for 5+ days | Partial rollback (reduce deployment scope); full investigation |
| Data classification breach (system accessing or processing data above its approved tier) | Immediate full rollback; security incident raised |
| Vendor data handling policy change (training on inputs enabled) | Immediate suspension; legal review before re-activation |

**Rollback is not failure.** A rollback that is executed cleanly, quickly, and with clear stakeholder communication is a governance success — evidence that the system is being operated responsibly.

---

## Deployment Decision Matrix

Use this matrix when a deployment decision is ambiguous:

| Risk Level | Stage 2 Criteria Met? | Business Value | Decision |
|---|---|---|---|
| Low | Yes | High | Proceed to Production |
| Low | Yes | Moderate | Proceed with enhanced monitoring |
| Low | Partially | Any | Extend Stage 2; address gaps |
| Medium | Yes | High | Proceed with explicit rollback readiness confirmation |
| Medium | Yes | Moderate | Extend Stage 2; add pilot users |
| Medium | No | Any | Do not proceed; return to Stage 1 if necessary |
| High | Yes | Any | Executive sign-off required before Production |
| High | Partially | Any | Do not proceed |

---

## Key Principle

> *The purpose of staged deployment is not to slow down AI adoption. It is to ensure that when AI reaches full production, it is ready — and that the organisation is ready for it. A system that reaches production through governance-controlled stages arrives with evidence of reliability, not hope.*

---

*Part of the [AI Governance Framework](./README.md) series.*
