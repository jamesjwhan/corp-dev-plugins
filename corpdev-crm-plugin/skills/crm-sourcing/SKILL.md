---
name: crm-sourcing
description: "Proactively discovers net-new companies and founders worth reaching out to — before they're in the CorpDev CRM. Scans VC portfolio pages and deal newsletters for recently announced companies in Toast's target verticals, infers focus areas from P0/P1 CRM priorities, filters out companies already tracked, and delivers a ranked shortlist with context cards. Use this skill whenever James says \"who should I be reaching out to\", \"find new companies to talk to\", \"what's new in [vertical]\", \"run crm sourcing\", \"show me new targets\", \"what should I add to the pipeline\", \"any new deals I should know about\", \"expand the pipeline\", or any time James wants to proactively build deal flow with net-new prospects not yet in the CRM. Also trigger if James asks \"what am I missing\" or \"what companies should I be tracking\" after a daily briefing or signal monitor run."
---
 
# CRM Sourcing
 
Discovers net-new companies worth adding to the CorpDev pipeline. Infers Toast's current focus
verticals from the highest-priority CRM companies, scans VC portfolio pages and deal newsletters
for recently announced companies in those verticals, filters out anything already tracked, and
delivers a ranked shortlist with enough context to decide who's worth a conversation.
 
**This skill discovers. crm-add-enrich logs.** When James decides to act on a candidate, hand off
to crm-add-enrich to create the CRM record.
 
**Complements the other CRM skills:**
- `crm-signal-monitor` watches companies already in the CRM for new developments
- `startup-meeting-manager` handles meetings and logging for companies James already knows
- `crm-sourcing` surfaces the net-new companies that aren't in the picture yet
---
 
## Inputs
 
Minimal input required. James can optionally override:
 
- **Vertical filter**: "focus on Voice AI" or "restaurant tech only"
- **Construct filter**: "M&A targets only" or "investment candidates only"
- **Lookback window**: how far back to scan for new announcements (default: 30 days)
- **Source filter**: "VC portfolio only" or "newsletters only"
Stage is shown on context cards as informational context, not used as a filter. Seed-stage companies
are always included — they're often the most interesting M&A or early investment targets.
 
---
 
## Phase 1: Load CRM Context
 
Query the Notion CorpDev CRM to build two things. This is a read-only reference step.
 
**CorpDev CRM data source ID:** `3b8058a353a98326b290013effbd3bf9`
 
### 1a. Exclusion list (de-duplication)
 
Pull all company names from the CRM regardless of priority or status. Every name goes into an
exclusion list. Any candidate surfaced in later phases that fuzzy-matches a name on this list is
silently dropped before being presented to James — he already knows about these companies.
 
### 1b. Target vertical inference
 
Look at the Pillar > Category > Sub-category and Construct distribution of P0 and P1 companies.
The verticals with the most P0/P1 concentration are Toast's current focus areas. Use this to
build an internal target profile (don't show it to James — just use it to guide scoring):
 
- **Primary verticals**: the Pillar > Category buckets most represented among P0/P1 companies
- **Likely construct interest**: M&A, Investment, or both — inferred from P0/P1 Construct values
If the CRM is unavailable, skip de-duplication and note the gap. Continue with sourcing using
Toast's known focus areas (restaurant/hospitality tech, SMB SaaS, payments, kitchen ops, workforce
management) as a fallback target profile.
 
---
 
## Phase 2: Scan VC Portfolio Pages
 
Search for recently-announced portfolio additions at top-tier VCs that invest in restaurant tech,
hospitality, SMB SaaS, and adjacent verticals.
 
**VC sources to scan:**
 
Generalist tier-1:
a16z, Sequoia, Lightspeed, Bessemer Venture Partners, General Catalyst, Khosla Ventures,
Lead Edge Capital, Accel, Index Ventures, NEA, Menlo Ventures, Insight Partners, Thrive Capital,
Battery Ventures, GV (Google Ventures), Founders Fund, Y Combinator (recent batches)
 
Vertical SaaS specialists:
OpenView Partners
 
Fintech specialists:
Ribbit Capital, QED Investors, Nyca Partners, Better Tomorrow Ventures
 
**What to look for:**
- New portfolio additions or investment announcements within the lookback window
- Sectors relevant to Toast: restaurant tech, hospitality, food service, SMB vertical SaaS,
  payments/fintech, kitchen/operations software, delivery/logistics, workforce management,
  table management, loyalty/CRM, back-of-house automation
**Search strategy:**
Run targeted queries per VC or batched:
```
site:a16z.com OR site:sequoiacap.com portfolio restaurant OR hospitality OR "food service" 2025
"[VC name]" new investment "restaurant tech" OR "SMB SaaS" OR "hospitality" 2025
```
For YC, check recent batch announcements:
```
site:ycombinator.com W25 OR S25 restaurant OR hospitality OR "food service"
```
 
**For each candidate found, extract:**
- Company name
- What they do (1–2 sentences from the VC announcement or company website)
- Funding stage and amount (if announced)
- Lead investor
- Source URL and publication date
- Apparent vertical / use case
**Drop immediately:**
- Companies clearly outside Toast's addressable space (pure B2C consumer, deep biotech, hardware
  with no software play, enterprise-only with no SMB angle)
- Companies with no apparent relevance to restaurants, hospitality, SMB, payments, or adjacent ops
---
 
## Phase 3: Scan Newsletters & Press
 
Search curated deal-flow sources for recently announced companies — new funding rounds, launches,
or notable coverage — in relevant verticals.
 
**Sources:**
 
General tech/VC press:
- Axios Pro Rata — axios.com/pro-rata
- TechCrunch — techcrunch.com
- StrictlyVC — strictlyvc.com
- Term Sheet (Fortune) — fortune.com/term-sheet
- The Information — theinformation.com
- DealBook (NYT) — nytimes.com/dealbook
Restaurant tech trade press (highest deal specificity — check these first):
- Restaurant Business Online — restaurantbusinessonline.com
- QSR Magazine — qsrmagazine.com
- Restaurant Technology News — restauranttechnologynews.com
**Search strategy:**
Run vertical-focused funding queries:
```
site:techcrunch.com "restaurant" OR "hospitality" OR "food service" funding 2025
site:axios.com "SMB" OR "restaurant tech" funding round 2025
site:restaurantbusinessonline.com OR site:qsrmagazine.com OR site:restauranttechnologynews.com funding raises 2025
"restaurant tech" OR "hospitality software" "Series A" OR "Series B" funding 2025
"kitchen management" OR "food service" startup funding 2025
```
 
Extract the same fields as Phase 2.
 
**Cross-source deduplication:** If the same company appears in both Phase 2 and Phase 3 results,
keep one entry and note both sources — it's a stronger signal, not noise.
 
---
 
## Phase 4: Filter, Score & Rank
 
### 4a. CRM de-duplication
 
Cross-reference every candidate against the exclusion list from Phase 1a. Use fuzzy matching —
"Olo Inc" matches "Olo", "HiAuto, Inc." matches "HiAuto". Drop any match silently.
 
### 4b. Relevance scoring
 
For each remaining candidate, assign a fit score (1–5) based on alignment with the target profile
from Phase 1b. The goal isn't a precise score — it's a ranking signal to help James focus on the
most relevant leads first.
 
| Score | What it means |
|---|---|
| 5 | Strong fit: directly in a P0/P1 vertical, right funding stage, clear M&A or investment angle for Toast |
| 4 | Good fit: adjacent vertical, or right vertical with stage uncertainty |
| 3 | Plausible fit: tangential to Toast's areas but worth knowing about |
| 1–2 | Weak fit: loosely connected; surface only if fewer than 5 candidates score 3+ |
 
When scoring, weight vertical alignment more heavily than funding stage — a perfect-vertical
seed company is more interesting than a wrong-vertical Series B.
 
### 4c. Rank and trim
 
Sort by score descending. Within the same score, prefer recency (more recently announced first).
Target 5–10 candidates total. If more than 10 score 3+, keep the top 10. If fewer than 3 score
3+, loosen the filter and surface what's available with a note.
 
---
 
## Phase 5: Build Context Cards & Present
 
Present the ranked shortlist. Each candidate gets a context card:
 
```
[Rank]. [Company Name] · [Vertical / Sub-category] · [Stage] · Fit: [X/5]
[1–2 sentences: what they do and the specific reason they're relevant to Toast]
Funding: [amount, round, lead investor, date — or "undisclosed" if not public]
Fit signal: [one sentence tying the company to Toast's P0/P1 focus areas]
Source: [VC firm or publication] · [date] · [URL]
```
 
After the list, show:
 
```
─────────────────────────────────────────
[N] candidates | [M] sources scanned | [K] CRM companies screened out | Lookback: [window]
 
To add any of these to the CRM: "add [company]" → I'll hand off to crm-add-enrich.
To draft an outreach note for one: "draft outreach for [company]".
To run signal monitor on your existing pipeline: trigger crm-signal-monitor.
```
 
If no candidates survive filtering, present:
```
No net-new candidates found in this scan. [N] companies screened against CRM. Sources: [list].
Consider broadening the lookback window or adjusting the vertical filter.
```
 
---
 
## Phase 6: Actions on Candidates
 
### "Add [company]" → hand off to crm-add-enrich
 
Package the context card data as structured input:
- Company name
- Inferred Pillar > Category > Sub-category (flag it as inferred — James should confirm)
- Funding, investors, location from enrichment
- Source URL as a reference link
crm-add-enrich will validate taxonomy, enrich further, propose scores, and write the Notion record.
 
### "Draft outreach for [company]"
 
Write a short, personalized cold outreach message James could send to the founder. Format:
 
```
Subject: [Company] x Toast
 
[2–3 sentences: specific reason Toast is interested in this space → what would make a conversation
valuable → light ask for a call. No AI-speak. No generic flattery. Write like a thoughtful corp dev
exec who actually read the announcement and has a real point of view on the space.]
```
 
Keep it tight — the best cold notes are under 100 words. Offer to adjust tone or angle.
 
---
 
## Failure Modes
 
| Issue | Response |
|---|---|
| CRM unavailable | Skip de-duplication, note "CRM unavailable — de-duplication skipped". Use fallback target profile. |
| VC searches return 0 results | Try broader vertical queries. If still empty, note gap and continue to Phase 3. |
| Newsletter searches return 0 results | Expected during slow news cycles — note and continue. |
| Fewer than 3 candidates after filtering | Surface what exists with a note: "Slow cycle — only [N] net-new candidates found. Consider broadening the lookback window." |
| Company already in CRM slips through | Drop silently in Phase 4a — this is expected and not an error. |
| Ambiguous company match (CRM de-dup) | When unsure if a candidate matches a CRM company, err on the side of including it and flag: "[Company] may already be in CRM as [similar name] — confirm before adding." |
 
---
 
## Notes
 
- This skill surfaces net-new companies only. It does not monitor companies already in the CRM —
  that's crm-signal-monitor's job.
- Target vertical inference (Phase 1b) is a heuristic. If the inferred focus areas seem off,
  James can override with explicit vertical keywords at invocation time.
- Fit scores are ranking signals, not verdicts. James decides who's actually worth a conversation.
- The goal is a tight, high-quality shortlist — 5–10 genuinely relevant leads beats a long list
  of loose matches.
- James's timezone is America/Los_Angeles.