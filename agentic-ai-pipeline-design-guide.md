# Agentic AI Pipeline Design Guide
### Agentic AI Architecture Patterns | Core Architecture

---

## What Is an Agentic AI Pipeline

A traditional AI integration is transactional: a user sends a query, the model returns a response. There is one step, one decision, one output.

An agentic AI pipeline is compositional: a user sends a query, and the system executes a sequence of steps — each building on the previous — before producing an output. The system can retrieve information, assess context, evaluate its own confidence, and decide whether a human needs to be involved — all before the user sees a response.

This compositionality is what makes agentic AI powerful. It is also what makes it complex to design, govern, and maintain.

**The Director's responsibility:** Understand each stage of the pipeline well enough to make architectural decisions, define acceptance criteria, and govern the system in production — without needing to implement the pipeline personally.

---

## The 6-Stage Pipeline

```
USER QUERY
    │
    ▼
┌─────────────────────────────┐
│  STAGE 1: QUERY             │
│  CLASSIFICATION             │
│                             │
│  What is the user asking?   │
│  Is this in scope?          │
│  What pipeline path?        │
└──────────────┬──────────────┘
               │
    ┌──────────┴──────────┐
    │                     │
    ▼                     ▼
 In Scope             Out of Scope
    │                     │
    │              Graceful deflection
    │              (see Stage 6 routing)
    ▼
┌─────────────────────────────┐
│  STAGE 2: RETRIEVAL         │
│                             │
│  What information does the  │
│  system need to answer this?│
│  Where does it come from?   │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  STAGE 3: CONTEXT           │
│  ASSESSMENT                 │
│                             │
│  Is the retrieved           │
│  information sufficient?    │
│  Is it current?             │
│  Is it relevant?            │
└──────────────┬──────────────┘
               │
    ┌──────────┴──────────┐
    │                     │
    ▼                     ▼
Sufficient          Insufficient
    │                     │
    │              Escalate to HITL
    │              (knowledge gap)
    ▼
┌─────────────────────────────┐
│  STAGE 4: RESPONSE          │
│  GENERATION                 │
│                             │
│  Generate response using    │
│  retrieved context and      │
│  user query                 │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  STAGE 5: CONFIDENCE        │
│  SCORING                    │
│                             │
│  How confident is the       │
│  system in this response?   │
│  Score: 0.0 → 1.0           │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  STAGE 6: OUTPUT ROUTING    │
│                             │
│  Based on confidence score: │
│  Auto-respond / Review /    │
│  Escalate / Deflect         │
└──────────────┬──────────────┘
               │
    ┌──────────┼──────────┐
    │          │          │
    ▼          ▼          ▼
Auto-       Human      Escalate
Respond     Review     to Agent
```

---

## Stage 1: Query Classification

### Purpose

Before the system does anything else, it must understand what the user is asking — and whether it is capable of answering it.

Query classification is the pipeline's gatekeeper. A system without classification attempts to answer everything, fails unpredictably, and produces a HITL rate that cannot be explained or improved.

### Classification Dimensions

**Dimension 1 — Intent**
What is the user trying to accomplish?

| Intent Category | Description | Pipeline Path |
|---|---|---|
| Information request | User wants to know something | Standard pipeline |
| Action request | User wants the system to do something | Extended pipeline with action confirmation step |
| Clarification | User's intent is ambiguous | Clarification micro-loop (ask one question, re-classify) |
| Complaint / feedback | User is expressing dissatisfaction | Sentiment-aware path; possible human routing |
| Out of scope | Request outside the system's design | Graceful deflection with redirection |

**Dimension 2 — Domain**
Does this query fall within the system's knowledge domain?

| Domain Status | Description | Action |
|---|---|---|
| In domain | Query topic is covered by the system's knowledge base | Proceed to Stage 2 |
| Partial domain | Query partially covered; gaps exist | Proceed with context gap flagged |
| Out of domain | Query topic is outside the knowledge base | Route to graceful deflection — do not attempt to answer |

**Dimension 3 — Sensitivity**
Does this query involve topics that require elevated care?

| Sensitivity Level | Description | Handling |
|---|---|---|
| Standard | No elevated risk | Standard pipeline |
| Elevated | Query involves regulatory, legal, medical, or financial topics | HITL threshold raised for this query |
| High | Query involves crisis, complaint, or escalation | Immediate human routing regardless of confidence score |

### Design Decisions at Stage 1

The Director makes these architectural decisions at Stage 1:

1. **What is in scope for this system?** This is a product decision, not a model decision. Define it precisely — and enforce it at classification.
2. **What happens to out-of-scope queries?** Graceful deflection with a useful redirect is always better than an attempted answer that fails.
3. **What topics require elevated sensitivity handling?** Define these explicitly. Do not leave them to the model's judgment.

---

## Stage 2: Retrieval

### Purpose

The system retrieves the information it needs to answer the user's query. This stage defines the quality ceiling of the system's response — no generation stage can compensate for poor retrieval.

### Retrieval Architecture Options

| Pattern | Description | When To Use |
|---|---|---|
| **Vector Search (RAG)** | Query is converted to an embedding; semantically similar documents are retrieved from a vector database | Large, unstructured knowledge bases; when exact keyword match is insufficient |
| **Keyword Search** | Traditional text search against a structured knowledge base | Structured data; when precision is more important than recall |
| **Hybrid Search** | Combination of vector and keyword search; results are merged and re-ranked | Production systems where both precision and recall matter |
| **Structured Query** | System queries a database or API with structured parameters | When the answer exists in a database as a retrievable record |
| **No Retrieval (Parametric)** | System answers from model knowledge only | Only for queries where model knowledge is sufficient and current — rare in enterprise contexts |

### Retrieval Quality Standards

The Director should define retrieval quality standards that are measured and tracked:

| Standard | Measurement | Target |
|---|---|---|
| **Relevance** | % of retrieved documents rated relevant to the query by human review | ≥ 85% |
| **Coverage** | % of in-scope queries for which at least one relevant document is retrieved | ≥ 90% |
| **Latency** | Time from query receipt to retrieval completion | ≤ defined SLA (typically 1–2 seconds) |
| **Freshness** | Age of most recent document in knowledge base | Defined by use case — typically ≤ 30 days |

### Director's Decision: What Goes in the Knowledge Base?

This is a product ownership decision that belongs to the Director and business stakeholder — not the engineering team.

The knowledge base defines what the system knows. Define:
- What documents and data sources are included
- How frequently the knowledge base is updated
- Who is responsible for knowledge base quality
- What the process is for removing outdated or incorrect information

A knowledge base that is not actively maintained is a liability — it will produce outdated answers with high confidence.

---

## Stage 3: Context Assessment

### Purpose

Before generating a response, the system assesses whether the retrieved information is sufficient to answer the query reliably.

This is the stage that prevents the most dangerous failure mode: a system that generates a confident response when it actually does not have the information to answer correctly.

### Assessment Criteria

| Criterion | Pass Condition | Fail Condition |
|---|---|---|
| **Relevance sufficiency** | Retrieved documents directly address the query | Retrieved documents are tangentially related only |
| **Completeness** | Retrieved information covers the full scope of the query | Significant aspect of the query is not addressed |
| **Recency** | Retrieved information is current enough for the use case | Retrieved information is outdated relative to the use case requirements |
| **Consistency** | Retrieved documents agree on the key facts | Retrieved documents contradict each other |

### Context Assessment Outcomes

| Outcome | Action |
|---|---|
| All criteria pass | Proceed to Stage 4 — Response Generation |
| Relevance or completeness fail | Route to HITL — knowledge gap; human answers directly |
| Recency fail | Generate response with explicit recency caveat; flag for knowledge base update |
| Consistency fail | Route to HITL — conflicting information requires human judgment |

### Why This Stage Is Often Skipped — And Why It Should Not Be

Many agentic AI implementations skip context assessment and go directly from retrieval to generation. The pipeline is faster. The implementation is simpler.

The cost: the system generates responses from insufficient context — producing plausible-sounding but incorrect answers. These answers often have high confidence scores because the model does not know what it does not know.

Context assessment is the stage that catches this before the user sees it.

---

## Stage 4: Response Generation

### Purpose

The system generates a response using the query, the retrieved context, and a system prompt that defines its behaviour, constraints, and output format.

### The System Prompt as a Governance Document

The system prompt is the most important governance document in an agentic AI pipeline. It defines:

- The system's persona and communication style
- What the system is authorised to answer
- What the system must refuse to answer
- How the system should handle uncertainty
- What format responses should take
- What disclaimers or caveats must be included in specific scenarios

**The Director's responsibility:** Review and approve the system prompt before production deployment. The system prompt is not a technical artifact — it is a policy document. It defines how the system behaves with every user it interacts with.

**System prompt governance principles:**
1. The system prompt is version-controlled
2. Changes to the system prompt trigger a re-evaluation of Stage 2 accuracy measurements
3. The system prompt is reviewed quarterly — or immediately following any AI incident involving unexpected system behaviour

### Response Quality Standards

| Standard | Measurement | Target |
|---|---|---|
| Factual accuracy | % of responses rated factually correct by human review sample | ≥ 90% |
| Relevance to query | % of responses that directly address the user's stated intent | ≥ 95% |
| Format compliance | % of responses in the specified output format | ≥ 99% |
| Hallucination rate | % of responses containing factual claims not supported by retrieved context | ≤ 5% |

---

## Stage 5: Confidence Scoring

### Purpose

The system evaluates its own certainty in the generated response and produces a score from 0.0 to 1.0 that determines how the response is routed in Stage 6.

### Confidence Score Components

A robust confidence score is not a single model output. It is a composite of multiple signals:

| Signal | Weight | Description |
|---|---|---|
| **Retrieval relevance score** | 30% | How closely the retrieved documents matched the query |
| **Context completeness assessment** | 25% | Stage 3 outcome — how fully the context covered the query |
| **Model generation uncertainty** | 25% | The model's internal uncertainty in the generated response (available in some models as token probability) |
| **Query classification confidence** | 20% | How clearly the query was classified in Stage 1 |

**Composite confidence score = weighted average of component signals**

### Confidence Score Calibration

A confidence score is only valuable if it is calibrated — meaning a score of 0.85 should predict accuracy at approximately 85%.

Calibration is measured during Stage 2 deployment:
- Sample 200 outputs with known correct answers
- Plot confidence score against actual accuracy
- If scores are systematically overconfident (score 0.85 but accuracy 0.70), apply a calibration correction

Recalibrate quarterly in production.

---

## Stage 6: Output Routing

### Purpose

Based on the confidence score from Stage 5, the system routes the response to the appropriate output channel.

### Routing Decision Table

| Confidence Score | Route | User Experience |
|---|---|---|
| ≥ 0.90 | **Auto-respond** | Response delivered immediately. No delay, no disclaimer. |
| 0.75 – 0.89 | **Auto-respond with disclaimer** | Response delivered with: *"This response was generated by AI. Please verify before acting on it."* |
| 0.60 – 0.74 | **Queue for human review** | User receives: *"Your query requires a specialist response. A member of the team will respond within [SLA]."* Response goes to human review queue. |
| < 0.60 | **Escalate to human agent** | User receives: *"I'm connecting you with a specialist now."* Live handoff to human agent with full context. |
| Out of scope (Stage 1) | **Graceful deflection** | *"This is outside what I can help with. For [this topic], please contact [specific resource]."* |
| Context insufficient (Stage 3) | **Knowledge gap routing** | Same as < 0.60 — human agent receives the query and a flag indicating a knowledge base gap |

### Designing the User Experience at Each Route

The Director and product stakeholder define the user-facing language for each routing outcome. This is a product decision — not an engineering decision.

Design principles for routing messages:
- Never tell the user the confidence score was low. This destroys trust unnecessarily.
- Always give the user a clear next step — not a dead end.
- Maintain the system's persona across all routing outcomes.
- The human review queue must have a defined SLA — "a specialist will respond" without a timeframe is not a user experience, it is an absence of one.

---

## Pipeline Performance Measurement

The Director tracks pipeline performance at two levels:

**Stage-level metrics** (weekly):
- Stage 1: Classification accuracy; out-of-scope rate
- Stage 2: Retrieval relevance; coverage; latency
- Stage 3: Context assessment pass rate; knowledge gap rate
- Stage 4: Hallucination rate (sampled); format compliance
- Stage 5: Confidence score distribution; calibration accuracy
- Stage 6: Auto-respond rate; HITL rate; deflection rate

**System-level metrics** (monthly):
- End-to-end accuracy (sample of 100 queries with known correct answers)
- User satisfaction score
- HITL rate trend (is the system improving over time?)
- Average response latency (full pipeline)
- FTE-hours saved (compared to fully-human equivalent)

---

## Key Principle

> *An agentic AI pipeline is not a product feature. It is an operational system — with stages, decision rules, quality thresholds, and governance obligations at every step. The Director who understands each stage of this pipeline — and can make architectural decisions about it — is the Director who can govern it. Governance begins with comprehension.*

---

*Part of the [Agentic AI Architecture Patterns](./README.md) series.*
