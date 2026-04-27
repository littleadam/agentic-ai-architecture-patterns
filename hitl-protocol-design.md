# HITL Protocol Design
### Agentic AI Architecture Patterns | Human Oversight

---

## What HITL Is — and What It Is Not

**HITL — Human-in-the-Loop — is not a fallback for a broken AI system.**

It is a deliberate architectural decision that acknowledges a fundamental truth about agentic AI: there are classes of decisions and query types for which AI-only responses are insufficient, inappropriate, or unsafe — and for which human judgment must remain in the loop.

The organisations that get HITL right treat it as a product feature, not a safety net. They design it deliberately, staff it operationally, measure it continuously, and improve the AI system using the intelligence that HITL generates.

The organisations that get HITL wrong treat it as an embarrassment — a sign that the AI isn't good enough yet. They under-invest in it, set thresholds too high (routing too little to human review), and discover its absence only when a harmful output reaches a user at scale.

This document builds the HITL protocol that gets it right.

---

## The Four HITL Routing Triggers

HITL is triggered by four distinct conditions, each requiring different handling:

### Trigger 1: Low Confidence Score

The AI system generates a response but assesses its own confidence below the auto-respond threshold.

**What this means:** The system has attempted to answer but is uncertain — either because the query is at the edge of its knowledge, the retrieved context was insufficient, or the query classification was ambiguous.

**Human role:** Review the AI-generated draft. The human can: approve it as-is, edit it before delivery, or discard it and respond independently.

**Design principle:** The human reviewer sees the AI draft. This accelerates their response — they are editing, not starting from scratch. The AI draft is a first attempt, not a finished product.

---

### Trigger 2: Context Insufficiency (Knowledge Gap)

The context assessment stage (Stage 3) determines that the retrieved information is insufficient to answer the query reliably.

**What this means:** The knowledge base does not contain the information needed. This is not a model failure — it is a knowledge gap.

**Human role:** Answer the query directly. The system routes the query to a human with a flag: *"Knowledge base gap detected for this query type."*

**Design principle:** Every knowledge gap is logged and reviewed weekly. A recurring knowledge gap is a signal to update the knowledge base — not a signal to tolerate repeated human handling.

---

### Trigger 3: Sensitivity Classification

The query classification stage (Stage 1) identifies the query as high-sensitivity — regardless of the confidence score.

**What this means:** Certain query types — regulatory, legal, medical, safety-related, complaint-related — must always involve human judgment. The AI may generate a response, but it does not reach the user without human review.

**Human role:** Review and approve — or rewrite — the AI response before delivery. The AI draft may still add value as a starting point, but human judgment is mandatory.

**Design principle:** Sensitivity thresholds are defined by the Director and business stakeholder — not by the engineering team. They reflect business risk tolerance and regulatory obligation.

---

### Trigger 4: Out-of-Scope Detection

The query classification stage determines the query is outside the system's defined scope.

**What this means:** The user is asking something the system was not designed to answer. Attempting to answer risks hallucination or misleading deflection.

**Human role:** Receive the routed query and respond directly, or confirm the deflection message is appropriate.

**Design principle:** Out-of-scope deflection messages are written in advance and tested for user experience before deployment. An abrupt "I can't help with that" is a product failure. A warm, specific redirect is a product feature.

---

## The HITL Queue: Operational Design

### Queue Architecture

The HITL queue is the operational backbone of the HITL protocol. Every triggered response enters the queue and must be processed within the defined SLA.

**Queue fields for each item:**

| Field | Description |
|---|---|
| Query ID | Unique identifier |
| Trigger type | Which of the four triggers caused the routing |
| Confidence score | The score that triggered routing (if Trigger 1) |
| AI-generated draft | The draft response (if generated) |
| Retrieved context | The documents retrieved by the system |
| Classification | Intent, domain, sensitivity from Stage 1 |
| Timestamp | When the query arrived in the queue |
| SLA deadline | When the response must be delivered |
| Assigned reviewer | Who is handling this item |
| Status | Pending / In Review / Responded / Escalated |

### Queue SLA Design

The queue SLA is a product decision — agreed with the business stakeholder before deployment. It must be operationally achievable given the staffing model.

| Queue Type | Recommended SLA | Staffing Implication |
|---|---|---|
| Standard review (Trigger 1 — low confidence) | ≤ 4 business hours | Part-time reviewer pool |
| Knowledge gap (Trigger 2) | ≤ 2 business hours | Subject matter expert access |
| High sensitivity (Trigger 3) | ≤ 1 business hour | Dedicated reviewer on shift |
| Out of scope (Trigger 4) | ≤ 8 business hours | Standard support team |
| Live escalation (Trigger 1 — very low confidence) | ≤ 5 minutes | Live agent coverage required |

**The Director's question to ask before deployment:** If the HITL rate is 20%, and the system handles 500 queries per day, that is 100 HITL items per day. Can the reviewer team handle 100 items at the defined SLA? If no — the SLA, the staffing model, or the confidence threshold must change before launch.

### Reviewer Training

HITL reviewers are not passive approvers. They are active quality controllers whose decisions directly influence user experience and whose logged actions feed the AI improvement cycle.

**Reviewer training must cover:**

1. **Understanding the AI draft:** How was this response generated? What does the confidence score mean? Why was it routed?
2. **Editing principles:** How to improve an AI draft without rewriting it unnecessarily. How to recognise and correct hallucinations. How to maintain the system's persona.
3. **Escalation criteria:** When to escalate beyond the reviewer (regulatory obligation, complaint, potential legal exposure).
4. **Logging discipline:** Every reviewer action is logged with a decision code. The codes feed the AI improvement analysis.
5. **Feedback channel:** How to flag systematic AI failures — patterns that suggest a model gap or knowledge base deficiency.

---

## Confidence Score Threshold Design

### Why Thresholds Are Not Universal

The confidence threshold that determines HITL routing is not a technical constant. It is a business decision that reflects:

- **Risk tolerance:** How wrong can the system be before the business impact is unacceptable?
- **User impact:** What happens to a user who receives an incorrect auto-response?
- **Cost of HITL:** What is the operational cost of routing to human review at the current threshold?

These three factors must be balanced by the Director and business stakeholder — not set by the engineering team.

### Threshold Design by Risk Profile

| Use Case Risk Profile | Recommended Auto-Respond Threshold | Rationale |
|---|---|---|
| **Low risk** — internal productivity tools, information retrieval for trained users | ≥ 0.75 | Users can identify errors; impact of wrong answer is low |
| **Medium risk** — customer-facing information, HR queries, product support | ≥ 0.85 | Users may act on responses; moderate impact of error |
| **High risk** — regulatory information, compliance queries, financial guidance | ≥ 0.92 | High impact of error; regulatory or legal consequence possible |
| **Critical risk** — medical, safety, crisis response | Human-in-loop always | No auto-respond regardless of confidence score |

### Threshold Adjustment Protocol

Thresholds are not set and forgotten. They are reviewed and adjusted based on production data.

**Lower the threshold (route more to HITL) when:**
- Accuracy on auto-responded queries drops below the Stage 2 baseline
- User-reported issues cluster in the 0.75–0.85 confidence band
- An AI incident involved an auto-responded query with a borderline confidence score

**Raise the threshold (route less to HITL) when:**
- Human reviewer approval rate on HITL queue items is consistently > 85% (reviewers are approving AI drafts as-is — the threshold is too conservative)
- HITL queue SLA is being breached due to volume (staffing cannot keep up with HITL rate)
- Model retraining has demonstrably improved accuracy in the routed confidence band

**Threshold changes are governed changes.** Every adjustment is documented, approved by the Director, communicated to the operations team, and monitored for impact in the two weeks following the change.

---

## Using HITL Data for AI Improvement

The HITL queue is not just an operational necessity. It is the most valuable source of AI improvement signal in the entire system.

Every HITL interaction generates data:
- What query triggered HITL?
- What did the AI generate?
- What did the human reviewer decide?
- Did the reviewer approve, edit, or discard the AI draft?
- If edited, what was changed?

This data answers the questions that drive AI improvement:

### Analysis 1: Query Clustering
**Question:** What types of queries are consistently triggering HITL?

If the same query type appears in HITL week after week, it is a signal of one of three things:
1. A knowledge base gap — the information to answer this query is not in the system
2. A model performance gap — the model is not handling this query type reliably
3. A scope definition gap — this query type should be explicitly out of scope

Each of these has a different resolution. The clustering analysis identifies which.

### Analysis 2: Draft Quality Scoring
**Question:** What percentage of AI drafts are being approved as-is versus significantly edited?

| Approval Rate | Signal |
|---|---|
| > 80% approved as-is | Threshold may be too conservative — this content could be auto-responded |
| 50–80% approved with minor edits | HITL is functioning correctly — adding value without significant friction |
| < 50% approved; majority significantly edited or discarded | AI performance is poor for this query type — investigate root cause |

### Analysis 3: Hallucination Mapping
**Question:** Where in the knowledge domain is the system hallucinating?

Every discarded AI draft (where the reviewer responded independently) is a confirmed failure. Map these against query type and knowledge base coverage. Patterns indicate hallucination-prone domains — candidates for knowledge base update, prompt refinement, or scope exclusion.

### The Weekly HITL Review

The AI Governance Lead conducts a weekly HITL review covering:

1. HITL rate this week versus last week (trend)
2. Queue SLA compliance rate
3. Top 3 query types triggering HITL this week
4. Draft approval rate this week
5. Any new hallucination patterns identified
6. Recommended action: knowledge base update / threshold adjustment / scope clarification

Output: a one-page weekly report distributed to the Engineering Director and business stakeholder.

---

## Preventing Hallucinations from Reaching Users

The HITL protocol is the last line of defence against hallucinations reaching users. But it should not be the only line.

**Defence in depth — hallucination prevention at each pipeline stage:**

| Stage | Hallucination Prevention Mechanism |
|---|---|
| Stage 2 — Retrieval | Retrieval quality threshold: if relevance score < minimum, flag as knowledge gap before generation |
| Stage 3 — Context Assessment | Consistency check: if retrieved documents contradict each other, route to HITL before generation |
| Stage 4 — Generation | System prompt constraint: "Answer only using the provided context. If the context does not support the answer, say so." |
| Stage 5 — Confidence Scoring | Low confidence routing: responses generated without strong contextual support produce lower confidence scores |
| Stage 6 — HITL Routing | Final gate: low-confidence responses never auto-respond |

**The principle:** A hallucination that enters the pipeline should encounter resistance at every subsequent stage. The HITL protocol is the last resistance — not the only one.

---

## HITL Metrics Dashboard

The Director maintains visibility of HITL health through a weekly metrics snapshot:

```
HITL WEEKLY METRICS
Week of: [Date]
System: [System Name]

VOLUME
Total queries this week:          ______
Auto-responded:                   ______ (___%)
Routed to HITL:                   ______ (___%)
  └─ Low confidence:              ______ (___%)
  └─ Knowledge gap:               ______ (___%)
  └─ High sensitivity:            ______ (___%)
  └─ Out of scope:                ______ (___%)

QUEUE PERFORMANCE
Queue SLA compliance:             ____%  (target: ≥ 95%)
Average queue resolution time:    ______ hours

QUALITY
Draft approval rate (as-is):      ____%
Draft approval rate (with edits): ____%
Draft discard rate:               ____%

TREND
HITL rate vs last week:           ↑ / ↓ / → by ____%
Draft approval rate vs last week: ↑ / ↓ / → by ____%

TOP HITL QUERY TYPES THIS WEEK
1. [Query type] — [count] — [pattern or action]
2. [Query type] — [count] — [pattern or action]
3. [Query type] — [count] — [pattern or action]

RECOMMENDED ACTIONS
[ ] Knowledge base update: [topic]
[ ] Threshold review: [direction and rationale]
[ ] Scope clarification: [query type]
[ ] Reviewer training: [topic]
```

---

## Key Principle

> *HITL is not a confession that the AI isn't good enough. It is a declaration that some decisions are important enough to warrant human judgment — and that a well-designed system knows the difference. The organisations that treat HITL as a product feature — investing in its operations, learning from its data, and improving both the AI and the human process continuously — are the organisations whose AI systems earn trust over time.*

---

*Part of the [Agentic AI Architecture Patterns](./README.md) series.*
