---
name: deal-integration-plan
description: Build the post-close integration plan, OKRs, and performance dashboard for a completed Toast acquisition — long-term integration roadmap with 2/4/6-month milestones, the OKR set tied to the deal thesis, and a performance dashboard tracking delivery vs. OKR plus financial/business metrics. Use this whenever a deal has closed (or is about to) and the team needs to plan and track value realization: "build the integration plan", "set the integration OKRs", "post-close plan for [deal]", "integration roadmap", "performance dashboard for the acquisition", "are we hitting our integration milestones", "2/4/6 month plan", or any time a deal enters Stage 7. Pulls baseline objectives from the Deal Memo thesis and the IWG charter. Optionally wires in earnout/payout and post-close obligations tracking. Feeds the integration-tracker agent. Always use this skill for post-close integration planning and tracking; do not assemble it by hand.
---

# Deal Integration Plan

Produces the **post-close integration plan, OKRs, and performance dashboard** for a closed
acquisition. Maps to **Stage 7 — Integration & Value Realization** of the Toast Deal Operating Map.

This skill is the **one exception to the planner→library pattern.** The other planners compose from a
checklist library; this one derives from the **deal thesis.** The reason the deal was done — the Deal
Memo's rationale and key assertions — *becomes* the OKR set. Integration succeeds when the thesis
comes true, so the plan is built backwards from the thesis, not forward from a generic template.

It covers the operating map's two Stage 7 activities — (1) long-term plan & OKRs, (2) performance
tracking / dashboard — plus the optional earnout/obligations tracker.

---

## Critical guardrails — read first

1. **Drafts and surfaces only.** Produces the plan, OKRs, and dashboard. The `integration-tracker`
   agent runs the cadence but does not take action or report externally without human approval. This
   includes **creating shared artifacts**: the skill drafts the dashboard against its schema but does
   **not** create the Google Sheet or write to Corp Dev Spaces / Drive on its own — it presents the
   draft and asks the human to approve creating it.
2. **OKRs derive from the thesis, not from activity.** Resist listing tasks as objectives. An OKR is a
   measurable outcome that proves the thesis ("acquired product live to 500 Toast customers by month
   6"), not "complete the integration." Tie every objective to a Deal Memo assertion.
3. **Retention is a first-class objective.** For acqui-hires especially, keeping the acquired team is
   often the core of the thesis — make it an explicit, tracked OKR, not an afterthought.

---

## Inputs

**Primary — the Deal Memo thesis.** Read the rationale, the "why now," and the **key assertions**.
These are the raw material for OKRs: each assertion implies a measurable outcome that, if hit, proves
the deal worked.

**Secondary:**
- **IWG charter** (from Stage 6) — the workstreams and DRIs (Roadmap DRI / Product, Engagement DRI /
  Eng, optional Ops DRI). The plan assigns OKRs to these owners.
- The **integration approach** from technical diligence (keep / rebuild / discard per component, with
  2/4/6-month milestones) — operationalize it.
- The **post-close tail** from the closing plan (NWC true-up, escrow release, earnout) for the optional
  obligations tracker.

---

## Workflow

### Step 1 — Extract the thesis into objectives
List the Deal Memo's key assertions and rationale. For each, write the **outcome that proves it**.
"Accelerates our payments roadmap by 12 months" → "Acquired capability shipped in Toast product by
month 6." Use `references/okr-derivation.md` for the translation patterns.

### Step 2 — Set 2/4/6-month milestones
Build the integration roadmap (`references/integration-roadmap.md`): the keep/rebuild/discard plan
operationalized into milestones at 2, 4, and 6 months. Pick milestone patterns by deal type
(acqui-hire vs. product tuck-in vs. platform).

### Step 3 — Write the OKR set
Translate objectives into OKRs with the Roadmap DRI (Product) + Engagement DRI (Eng):
- **Objectives** — outcomes that prove the thesis (3–5 max)
- **Key results** — measurable, time-bound, owned. Include **retention** (acquired-team retention %)
  and **integration milestones** as KRs.
Keep it tight — an integration with 12 objectives has none.

### Step 4 — Draft the performance dashboard
Draft the dashboard against `assets/integration-dashboard-schema.md`: OKR/KR tracking with RAG status,
plus the financial/business metrics that matter for this deal (e.g., acquired-product adoption, revenue
contribution, cost synergies, retention). **Do not create the Google Sheet yourself** — present the
draft and ask the human to approve creating it, or to create it. Set the cadence: **2–3×/week for the
first 30 days, then weekly.**

### Step 5 — (Optional) draft the obligations tracker
If the deal has an earnout, holdback, or material post-close obligations, draft the
earnout/obligations tracker against `assets/earnout-obligations-tracker-schema.md`: earnout milestones
+ payout schedule, NWC true-up (60–90 days), escrow-release schedule, indemnity survival, post-close
tax elections. **Do not create the Google Sheet yourself** — present the draft and ask the human to
approve creating it. DRI: Finance.

### Step 6 — Present + hand off
Surface the plan, OKRs, and dashboard to the Exec Sponsor + IWG for sign-off. On approval,
`integration-tracker` runs the cadence and surfaces delivery-vs-OKR and misses.

---

## References & assets
- `references/okr-derivation.md` — turning thesis/assertions into measurable OKRs (with examples)
- `references/integration-roadmap.md` — 2/4/6-month milestone patterns by deal type
- `assets/integration-dashboard-schema.md` — Google Sheet (OKR tracker + metrics + RAG)
- `assets/earnout-obligations-tracker-schema.md` — optional post-close obligations sheet

## Output
Integration plan + OKR set + performance dashboard (drafted against the schema; created in Google
Sheets only on human approval), in the deal's workspace. Once created, `integration-tracker` runs the
cadence from here. Drafts/surfaces only — the team and execs own the decisions and the external reporting.
