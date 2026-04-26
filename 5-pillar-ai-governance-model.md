# 5-Pillar AI Governance Model
### AI Governance Framework | Core Architecture

---

## Overview

Enterprise AI governance requires five capabilities working in concert. A programme that builds only one or two of these pillars will eventually fail — at the compliance gate, the production deployment review, or the stakeholder trust audit.

The five pillars are:

| Pillar | What It Governs |
|---|---|
| [Pillar 1 — Tool Approval](#pillar-1--tool-approval) | Which AI tools are permitted for use and under what conditions |
| [Pillar 2 — Data Security Standards](#pillar-2--data-security-standards) | What data can flow into AI systems and how it must be protected |
| [Pillar 3 — Staged Deployment Gates](#pillar-3--staged-deployment-gates) | How AI moves from experiment to production with controlled risk |
| [Pillar 4 — Human-in-the-Loop Protocol](#pillar-4--human-in-the-loop-protocol) | When AI output requires human review before action |
| [Pillar 5 — ROI and Impact Tracking](#pillar-5--roi-and-impact-tracking) | How AI value is measured, reported, and sustained |

These pillars are not sequential. They operate simultaneously across every AI initiative in the programme.

---

## Pillar 1 — Tool Approval

### The Problem This Pillar Solves

Without a tool approval process, engineers adopt AI tools based on convenience and individual preference. Within months, the organisation is running dozens of unreviewed tools — with unknown data handling practices, unknown security postures, and unknown contractual implications.

When an audit happens, or when a data incident occurs, there is no registry, no policy, and no accountability.

### The Approved Tool Register

Every AI tool used in the organisation must appear in the Approved Tool Register before any engineer uses it in a work context.

**Register structure:**

| Field | Description |
|---|---|
| Tool Name | Commercial name and version |
| Approval Status | Approved / Conditional / Under Review / Rejected |
| Approved Use Cases | What the tool is permitted to be used for |
| Data Classification Permitted | What data classifications can be used with this tool |
| Security Review Date | When the security assessment was last completed |
| Review Owner | Who is accountable for the next review |
| Next Review Date | Scheduled reassessment date |
| Conditions | Any restrictions on use |

### The Approval Workflow

```
Engineer identifies AI tool for potential use
           ↓
Submits Tool Evaluation Request (TER) to AI Governance Lead
           ↓
AI Tool Evaluation Scorecard completed (see separate document)
           ↓
Security review: data handling, API terms, model training policies
           ↓
Legal review: contractual implications, IP ownership of outputs
           ↓
Decision: Approved / Conditional / Rejected
           ↓
Tool added to register with conditions documented
           ↓
Team communication: approved tools list updated
           ↓
Quarterly re-review scheduled
```

### Approval Categories

**Approved — Unrestricted:** Tool has passed full security and legal review. Permitted for use with all approved data classifications. No additional conditions.

**Approved — Conditional:** Tool has passed review with specific conditions. Example: permitted for use with anonymised data only; not permitted for use with customer-identifiable information.

**Under Review:** Evaluation in progress. Tool may not be used until review is complete.

**Rejected:** Tool failed security, legal, or compliance review. May not be used. Rejection reason documented for reference.

### Enforcement

Tool approval is a governance control, not a suggestion. Use of unapproved tools in work contexts constitutes a policy breach — addressed through the standard performance management process.

The governance lead conducts quarterly spot-checks: reviewing team activity logs and conducting 1:1 interviews with TLs to identify unapproved tool usage. The goal is education first, enforcement second.

---

## Pillar 2 — Data Security Standards

### Data Classification for AI Contexts

AI tools introduce a specific risk that traditional data security frameworks do not fully address: **the risk of data leaving the organisation through model inputs.**

Every AI tool that processes input data potentially sends that data to an external model — where it may be used for training, retained in logs, or accessible to the tool vendor.

This pillar classifies data and defines what may flow into AI systems.

**Data Classification Tiers:**

| Tier | Definition | AI Tool Usage |
|---|---|---|
| **Public** | Information already in the public domain. No confidentiality requirement. | Permitted with any approved AI tool |
| **Internal** | Information for internal use only. Not confidential but not public. | Permitted with approved tools. Anonymise where practical. |
| **Confidential** | Sensitive business, technical, or operational information. | Permitted only with tools that have explicit no-training, no-retention contractual guarantees |
| **Restricted** | Customer PII, regulated data, security credentials, source code under NDA | Not permitted in external AI tools. Internal/on-premise models only. |

### Prompt Engineering Standards for Data Security

Engineers working with AI tools must follow these standards when constructing prompts:

1. **Anonymise before prompting.** Replace names, IDs, addresses, and identifiable references with placeholders before submitting to an AI tool.
2. **Never include credentials in prompts.** API keys, passwords, tokens — never in AI input.
3. **Never include customer-identifiable data in prompts.** Summarise the scenario; do not paste the raw record.
4. **Review AI-generated output before sharing.** AI tools can hallucinate. Outputs must be reviewed by the engineer before being used, shared, or committed.

### Vendor Data Handling Assessment

Before any tool enters the approval process, the following vendor data handling questions must be answered — from the vendor's published documentation or contractual terms:

- Does the vendor use input data for model training? (Yes / No / Opt-out available)
- How long is input data retained? (Duration)
- Is data encrypted in transit and at rest? (Yes / No)
- Is there a Data Processing Agreement (DPA) available? (Yes / No)
- Is the vendor compliant with relevant data protection regulations? (List certifications)

A tool that uses input data for training without opt-out is not approved for any data tier above Public.

---

## Pillar 3 — Staged Deployment Gates

AI deployment is not a single event. It is a controlled progression through stages — each with explicit success criteria that must be met before advancing.

*(See [Staged AI Deployment Model](./staged-ai-deployment-model.md) for the full framework.)*

**The three stages at a glance:**

```
STAGE 1: INTERNAL (Team-only)
Goal: Prove the system works under controlled conditions
Gate: Accuracy ≥ threshold, no data issues, team trained
Duration: 2–4 weeks

          ↓  GATE 1 REVIEW

STAGE 2: LIMITED (Defined user group)
Goal: Prove the system works under real-world load and feedback
Gate: Accuracy maintained, HITL escalation rate acceptable, feedback positive
Duration: 4–8 weeks

          ↓  GATE 2 REVIEW

STAGE 3: PRODUCTION (Full deployment)
Goal: Sustained performance at scale with full monitoring
Gate: All Stage 2 gates met for 2+ consecutive weeks
Duration: Ongoing

          ↓  QUARTERLY REVIEW
```

Each gate is a formal review — attended by the AI Governance Lead, the Engineering Director, and the product/business stakeholder. The gate decision is documented. No system advances without documented gate approval.

---

## Pillar 4 — Human-in-the-Loop Protocol

### The Core Principle

AI systems should be designed to *assist* human judgment — not replace it for high-stakes decisions.

The Human-in-the-Loop (HITL) protocol defines the conditions under which an AI system must route to a human reviewer before taking action or delivering output to an end user.

### Confidence Score Framework

Every AI system in production must implement a confidence scoring mechanism. The system assesses its own certainty for each output and routes accordingly.

**Standard confidence tiers:**

| Confidence Score | Action | Rationale |
|---|---|---|
| ≥ 0.90 | Auto-respond | High confidence. System has seen this pattern reliably. |
| 0.75 – 0.89 | Auto-respond with disclaimer | Moderate confidence. Output delivered with a note indicating the response is AI-generated and should be verified. |
| 0.60 – 0.74 | Queue for human review | Below acceptable auto-response threshold. Human reviews and approves before delivery. |
| < 0.60 | Escalate to human immediately | Low confidence. Do not auto-respond. Route directly to human agent with full context. |

**Thresholds are not universal.** High-stakes domains (medical, legal, financial, security) require higher confidence thresholds. The appropriate threshold for each system is defined at the Gate 1 review and documented in the system's governance record.

### HITL Escalation Path

```
AI System generates output
         ↓
Confidence score assessed
         ↓
Score ≥ 0.90? → Auto-respond
         ↓
Score 0.75–0.89? → Auto-respond + disclaimer
         ↓
Score 0.60–0.74? → Queue for human review → Human approves/edits → Deliver
         ↓
Score < 0.60? → Escalate → Human agent notified → Human responds directly
         ↓
All HITL interventions logged → Weekly HITL rate review
```

### HITL Rate as a Governance Metric

The percentage of outputs that trigger HITL review is tracked weekly.

| HITL Rate | Signal | Action |
|---|---|---|
| Declining | System improving; confidence increasing | Maintain current model; review threshold |
| Stable | System performing consistently | Normal monitoring |
| Rising | System degrading or encountering new query patterns | Investigate; consider model retraining or threshold adjustment |
| > 40% | System is not ready for current deployment scope | Review deployment stage; consider rollback |

---

## Pillar 5 — ROI and Impact Tracking

### Why AI ROI Measurement Is Different

Traditional software ROI is relatively straightforward: measure the cost of building it versus the business value it delivers.

AI ROI has an additional layer: the value of AI initiatives is often expressed as *avoided cost* — work that was not done by a human because the AI did it. This is harder to measure and easier to exaggerate.

This pillar establishes a rigorous, consistent methodology for measuring and reporting AI programme impact.

### The FTE-Hours Savings Model

**Step 1: Establish the baseline.**
For each task that an AI tool addresses, measure the current human time investment. This must be measured — not estimated.

```
Baseline Task Measurement
Task: [Task name]
Frequency: [How many times per day/week/month]
Average human time per instance: [Minutes]
Monthly human time total: Frequency × Time per instance
```

**Step 2: Measure AI-assisted time.**
After deployment, measure the actual time for the same task with AI assistance.

```
AI-Assisted Task Measurement
Task: [Same task]
Frequency: [Same frequency — or higher if AI enables more throughput]
Average human time per instance with AI: [Minutes]
Monthly human time total with AI: Frequency × Time per instance
```

**Step 3: Calculate savings.**

```
Monthly Hours Saved = Baseline Monthly Hours − AI-Assisted Monthly Hours
Annual Hours Saved = Monthly Hours Saved × 12
FTE Equivalent = Annual Hours Saved ÷ 2080 (standard working hours per year)
Financial Equivalent = FTE Equivalent × Average fully-loaded engineer cost
```

**Step 4: Validate the savings.**
Savings are not confirmed until a second measurement is taken 4 weeks after deployment. First-week measurements are excluded — they reflect novelty effect, not sustainable productivity.

### Programme-Level Reporting

AI programme ROI is reported at two levels:

**Monthly — Director level:**
- Tools deployed this month
- FTE-hours saved this month (validated)
- Cumulative FTE-hours saved (programme to date)
- HITL rate trend (all production AI systems)
- Any AI incidents this month

**Quarterly — Executive level:**
- AI tool adoption rate (% of engineers actively using approved tools)
- FTE-hours saved vs target
- Programme self-funding timeline (when savings cover programme investment)
- New tools pipeline (approved, in development, planned)
- AI training compliance rate

---

## Governance Operating Rhythm

| Cadence | Activity | Owner |
|---|---|---|
| Weekly | HITL rate review, active incident check | AI Governance Lead |
| Fortnightly | AI programme PI review — team experiment updates | Engineering Director |
| Monthly | ROI report, tool register review | Engineering Director |
| Quarterly | Tool re-evaluation, compliance audit, executive report | Engineering Director + Legal |
| Annually | Full governance framework review against ISO/IEC 42001 | Engineering Director + Compliance |

---

## Key Principle

> *Five pillars. One system. Governance that works is governance that is visible, consistent, and operated — not documented and forgotten. The purpose of this framework is not to produce documents. It is to produce AI that works reliably, safely, and with measurable value — at enterprise scale.*

---

*Part of the [AI Governance Framework](./README.md) series.*
