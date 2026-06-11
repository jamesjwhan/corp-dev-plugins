# Module — Regulatory Clearance & Third-Party Consents

Owner DRI: **Legal** (with outside antitrust counsel for HSR). Everything here is preliminary triage
for counsel to confirm — never a legal determination.

## A. HSR reportability (US antitrust)

Run this test to decide whether the deal likely requires a Hart-Scott-Rodino premerger filing. If it
does, the deal **cannot close** until the mandatory waiting period expires — this forces a deferred
close and is a hard P0 close-blocker.

**Current thresholds (effective Feb 17, 2026 — confirm the version in effect on the expected closing
date, as these adjust annually each ~February):**
- **Size-of-transaction:** deal valued **≤ $133.9M → not reportable.** Above that, continue.
- **$133.9M – $535.5M:** reportable **only if** the size-of-person test is also met — one party has
  ≥ $267.8M in annual net sales or total assets **and** the other has ≥ $26.8M.
- **> $535.5M:** reportable regardless of party size (absent an exemption).

For Toast's profile: Toast itself clears the large side of the size-of-person test, so for a target
valued between $133.9M and $535.5M, reportability turns on whether the **target** has ≥ $26.8M in
sales/assets. Most acqui-hires and small tuck-ins sit below $133.9M and are **not reportable** — a
quick "not reportable" memo for the file and move on. Larger strategic deals will be.

**If reportable:** flag to Legal/antitrust counsel to prepare and file the HSR form (note the form is
substantially more burdensome since the 2025 overhaul — build lead time), pay the filing fee, and
observe the waiting period. Add to the plan: *file HSR (Legal), observe waiting period, no closing or
gun-jumping integration until expiry.*

**Gun-jumping:** if HSR-reportable, the parties remain separate until close and **cannot** prematurely
integrate or share competitively sensitive information. The clean team persists through close. Put an
explicit gun-jumping note on any pre-close integration item.

## B. Foreign merger control / CFIUS

Usually **not in scope** for Toast as a US acquirer of a US target. Flag only if: the target has
material foreign operations/revenue triggering a foreign merger-control regime, or there's a
foreign-investment/national-security angle (rare here). If flagged, route to counsel; treat as a
deferred-close gating condition.

## C. Third-party change-of-control consents

For each category, identify agreements whose change-of-control or anti-assignment clauses require the
counterparty's consent to the transaction. Each required consent is a **P0 close-blocker** (or a
post-close cure if the agreement permits and the risk is acceptable — Legal's call).

Checklist:
- **Material customer contracts / MSAs** — change-of-control or assignment-consent clauses
- **Real-estate leases** — landlord consent to assignment/change of control
- **Key IP licenses** (inbound and outbound) — including open-source obligations surfaced in tech diligence
- **Debt instruments** — change-of-control triggers, required payoff
- **Material vendor / partner agreements** — assignment consent
- **Government / regulatory licenses or permits** — transferability

Output: a **consent tracker** (rows: counterparty, agreement, clause, consent required Y/N, DRI,
status, P0/P1). Consent *requests* are drafted for Legal to send — never sent by the skill/agent.

## In/out summary for the plan
- HSR section in **only if** deal size clears the size-of-transaction screen.
- Foreign/CFIUS in **only if** cross-border facts warrant — default out.
- Consents in **if** any material agreement carries change-of-control/assignment terms (run the checklist).
