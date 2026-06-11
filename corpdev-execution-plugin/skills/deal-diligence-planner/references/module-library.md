# DD-Module Library — Index & Scoping

The `deal-diligence-planner` composes a confirmatory diligence plan from these modules. Read this
index first, decide which workstreams are in / light / out from the deal facts and the Deal Memo's
**key assertions**, then read only the modules in scope.

## Modules

| Module file | Workstream | DRI | Default scope |
|---|---|---|---|
| `legal-dd.md` | Legal | Legal | **Always in** |
| `technical-dd.md` | Technical & product | Eng (clean team) | In if a codebase/product transfers |
| `financial-dd.md` | Financial | Finance | In if revenue is material |
| `people-dd.md` | People / HR | People Ops | In if employees transfer |
| `tax-accounting-dd.md` | Tax & accounting | Tax / Accounting | **Always in** |
| `customer-dd.md` | Customer | Corp Dev + Product | In if there are customers |

## Depth by archetype

| Archetype | Legal | Technical | Financial | People | Tax/Acct | Customer |
|---|---|---|---|---|---|---|
| **Talent acqui-hire** (pre-rev) | IP + employment heavy | Team + code-quality | Light (no QoE) | **Deep** | Light | Out / minimal |
| **Tuck-in** (some revenue) | Moderate | Integration-focused | Real (QoE) | Moderate | Moderate | Real |
| **Platform** (material revenue) | Full | **Full** (security, arch) | **Deep** (QoE, cohorts) | Moderate | Full (cross-border) | **Deep** (concentration) |

## Scoping principle — anchor to assertions

The Deal Memo lists **key assertions** (what the thesis depends on) and **flagged risks**. Each one
maps to a workstream and becomes a **P0** item:
- "130% NRR" → financial-dd (retention) + customer-dd (cohort/interviews) **P0**
- "World-class ML team" → people-dd (key-person) + technical-dd (team assessment) **P0**
- "Clean IP, no open-source contamination" → legal-dd (IP) + technical-dd (license review) **P0**
- "No customer concentration" → customer-dd (concentration) **P0**

If an assertion has no diligence item testing it, the plan has a hole. Cross-check assertions →
P0 coverage before finalizing.
