# Asset — Comms Tracker (Google Sheet schema)

The `deal-comms-runbook` generates this; Comms runs it; `integration-tracker` references it at the
close gate. Create in the deal's private workspace.

## Tab 1: `Asset Tracker`
| Column | Type | Notes |
|---|---|---|
| Asset | text | Press release, blog, internal all-hands, seller-employee, customer, FAQ, banner, social, AMA, IR |
| Audience | select | Public · Toast internal · Seller employees · Customers · Investors |
| DRI | person | Single owner |
| Draft status | select | Not started · Drafting · Draft ready · **Approved** |
| Approver | person | Who signs off (Legal for external; People Ops for employee) |
| Fire timing | text | Relative to signing/close + market hours (from run-of-show) |
| Notes | text | Links, dependencies |

## Tab 2: `Run-of-Show`
- The day-of sequence (from the run-of-show template) with order, window, owner, go/no-go checkbox.

## Tab 3: `Leak Log`
- Date · source · reach · content · response taken · escalated to · outcome.

## Tab 4: `8-K / IR`
- Materiality decision (Legal/IR) · filing path (1.01 / 2.01 / none) · filing window · IR sequence.

## Conventions
- **Approved** is a hard gate — the run-of-show cannot fire an asset that isn't Approved.
- Seller-employee comms ordered **before** any public asset.
- MNPI: restrict the sheet to the tent roster until announcement.
