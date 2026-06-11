# Closing-Module Library — Index & Scoping

The `deal-closing-planner` composes a sign-to-close plan from these modules. Read this index first,
decide which modules are in scope from the deal facts, then read only those module files.

## Modules

| Module file | Covers | Default scope |
|---|---|---|
| `closing-mechanics.md` | CP tracker structure, flow-of-funds, closing certificates, the close gate | **Always in** |
| `regulatory-and-consents.md` | HSR reportability, foreign merger control, CFIUS; third-party change-of-control consents | Conditional |
| `stockholder-and-financial.md` | Stockholder package (consents, transmittal, payment spreadsheet, 280G, FIRPTA); financial/tax/accounting close-out | Stockholder = if equity holders; Financial = **always** |
| `day1-readiness.md` | People/HR, IT/Security, Product/Commercial Day-1 tracks | People = if employees transfer; IT = **always**; Product = if customers/product transfer |

> Announcement comms is **not** a closing module — it's owned by the `deal-comms-runbook` skill.
> Reference it in the plan; don't duplicate it.

## Scoping decision table

Work top to bottom. For each row, the deal fact on the left puts the item in scope.

| Deal fact | Effect on the plan |
|---|---|
| Deal consideration **> ~$133.9M** (2026 HSR size-of-transaction) | HSR section in scope — run the reportability test in `regulatory-and-consents.md`. If reportable, this forces a **deferred close** (mandatory waiting period). |
| Cross-border (foreign target or foreign acquirer interest) | Foreign merger control / CFIUS review — usually N/A for Toast as a US acquirer of a US target; flag only if facts warrant. |
| Material customer contracts / leases / IP licenses / debt with **change-of-control** clauses | Consents section in scope; each required consent is a **P0 close-blocker**. |
| Target has **equity holders** to pay (any preferred/common/options) | Stockholder section in scope (written consent, transmittal, payment spreadsheet). |
| Any **golden-parachute** exposure (accelerated equity to disqualified individuals) | 280G analysis + cleansing vote in scope. |
| Target has **foreign sellers** | FIRPTA certificate in scope. |
| Deal has **debt to be repaid at close** | Payoff letters + lien releases in scope. |
| **Employees transferring** | People/HR Day-1 track in scope. International employees/contractors → add immigration + entity/contractor vendor items (long-lead). |
| **Customers / product transferring** | Product/Commercial Day-1 track in scope (customer comms, support transition, sunset timeline). |
| **Earnout or holdback** in the deal | Note for Stage 7 `deal-integration-plan` (earnout tracking) — don't build it here, just flag continuity. |

## Archetype shortcut

- **No HSR, no required third-party consents, no financing** → **same-day close** likely.
  Lean plan; close gate merges into signing; most items move to immediately pre-signing.
- **Any** of HSR-reportable, required consents, or financing → **deferred close**. Full plan, dated
  timeline, long-lead items (PPA valuation, immigration, HSR waiting period) start at signing.
