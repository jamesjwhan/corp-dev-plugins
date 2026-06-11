---
name: deal-closing-planner
description: Scope a deal-specific sign-to-close plan for an M&A acquisition — which conditions precedent, third-party consents, and Day-1 readiness tracks actually apply to THIS deal — sequenced to the sign-to-close timeline, with a live conditions-precedent (CP) tracker. Use this whenever a Toast deal has signed or is about to sign a term sheet or definitive agreement and the team needs to plan the path to close: "build the closing plan", "what do we need to close this", "sign-to-close plan", "closing checklist for [deal]", "conditions to close", "Day-1 readiness plan", "what consents do we need", "do we need to file HSR", "plan the integration working group", or any time a deal moves from confirmatory diligence into Stage 6. Composes the plan from a reusable closing-module library rather than a static checklist, so small same-day tuck-ins get a lean plan and larger deferred-close deals get the full set. Always use this skill to produce a sign-to-close plan; do not assemble one by hand.
---

# Deal Closing Planner

Generates a **deal-specific sign-to-close plan** — the scoped set of conditions precedent (CPs),
third-party consents, regulatory steps, and Day-1 readiness tracks that apply to a particular
acquisition — sequenced against the sign-to-close timeline, plus a live CP tracker that the
`integration-tracker` agent runs to closure.

The plan is **composed**, not boilerplate: read the deal's facts, decide which modules from the
closing-module library are in scope, and assemble only those. A sub-HSR acqui-hire that signs and
closes the same day gets a short plan; an HSR-reportable deal with international employees, debt,
and customer-consent requirements gets the full set.

This skill maps to **Stage 6 — Sign-to-Close** of the Toast Deal Operating Map.

---

## Critical guardrails — read first

1. **Drafts and surfaces only.** This skill (and the `integration-tracker` agent that runs its
   output) produces plans, trackers, and draft documents. It does **not** file HSR, send consent
   requests, execute wires or funds flow, or send comms. Every action that touches the outside
   world or moves money is routed to a named human DRI for approval. This includes **creating shared
   artifacts**: the skill drafts the tracker against its schema but does **not** create Google Sheets
   or write to Corp Dev Spaces / Drive on its own — it presents the draft and asks the human to
   approve creating the sheet (or to create it themselves). State this when presenting the plan.
2. **Not legal or tax advice.** Regulatory flags — HSR reportability above all — are *preliminary
   triage for counsel to confirm*, never a determination. Legal instruments (definitive agreements,
   stockholder package, 280G/FIRPTA certificates) are tracked as required items but are **owned and
   drafted by Legal / outside counsel**, not by this skill.
3. **Confidentiality.** A live deal is MNPI. Keep outputs inside the deal's private workspace
   (code-name folder in Corp Dev Spaces, `#acq-` channel). Do not widen the circle without the
   Deal Lead's say-so.

---

## Inputs

**Primary input — the Deal Memo** (output of `deal-memo-writer`). Read it for: deal type
(acqui-hire / tuck-in / platform), proposed structure (stock vs. asset vs. merger), consideration
size, geographies, revenue model, cap structure, employee count, and the flagged risks. Most
scoping decisions come straight from here.

**Secondary inputs** (ask for whatever isn't in the memo):
- Term sheet / definitive agreement terms (consideration, retention pool, any defined closing conditions)
- Deal size (for HSR reportability) and whether the seller has foreign owners (FIRPTA)
- Whether material customer contracts, leases, IP licenses, or debt carry change-of-control clauses
- Whether the target has international employees or contractors
- Target close date / whether sign-and-close is simultaneous

If the Deal Memo isn't available, you can still run — gather the secondary inputs directly — but
say so, because the scoping will be coarser.

---

## Workflow

### Step 1 — Gather and read context
Read the Deal Memo and collect the secondary inputs above. If key facts are missing, ask for them
in **one** consolidated question rather than interrogating turn by turn.

### Step 2 — Determine the close archetype
Decide **same-day close** vs. **deferred close**, because it determines whether Stage 6 is a real
calendar phase or a compressed pre-signing checklist:
- **Same-day** (most sub-HSR acqui-hires/tuck-ins): no regulatory waiting period, no required
  third-party consents that gate signing → the work collapses to immediately pre-signing. Produce a
  lean plan; the "close gate" merges into signing.
- **Deferred** (HSR-reportable, required consents, financing, carve-out): a multi-week/month phase
  governed by the agreement's interim covenants. Produce the full plan with a dated timeline.

State which archetype you picked and why.

### Step 3 — Scope the in-scope modules
For each module in the library (`references/module-library.md`), decide in or out from the deal
facts. The scoping logic for each lives in its reference file — read the relevant ones. Quick map:

| Trigger in the deal | Pull this module |
|---|---|
| Always | `closing-mechanics` (CP tracker, flow-of-funds, certificates) |
| Deal size near/above HSR threshold, or any antitrust sensitivity | `regulatory-and-consents` (HSR section) |
| Material contracts/leases/IP/debt with change-of-control | `regulatory-and-consents` (consents section) |
| Any equity/stockholders to pay | `stockholder-and-financial` (stockholder section) |
| Always (deal consideration moves) | `stockholder-and-financial` (financial/tax/acct section) |
| Employees transferring | `day1-readiness` (People track) |
| Always (systems handover) | `day1-readiness` (IT/Security track) |
| Customers/product transferring | `day1-readiness` (Product/Commercial track) |
| Always (announcement) | hand to `deal-comms-runbook` — don't duplicate here |

When a module is **out**, say so explicitly in the plan ("Not in scope: foreign merger control —
single-jurisdiction US deal"). Naming what you excluded is as important as what you included.

### Step 4 — Compose the plan
For each in-scope module, pull its checklist items, assign the **DRI** (use the Operating Map
defaults — Legal, Finance/Tax/Acct, People Ops, IT/Security, Product+GTM, Corp Dev), and place each
item on the timeline relative to **sign** and **close**. Mark each item's **type**
(CP / consent / regulatory / Day-1) and **priority** (P0 blocks close · P1 needed for clean Day-1 ·
P2 fast-follow). Long-lead items (PPA/intangibles valuation, immigration transfers, HSR waiting
period) get flagged to start at signing.

### Step 5 — Draft the CP tracker
Draft the tracker content against `assets/cp-tracker-schema.md` — one row per condition/item, with the
schema's columns — and present it (as a table) for review. **Do not create the Google Sheet yourself.**
Ask the human to approve creating it in the deal's workspace, or to create it themselves. Once it
exists, this is the artifact `integration-tracker` runs.

### Step 6 — Present for human review
Surface the plan and tracker to the Deal Lead **before** anything is created in shared systems or
sent. Lead with: the archetype, the modules in/out, the P0 (close-blocking) items, and the
long-lead items that must start now. Ask for sign-off, then hand the tracker to `integration-tracker`.

---

## The module library

The closing-module library lives in `references/`. Read `references/module-library.md` first for the
index and the in/out scoping logic, then read only the module files you need for this deal:

- `references/module-library.md` — index + scoping decision table (read first)
- `references/regulatory-and-consents.md` — HSR reportability (with current thresholds), foreign
  merger control, CFIUS, third-party change-of-control consents
- `references/stockholder-and-financial.md` — stockholder package (consents, transmittal, payment
  spreadsheet, 280G, FIRPTA) and financial/tax/accounting close-out (estimated closing statement,
  NWC peg/true-up, PPA, escrow, insurance, tax elections)
- `references/day1-readiness.md` — the three Day-1 tracks (People/HR, IT/Security, Product/Commercial)
- `references/closing-mechanics.md` — closing checklist, flow-of-funds, closing certificates, the close gate

Modules are authored once and reused across deals. To add coverage (e.g., a new consent type),
extend the module file — not this SKILL.md.

---

## Output

Two artifacts, both in the deal's private workspace:
1. **Sign-to-close plan** (Markdown in Corp Dev Spaces) — archetype, modules in/out, workstreams
   with DRIs and timeline, P0/P1/P2, long-lead flags.
2. **CP tracker** — drafted against the schema and presented for approval; created in Google Sheets
   only on the human's go-ahead. Once created, it's the live execution artifact for `integration-tracker`.

Close the loop by naming the handoff: "Plan ready for review. On sign-off, `integration-tracker`
takes the CP tracker from here and runs it to close."
