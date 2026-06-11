---
name: deal-comms-runbook
description: Build the M&A announcement and communications plan for a Toast acquisition — announcement decision tree, asset checklist with DRIs, day-of run-of-show, 8-K/IR sequencing, leak/rumor playbook, and customer + seller-employee comms. Use this whenever a deal approaches signing or close and needs a comms plan: "build the comms plan", "announcement plan for [deal]", "how do we announce this", "run-of-show", "do we need an 8-K", "leak response plan", "customer comms for the acquisition", or any time a deal enters Stage 5 (announcement at signing) or Stage 6 (close-day formality). Distinguishes the signing announcement (the real launch, Item 1.01 8-K) from the close-day confirmation (formality, Item 2.01 8-K if material). Always use this skill for acquisition comms planning; do not assemble it by hand.
---

# Deal Comms Runbook

Produces the acquisition communications plan — announcement posture, the full asset set with owners,
the day-of run-of-show, 8-K/IR sequencing, and the leak/rumor playbook. Maps to **Stage 5 (deal
announcement at signing — the launch)** and **Stage 6 (close-day comms — the formality)** of the
Toast Deal Operating Map.

The plan is **composed to the deal's materiality and posture**: a quiet sub-threshold acqui-hire gets
a light internal-plus-customer plan with no 8-K; a material platform deal gets full PR, an Item 1.01
8-K at signing, IR coordination, and an AMA. Read the deal, set the posture, assemble accordingly.

---

## Critical guardrails — read first

1. **Drafts plans and assets only — never publishes, files, or sends.** Every external action
   (press release, 8-K filing, customer email, social post) is drafted and routed to its named DRI
   for approval. This includes **creating shared artifacts**: the skill drafts the comms tracker
   against its schema but does **not** create the Google Sheet or write to Corp Dev Spaces / Drive on
   its own — it presents the draft and asks the human to approve creating it. The skill builds the
   plan and the drafts; humans push the buttons.
2. **MNPI until announced.** A pending deal is material non-public information. Keep all comms work in
   the deal's private workspace. Pre-announcement, even internal distribution is tent-only.
3. **Gun-jumping / Reg FD awareness.** If HSR-reportable, no public posture that implies integration
   before close. For a public company, selective disclosure rules (Reg FD) shape timing — route 8-K
   and IR decisions to Legal/IR, don't freelance.
4. **Not legal/IR advice.** Materiality and 8-K obligations are flagged for Legal/IR to confirm.

---

## The core distinction to get right

| | **Stage 5 — Announcement (the launch)** | **Stage 6 — Close-day (the formality)** |
|---|---|---|
| Fires at | **Signing** | **Closing** |
| 8-K (public co) | **Item 1.01** within 4 business days of signing, if material | **Item 2.01** within 4 business days of close, if still material |
| Effort | Full: blog, PR, internal all-hands, customer, seller-employee, social, AMA, IR | Light: close-confirmation note, banners/status updates |
| Why | First public reveal — the moment that shapes the narrative | Confirmation that the already-announced deal completed |

For **same-day sign-and-close**, the two collapse into one event — run the Stage 5 plan, file the
appropriate 8-K, skip the separate close-day step.

---

## Inputs
- Deal Memo + deal facts (size, strategic rationale, target name, what's being acquired)
- **Materiality read** (drives the 8-K question) — flag to Legal/IR
- Deal sponsor's preferred **posture**: proactive PR / part of earnings / reactive-only
- Sign-and-close simultaneous? (collapses the two stages)
- Tent roster + notification constraints

---

## Workflow

### Step 1 — Set the announcement posture
Use `references/announcement-decision-tree.md` to choose proactive PR vs. earnings-timed vs.
reactive-only, from deal size, strategic significance, and competitive sensitivity.

### Step 2 — Determine 8-K / IR obligations
From the materiality read, determine the 8-K path (1.01 at signing / 2.01 at close) using
`references/8k-and-ir.md`. **Flag to Legal/IR — don't decide materiality unilaterally.** Sequence IR/
analyst comms relative to the filing.

### Step 3 — Compose the asset checklist
From `references/asset-library.md`, select the assets the posture warrants and assign a **DRI** to
each: blog, PR/press release, internal all-hands + Slack, customer comms, seller-employee comms,
website banner, social, AMA, IR/analyst, FAQ (internal + external).

### Step 4 — Build the run-of-show
Sequence the day-of using `assets/run-of-show-template.md`: the **notification order** (who's told
when — tent → broader internal → employees → customers → public), timing relative to signing/close/
market hours, and the go/no-go checkpoints.

### Step 5 — Build the leak/rumor playbook
From `references/leak-rumor-playbook.md`: holding statements, escalation tree, monitoring plan.
Active through the **entire** sign-to-close window (leak risk peaks during a deferred close).

### Step 6 — Draft the comms tracker + present
Draft the comms tracker against `assets/comms-tracker-schema.md` (assets, DRIs, status, approval
state) and present the full plan to the deal sponsor + Comms lead for sign-off. **Do not create the
Google Sheet yourself** — ask the human to approve creating it, or to create it. Nothing publishes
without approval.

---

## References & assets
- `references/announcement-decision-tree.md` — posture logic
- `references/asset-library.md` — the full asset set with audience + purpose
- `references/8k-and-ir.md` — Item 1.01 vs 2.01, timing, materiality, Reg FD / gun-jumping
- `references/leak-rumor-playbook.md` — holding statements, escalation, monitoring
- `assets/run-of-show-template.md` — day-of sequence
- `assets/comms-tracker-schema.md` — Google Sheet (assets, DRIs, status, approval)

## Output
Comms plan + run-of-show + comms tracker (drafted against the schema; created in Google Sheets only on
human approval), all in the deal's private workspace. Drafts only — publication, filing, and sends are
human-approved. Coordinates with `integration-tracker` so the announcement fires at the close gate (or
at signing for same-day).
