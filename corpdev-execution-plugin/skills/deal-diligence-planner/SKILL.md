---
name: deal-diligence-planner
description: Scope a deal-specific confirmatory diligence plan for an M&A acquisition — which functional workstreams (legal, technical, financial, people/HR, tax/accounting, customer) are in scope, with tailored request lists and P0/P1/P2 priorities — driven by the deal's facts rather than a static checklist. Use this whenever a Toast deal enters confirmatory diligence (post-term-sheet, Stage 5) and the team needs a diligence plan: "build the diligence plan", "what do we need to diligence", "confirmatory diligence plan", "diligence request list for [deal]", "scope the DD", "what should we be checking on this deal", or any time a deal moves from term sheet into deep diligence. Can also seed the lighter Stage 3 early-diligence request list. Composes from a reusable DD-module library so a small acqui-hire gets a lean plan and a revenue-generating platform deal gets the full set. Feeds the diligence-coordinator agent. Always use this skill to produce a diligence plan; do not assemble one by hand.
---

# Deal Diligence Planner

Generates a **deal-specific confirmatory diligence plan** — the scoped set of functional diligence
workstreams, tailored request lists, and priorities for a particular acquisition — driven by the
deal's facts and the risks the Deal Memo flagged. Maps to **Stage 5 — Confirmatory Diligence** of the
Toast Deal Operating Map (and can seed the lighter Stage 3 early-diligence request list).

The plan is **composed**, not boilerplate. A pre-revenue talent acqui-hire and a $200M revenue
platform get very different diligence: read the deal's facts, decide which workstreams go deep and
which are light or out, and assemble only what the deal warrants. **The flagged risks in the Deal
Memo are the single most important driver** — they tell you where to dig.

This skill is the diligence half of a planner→coordinator pair: it *generates* the plan;
`diligence-coordinator` *runs* it to closure.

---

## Critical guardrails — read first

1. **Drafts and surfaces only.** Produces the plan, request lists, and the status tracker. It does
   **not** send diligence requests, share documents, or contact the target. Outbound requests are
   drafted for the Deal Lead / function leads to send.
2. **Not legal, tax, or financial advice.** The plan scopes *what to examine* and *who owns it*; the
   functional DRIs and specialist skills (`financial-diligence`) do the actual analysis and form the
   conclusions.
3. **No unilateral shared-system writes.** The skill drafts the tracker against its schema but does
   **not** create Google Sheets or write to Corp Dev Spaces / Drive on its own. It presents the draft
   and asks the human to approve creating the sheet (or to create it themselves).
4. **Clean-team discipline.** Technical diligence runs under a clean-team protocol (see the technical
   module). Respect it in the plan — don't route sensitive code/IP to people who'd build the same
   thing if the deal dies.
5. **Confidentiality.** Live deal = MNPI. Keep outputs in the deal's private workspace; restrict to
   the tent roster.

---

## Inputs

**Primary input — the Deal Memo** (output of `deal-memo-writer`). Read it for: deal type
(acqui-hire / tuck-in / platform), business model and revenue scale, geographies, tech stack, cap
structure, employee count and composition, and — most importantly — the **flagged risks and key
assertions**. The assertions are the things the deal thesis depends on being true; confirmatory
diligence exists to validate or break them.

**Secondary inputs** (ask for what's missing, in one consolidated question):
- What's in the data room already (from `data-room-analyst`) so you don't re-request it
- Revenue scale and whether there are paying customers (drives financial + customer depth)
- Employee count and locations, esp. international (drives people depth)
- Open-source posture of the codebase (drives technical/IP depth)
- Any specific risks the team already wants pressure-tested

---

## Workflow

### Step 1 — Read context and assertions
Read the Deal Memo, with focus on the **key assertions and flagged risks**. List them — each
assertion becomes a diligence objective ("thesis says 130% NRR → financial + customer DD must
confirm"). Collect the secondary inputs.

### Step 2 — Set diligence depth (archetype)
Pick the depth, because it scales every workstream:
- **Talent acqui-hire** (pre-revenue, small team, product likely sunset): light financial/customer,
  heavy people + technical(team) + IP/employment legal. Often no QoE.
- **Tuck-in** (some revenue, product to integrate): moderate across the board; real financial and
  customer DD; technical with integration focus.
- **Platform** (material revenue, standalone product): full depth everywhere; QoE, deep customer
  cohort analysis, full code/architecture + security review.

State the archetype and what it implies.

### Step 3 — Scope the workstreams
For each of the six functional modules (`references/module-library.md`), decide in / light / out from
the deal facts and the assertions. Read the relevant module files for the scoping logic and the
request-list items. Map:

| Module | Default | Goes deep when |
|---|---|---|
| `legal-dd` | Always in | IP-heavy, regulated, litigation flags, complex cap table |
| `technical-dd` | In if a codebase transfers | Product is the thesis; open-source obligations; security-sensitive |
| `financial-dd` | In if revenue is material | Revenue/retention is a key assertion (→ pair with `financial-diligence` skill) |
| `people-dd` | In if employees transfer | Team is the thesis (acqui-hire); international; key-person risk |
| `tax-accounting-dd` | Always in | Cross-border, complex structure, material tax exposure |
| `customer-dd` | In if there are customers | Retention/concentration is a key assertion |

Name what's **out** and why ("Customer DD: out — pre-revenue talent acqui-hire, no customers").

### Step 4 — Compose the request lists
For each in-scope module, pull its request-list items, **trim to what this deal needs**, assign the
**DRI** (Legal, Eng, Finance/Tax/Acct, People Ops, Corp Dev+Product for customer), and set **priority**:
- **P0** — validates a key assertion or surfaces a deal-breaker (do first)
- **P1** — material to valuation or integration, not deal-breaking
- **P2** — confirmatory / nice-to-have

Tie each P0 item back to the assertion it tests. Flag long-lead items (full security review,
all-employee interviews for large teams).

### Step 5 — Draft the diligence status tracker
Draft the tracker content against `assets/dd-tracker-schema.md` and present it (as a table) for review.
**Do not create the Google Sheet yourself** — ask the human to approve creating it, or to create it.
Once it exists, this is the artifact `diligence-coordinator` runs. Mirrors the closing-planner's
tracker shape for consistency.

### Step 6 — Present for human review
Surface to the Deal Lead before any request goes out: archetype, workstreams in/light/out, the P0
items mapped to assertions, and long-lead items to start now. On sign-off, hand the tracker to
`diligence-coordinator`.

---

## The module library

Lives in `references/`. Read `references/module-library.md` first (index + scoping), then only the
modules in scope:
- `references/legal-dd.md` — corporate, IP/license, contracts/MSAs, employment, litigation, privacy/security
- `references/technical-dd.md` — code/architecture (clean team), license + security review, integration milestones
- `references/financial-dd.md` — QoE, NWC, cash bridge, debt/cash (hands the model to `financial-diligence`)
- `references/people-dd.md` — comp/leveling, classification, retention, all-employee interviews
- `references/tax-accounting-dd.md` — structure, ASC 805, material tax liabilities
- `references/customer-dd.md` — blind references, retention (GRR/NRR), concentration, interviews

Modules are authored once and reused. Extend the module file to add coverage; don't bloat this SKILL.md.

---

## Output

Two artifacts in the deal's private workspace:
1. **Confirmatory diligence plan** (Markdown in Corp Dev Spaces) — archetype, workstreams in/light/out,
   request lists with DRIs, P0/P1/P2 mapped to assertions, long-lead flags.
2. **Diligence status tracker** — drafted against the schema and presented for approval; created in
   Google Sheets only on the human's go-ahead. Once created, it's the live artifact for `diligence-coordinator`.

Name the handoff: "Plan ready for review. On sign-off, `diligence-coordinator` runs the request list
to closure and surfaces blockers at the diligence cadence."
