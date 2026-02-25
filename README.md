# Wealthsimple Mortgage AI

**A working prototype built for the Wealthsimple AI Builders application.**

→ **[View live demo](https://your-site.netlify.app)** *(replace with your Netlify URL)*

---

## What this is

Mortgage pre-approval is broken — designed around the broker's workflow, not the user's experience. Users wait days for a discovery call, hunt for documents blindly, and have no idea where they stand. Brokers spend 60–70% of their time on work requiring no expertise: collecting information, checking completeness, running arithmetic. High-judgment work gets rushed because the pipeline is clogged with admin.

This prototype reimagines the entire workflow. An AI agent handles intake, calculation, and file preparation — so users understand their situation in minutes, and brokers receive a structured brief that turns a Discovery Call into a Closing Call.

---

## System architecture — four layers

| Layer | What it does | Who builds it |
|---|---|---|
| **Adaptive Intake Agent** | LLM-powered conversational intake. Handles ambiguity, branches on employment type and intent, collects a complete financial profile one question at a time. | Claude Sonnet |
| **Deterministic Financial Engine** | Pure JavaScript. GDS/TDS ratios, stress test at 5.25%, CMHC thresholds, qualifying range calculation. No LLM — reproducible, auditable math. | JS |
| **Risk Flag Engine** | Rule-based binary flags: self-employed under 2 years, TDS above 44%, gifted down payment, variable income, LTV above 80%. Triggers are explicit and traceable. | JS |
| **Confidence Scoring + Escalation** | 0–100 score with visible factor breakdown. Below 75: mandatory human review. Above 90: broker proceeds with minimal clarification. | JS |

**AI owns Layers 1–3. The broker owns the Final Suitability Assessment** — the legal requirement under MBLAA for a licensed human to attest to product suitability. Escalation is rule-triggered, not discretionary.

---

## What it does

### For the user

**Purchase path:**
- "Are you a Wealthsimple client?" — existing clients connect their account, greet by name, savings data surfaces naturally at the down payment question
- Collects: co-applicant status, first-time buyer status, property type, buying stage, timeline, employment, income, debts, down payment, target price
- GDS/TDS ratio analysis with plain-language explanation
- Qualifying range with low/high bounds
- Scenario modelling — "paying off your car loan adds ~$40k to your qualifying range"
- First-time buyer callout — RRSP Home Buyers' Plan, FHSA, land transfer tax rebate
- Rate context — current 5-year fixed range, translated to an estimated monthly payment for their specific mortgage amount
- Personalised document checklist
- **Follow-up chat** — "Questions about your assessment? Ask here" — live AI answers about their specific report, in context
- Broker calendar always one click away

**Refinance path:**
- Collects: term remaining, current lender, refinance reason, equity use purpose, income, debts, property value, mortgage balance, current rate, amortisation remaining
- Equity available display (LTV-constrained at 80%)
- Estimated monthly saving at current market rates
- Break-even analysis — months to recover prepayment penalty
- Amortisation reset cost — total additional interest if extending from remaining years to 25yr
- Prepayment penalty context based on term remaining and lender
- Refinance-specific document checklist

### For the broker

- **Suggested opening** — AI-generated paragraph tailored to client's intent, stage, timeline, lender, and confidence score. Tells the broker exactly how to open the call.
- **Human escalation status** — Yes/No badge with the specific rule that triggered it
- **Confidence score** — 0–100 with factor breakdown (visible deductions for self-employment, elevated TDS, gifted down payment, etc.)
- **Client profile** — name, contact info, Wealthsimple client status, all financial data pre-calculated
- **Complexity flags** — self-employment, contract income, variable income, LTV concerns — each with broker-specific guidance
- **What the client was told** — exact record of disclaimer language and qualifying range shown, preventing misaligned expectations

### Data integrations (prototype placeholders)

Three connection points shown in the UI — architecturally correct positions, not live integrations:

- **Wealthsimple Account** (intake start) — pre-populates savings, RRSP, TFSA for existing clients. Unique platform advantage no third party can replicate.
- **CRA My Account** (post-intake) — verifies income via NOA 2-year average. Closes the gap between stated and qualifying income. Boosts confidence score by ~18 points.
- **Credit Bureau** (post-intake) — soft pull, does not affect credit score. Each connection updates the confidence dial and adds a verified badge to the broker brief.

---

## Human/AI boundary

In Canada, recommending lenders, advising on products, or issuing pre-approvals requires a provincial mortgage broker licence. The AI operates entirely within what is permissible without one.

The output is called a **"Readiness Assessment"** — not a pre-approval. A persistent disclaimer is the first element on the report. The system never implies the user can act on its output without broker confirmation.

---

## Key design decisions

**"Readiness Assessment" not "Pre-Approval"** — language chosen deliberately to avoid implying regulated activity.

**Disclaimer is not hidden** — opens the report with a prominent, persistent warning. Design choice, not boilerplate.

**Confidence score makes uncertainty visible** — rather than presenting a single qualifying number with false precision, the system shows a score with a breakdown. Users and brokers know exactly how much to trust the estimate.

**Broker opener generated from profile** — the brief doesn't just hand over data, it tells the broker how to use it. One AI-generated paragraph that changes a discovery call into a closing call.

**Follow-up chat on report** — users who have questions about their assessment get answers in context without jumping to a broker call they're not ready for. Keeps them engaged, builds trust, means they arrive at the broker call genuinely prepared.

**Family income, not individual** — combined household income matches how lenders assess applications and reduces cognitive load.

**Broker calendar always visible** — users uncomfortable with AI-driven intake can book a human call at any point.

---

## Tech stack

- Single-file HTML/CSS/JS frontend — no framework, no build step
- Anthropic API (`claude-sonnet-4-20250514`) for conversational intake and follow-up chat
- Netlify Functions for secure API proxy — key never exposed to frontend or repository
- Pure JS calculation engine for all financial math — independently auditable without touching the model layer

*In production: all model calls would be server-side only, with full audit logging, versioned and hash-pinned prompts, and decision trace storage for regulatory review.*

---

## Repo structure

```
wealthsimple-mortgage-ai/
├── index.html          # Full frontend — single file, no build step
├── netlify.toml        # Netlify config — CSP headers, functions directory
├── README.md
└── netlify/
    └── functions/
        └── chat.js     # Serverless API proxy — key never hits the client
```

---

## Local development

```bash
npm install -g netlify-cli
netlify dev
```

Then open `http://localhost:8888`

---

## Deployment

1. Clone this repo and push to GitHub
2. Connect to Netlify via GitHub integration
3. Add environment variable in Netlify dashboard:
   - Key: `ANTHROPIC_API_KEY`
   - Value: your Anthropic API key
4. Deploy — Netlify auto-detects functions from `netlify.toml`

---

## What I'd build next

- **Document upload with AI completeness verification** — agent checks uploads against the personalised checklist before the broker sees the file
- **Live CRA My Account integration** — requires CRA partnership approval; auto-populates NOA income, closes stated vs qualifying income gap
- **Rate/Policy Registry** — centralised source of truth for stress test rates and CMHC thresholds, feeds all agents simultaneously to prevent drift when regulations change
- **Re-engagement flow** — "You mentioned you'd pay off your car loan. Want to re-run your assessment?" — closes the loop on users who weren't ready
- **Lender policy variance flags** — profile attributes known to affect lender sensitivity, surfaced to broker as context without constituting advice
- **Broker dashboard** — queue management, file prioritisation, one-click lender submission

---

*Built by [Your Name] — Wealthsimple AI Builders application, February 2026*
