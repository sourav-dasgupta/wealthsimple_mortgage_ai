# Wealthsimple Mortgage AI

**A working prototype built for the Wealthsimple AI Builders application.**

→ **[View live demo](https://your-site.netlify.app)** *(replace with your Netlify URL)*

---

## What this is

Mortgage pre-approval is one of the most consequential financial decisions in a person's life — yet the process is designed around the broker's workflow, not the user's experience. Users wait days for a call, hunt for documents blindly, and have no idea where they stand until a human tells them. Meanwhile, brokers spend the majority of their time on work that requires no expertise: collecting information, checking completeness, and doing arithmetic.

This prototype reimagines that process. An AI agent handles the entire intake — conversational, intelligent, and personalised — so that users understand their situation in minutes, and brokers receive a fully structured file instead of starting from scratch.

## What it does

**For the user:**
- Conversational AI intake — one question at a time, plain language, adapts to employment type and household situation
- Real-time GDS/TDS ratio calculation with stress test
- Personalised document checklist based on their specific situation
- Scenario modelling — "paying off your car loan adds ~$40k to your qualifying range"
- Clear, honest readiness report — explicitly not a pre-approval

**For the broker:**
- Structured brief with all financial data pre-calculated
- Complexity flags requiring human judgment (self-employment, variable income, gifted down payment)
- Record of exactly what the user was told — preventing misaligned expectations

**Human/AI boundary (intentional design decision):**
- AI owns: intake, calculation, document checklist, scenario modelling, eligibility estimation
- Broker owns: lender matching, final approval, advice, anything requiring a license
- The AI never issues a pre-approval. It prepares the file. The broker closes the loop.

## System design

```
User → Conversational intake (AI agent)
     → Real-time calculation engine (GDS/TDS/stress test)
     → Readiness Assessment Report
     → Document upload & completeness check (AI)
     → Broker brief (structured handoff)
     → Broker reviews → Lender submission → Pre-approval
```

The AI agent is goal-directed — it's not a chatbot answering questions, it's driving toward a defined output (a complete financial profile) while handling ambiguity, branching logic, and plain-language explanation. At no point does it operate outside the boundary of what's legally permissible without a mortgage license.

## Key design decisions

**"Readiness Assessment" not "Pre-Approval"** — language chosen deliberately to avoid implying regulated activity. Users are told explicitly throughout that this is not a pre-approval.

**Disclaimer is not hidden** — the report opens with a persistent, prominent warning. This is a design choice, not legal boilerplate.

**Family income, not individual** — asking for combined household income matches how lenders actually assess applications and reduces cognitive load.

**Broker calendar always visible** — users who are uncomfortable with AI-driven intake can book a human call at any point. This reduces anxiety and paradoxically increases trust in the AI path.

## Tech stack

- Single-file HTML/CSS/JS frontend — no framework, no build step
- Anthropic API (`claude-haiku-4-5-20251001`) for conversational intake
- Netlify Functions for secure API proxy (key never exposed to frontend)
- Pure JS calculation engine for GDS/TDS ratios and stress test

## Local development

```bash
npm install -g netlify-cli
netlify dev
```

Then open `http://localhost:8888`

## Deployment

1. Fork or clone this repo
2. Connect to Netlify via GitHub
3. Add environment variable in Netlify dashboard:
   - Key: `ANTHROPIC_API_KEY`
   - Value: your Anthropic API key
4. Deploy

## What I'd build next

- Document upload with AI completeness verification
- CRA My Account integration to auto-populate NOA data
- Lender matching engine based on profile
- Broker dashboard with queue management
- Re-engagement flow for users who weren't ready ("you said you'd pay off your car loan — want to re-run your assessment?")

---

*Built by [Your Name] — Wealthsimple AI Builders application, February 2026*
