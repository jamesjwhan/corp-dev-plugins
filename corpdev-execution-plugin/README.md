# corpdev-execution

Deal execution and coordination for Toast Corp Dev — the **back half** of the deal lifecycle
(Stages 5–7 of the Deal Operating Map). This plugin is the execution counterpart to the two
research/sourcing plugins:

| Plugin | Covers | Lifecycle |
|---|---|---|
| `corp-dev-analyst` | research, diligence analysis, deal memo | Stages 0–3 |
| `corpdev-crm` | sourcing, pipeline, CRM | Stages 0–1 |
| **`corpdev-execution`** | **diligence/closing planning, comms, integration** | **Stages 5–7** |

## What's inside

**Skills**
- `deal-closing-planner` — scopes a deal-specific **sign-to-close plan** (which CPs, consents, and Day-1 tracks apply), composing from the closing-module library; feeds `integration-tracker`.
- `deal-diligence-planner` — scopes a deal-specific **confirmatory diligence plan**, composing from the DD-module library; feeds `diligence-coordinator`.
- `deal-comms-runbook` — announcement decision tree, asset checklist, run-of-show, 8-K plan, leak/rumor playbook.
- `deal-integration-plan` — long-term integration plan, OKRs, performance dashboard.

**Agents**
- `diligence-coordinator` — runs the plan `deal-diligence-planner` produces (Stages 3 & 5).
- `integration-tracker` — runs the plan `deal-closing-planner` produces, then the Stage 7 OKR/performance cadence.

*All skills and agents are written and complete; none has been run against a live deal yet (test before relying on them). Tool wiring (Drive/Sheets/Gmail/Slack) is confirmed when the plugin is loaded in Claude Code.*

## Architecture

Two **planner → coordinator** pairs. A planner skill *generates* a deal-specific plan by composing
from a reusable component library; a coordinator agent *runs* that plan to closure. Planners and
agents **draft and surface only** — they never send comms or execute mechanics (filings, wires,
funds flow) without human approval.

## Conventions

- Trackers and structured outputs → **Google Sheets** — but skills/agents **draft** the content
  against a schema and **ask the human to approve creating the sheet**; they never write to Corp Dev
  Spaces / Drive on their own.
- Narrative plans, runbooks → **Markdown** in Corp Dev Spaces (Drive), on approval.
- Skills read the **Deal Memo** (from `deal-memo-writer`) as primary deal context.
- **Drafts and surfaces only** — nothing sends comms, files, moves money, or creates shared artifacts
  without explicit human approval.
- Nothing here is legal or tax advice; regulatory flags (e.g., HSR) are preliminary and route to counsel.
