# Voice360 — Five-Slide Capstone Presentation
**Quantum Shift AI Practitioner+ | Submission: 18-Aug-2026**

---

## SLIDE 1: Context and Why Now

**Title:** Insurance servicing must move from queues to conversations

---

### Challenges and Impact

| Challenge | Impact |
|---|---|
| Customers wait in call queues for routine questions (claim status, missing docs) | Long handling times, poor customer experience |
| Rigid IVR menus cannot understand context or complete actions | Calls escalate unnecessarily to human agents |
| Contact-center teams face rising demand, cost pressure, and high turnover | Agents spend time on repeatable low-complexity queries |
| Customers must repeat identity and context when transferred | Frustration for customers and agents alike |

---

### Strategic Imperative

- Customers expect **immediate, natural, always-available** service
- Human capacity should focus on **sensitive and complex** cases — judgment, disputes, vulnerability
- Generative AI + controlled automation can combine **conversation, knowledge, and action** in one interaction

---

### Shift in Operating Model

> **From:** Queue-based, agent-only servicing
> **To:** AI-assisted self-service with humans handling judgment and exceptions

**Suggested visual:** Before/after split — IVR queue tree on left | Direct conversational resolution + contextual handoff on right

---

## SLIDE 2: Current Challenges and Gaps

**Title:** Routine claim enquiries consume time but still deliver a fragmented experience

---

### Current State Workflow (6 Steps)

```
1. Customer calls insurer
       ↓ [WAIT]
2. Customer navigates IVR menu
       ↓ [WAIT]
3. Agent asks identity questions again
       ↓
4. Agent searches across multiple systems
       ↓
5. Agent explains claim status verbally
       ↓ [WAIT if transferred]
6. Customer repeats context to next agent
```

---

### Key Gaps

| Gap | Description |
|---|---|
| High manual effort | Repeatable enquiries consume agent time |
| Long wait and handling times | No 24/7 self-service for routine queries |
| Inconsistent explanations | Claim stages explained differently by different agents |
| Weak context transfer | Customer must repeat everything when transferred |
| Technology limitation | Traditional automation can route but cannot safely retrieve and explain individualized status |
| No grounded knowledge | Agents rely on personal recall for policy and process questions |

---

**Suggested visual:** Six-step journey map with clock/wait icons at steps 2, 4, and 6

---

## SLIDE 3: Vision and Future State

**Title:** Voice360 resolves routine needs and brings people in at the right moment

---

### What the MVP Enables

- Natural-language claim assistance — no menus, no navigation
- Approved, consistent answers to common process questions
- Secure retrieval of an existing claim **after identity verification**
- Clear missing-document and next-step guidance
- Callback creation with explicit customer confirmation
- Contextual human escalation — **context travels with the customer**

---

### Expected Strategic Outcomes

| Outcome | Mechanism |
|---|---|
| Faster answers, reduced customer effort | Immediate self-service for routine queries |
| Lower routine demand on contact-center agents | AI handles FAQ + status + callbacks |
| More consistent servicing | Answers grounded in approved knowledge only |
| Shorter handoff time | Structured summary passed automatically |
| Greater human focus on exceptions | AI flags fraud, vulnerability, complaints for escalation |

---

### Future State Operating Model

| AI Handles | People Handle |
|---|---|
| Intent recognition | Coverage and liability decisions |
| Approved FAQ responses | Formal complaints and legal cases |
| Identity verification flow | Customer vulnerability and bereavement |
| Claim data retrieval | Fraud investigation |
| Plain-language status explanation | Disputed decisions |
| Routine callback creation | Complex negotiations |
| Handoff summarization | Any case requiring judgment |

---

**Suggested visual:** Customer at centre → Voice360 on the routine path → Human specialist on the exception path

---

## SLIDE 4: Solution Overview

**Title:** A grounded Copilot Studio agent with controlled actions and human guardrails

---

### Architecture

```
                    ┌─────────────────────────────┐
                    │        Voice360              │
Customer ──────────▶│   Microsoft Copilot Studio   │──────▶ Human Agent / Callback
(Chat or Voice)     │   Generative Orchestration   │
                    └────────────┬────────────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              ▼                  ▼                   ▼
    ┌─────────────────┐ ┌──────────────────┐ ┌──────────────┐
    │ Approved Claim  │ │ Power Automate   │ │  Escalation  │
    │ Knowledge File  │ │  Agent Flows     │ │   Summary    │
    │ (04-knowledge)  │ │ VerifyCustomer   │ │   Flow       │
    └─────────────────┘ │ GetClaimStatus   │ └──────────────┘
                        │ CreateCallback   │
                        └────────┬─────────┘
                                 ▼
                    ┌────────────────────────┐
                    │ Voice360DemoData.xlsx   │
                    │ (Customers/Claims/      │
                    │  Callbacks — synthetic) │
                    └────────────────────────┘

  GitHub Copilot ··· supports design, flow expressions, test generation, documentation
```

---

### Key Capabilities

| Capability | How it works |
|---|---|
| Generative orchestration | Selects appropriate knowledge, topic, or action per turn |
| Grounded knowledge | Answers only from approved claim knowledge file — no web browsing |
| Deterministic verification | All three identity values must match before any data is revealed |
| Claim ownership check | Flow filters by both customer ID and claim number |
| Confirmation gate | Explicit user confirmation required before callback is created |
| Structured escalation | PrepareHandoffSummary captures full context before transfer |

---

### AI Tools Used

| Tool | Role in this project |
|---|---|
| **Microsoft Copilot Studio** | Conversational agent, generative orchestration, topic and action hosting |
| **GitHub Copilot** | Requirements refinement, Power Automate expression assistance, test case generation, security review, documentation — all uses recorded in evidence log |

---

## SLIDE 5: Business Flow and Role of AI

**Title:** One end-to-end journey from question to resolution or contextual handoff

---

### End-to-End Business Flow

```
Customer asks about claim
          │
          ▼
  AI recognises intent
          │
          ▼
   ┌──────────────┐
   │ General or   │
   │  Personal?   │
   └──────┬───────┘
          │
    ┌─────┴──────┐
    ▼            ▼
 General      Personal
 question     question
    │            │
    ▼            ▼
Ground in    Verify customer
approved     (policy + DOB +
knowledge    phone last 4)
    │            │
    │       ┌────┴────┐
    │       │Verified?│
    │       └────┬────┘
    │         Yes│    No (×2) → Escalate
    │            ▼
    │       Retrieve owned
    │       claim via flow
    │            │
    │            ▼
    │      AI explains status,
    │      missing docs, next step
    │            │
    └─────┬──────┘
          ▼
     Resolved?
    ┌──────┴──────┐
    │             │
   Yes         No / Sensitive
    │             │
    ▼             ▼
Summarise    Build context summary
and close    → Human agent or callback
```

---

### AI Intervention Points

| Step | What AI does |
|---|---|
| 1 | Recognises customer intent — FAQ vs personal query |
| 2 | Retrieves answer from approved knowledge (not web) |
| 3 | Collects verification inputs conversationally |
| 4 | Calls VerifyCustomer action and handles result |
| 5 | Calls GetClaimStatus with verified customer ID only |
| 6 | Explains returned status in plain language |
| 7 | Prepares structured handoff summary before escalation |

---

### Control Points (What AI Does NOT Do)

- ❌ No claim decisions — AI cannot approve, deny, or interpret liability
- ❌ No data without verification — claim info only after VerifyCustomer returns `verified=true`
- ❌ No cross-customer access — flow filters by customer ID in every query
- ❌ No write actions without confirmation — callback requires explicit `confirmed=true`
- ❌ No sensitive data exposure — phone, DOB, full policy details never echoed back

---

### Closing Statement

> *"Voice360 makes routine motor claim servicing immediate and consistent,*
> *while keeping consequential insurance decisions firmly with people."*

---

## Submission Checklist

| Item | Status |
|---|---|
| Working solution / source code | ✅ GitHub repo with all docs, data, flows, scripts |
| Five-slide presentation | ✅ This file → copy into PowerPoint/Google Slides |
| Demo screenshots (optional) | ⬜ Add Copilot Studio screenshots if available |
| AI prompts / config files | ✅ All `.md` files in repo — especially `03`, `04`, `06`, `10` |
