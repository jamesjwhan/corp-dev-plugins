---
name: crm-sourcing
description: "Proactively discovers net-new companies and founders worth reaching out to — before they're in the CorpDev CRM. Reads target verticals from a shared reference file, runs a Market Pulse scan to identify the hottest sub-verticals, then searches trade press and the broader web for recently announced companies in those verticals. Filters out companies already tracked and delivers a ranked shortlist with context cards. Use this skill whenever James says \"who should I be reaching out to\", \"find new companies to talk to\", \"what's new in [vertical]\", \"run crm sourcing\", \"show me new targets\", \"what should I add to the pipeline\", \"any new deals I should know about\", \"expand the pipeline\", or any time James wants to proactively build deal flow with net-new prospects not yet in the CRM. Also trigger if James asks \"what am I missing\" or \"what companies should I be tracking\" after a daily briefing or signal monitor run."
---

# CRM Sourcing

Discovers net-new companies worth adding to the CorpDev pipeline. Reads Toast's current focus verticals from a shared reference file, runs a Market Pulse scan to identify where activity is highest, then searches trade press and the broader web for recently announced companies in those verticals. Filters out anything already tracked and delivers a ranked shortlist with enough context to decide who's worth a conversation.

**This skill discovers. crm-add-enrich logs.** When James decides to act on a candidate, hand off to crm-add-enrich to create the CRM record.

**Complements the other CRM skills:**
- `crm-signal-monitor` watches companies already in the CRM for new developments
- `crm-market-pulse` scans the landscape for trends and insights — this skill runs it as Phase 2 before company discovery
- `startup-meeting-manager` handles meetings and logging for companies James already knows
- `crm-sourcing` surfaces the net-new companies that aren't in the picture yet

---

## Inputs

Minimal input required. James can optionally override:

- **Vertical filter**: "focus on Voice AI" or "restaurant tech only"
- **Construct filter**: "M&A targets only" or "investment candidates only"
- **Lookback window**: how far back to scan for new announcements (default: 30 days)
- **Market pulse output** *(optional)*: structured output from a prior crm-market-pulse run. When passed by the crm-auto-updater or from a cowork daily schedule, Phase 2 is satisfied from this input and crm-market-pulse is not re-invoked.

Stage is shown on context cards as informational context, not used as a filter. Seed-stage companies are always included — they're often the most interesting M&A or early investment targets.

---

## Phase 1: Load Context

Two sub-steps, run in sequence. This phase is fast — no Notion inference, no analysis.

### 1a. Exclusion List (CRM de-duplication)

Query the CorpDev CRM Notion database to pull all company names regardless of priority or status. Every name goes into an exclusion list. Any candidate surfaced in later phases that fuzzy-matches a name on this list is silently dropped before being presented to James — he already knows about these companies.

**CorpDev CRM data source ID:** `3b8058a353a98326b290013effbd3bf9`

If the CRM is unavailable, skip de-duplication, note the gap, and continue. Use Toast's known focus areas as a fallback target profile.

### 1b. Load Target Verticals

Read the shared reference file: `crm-market-pulse/references/target_verticals.md` (relative to the skills directory root).

This file contains:
- Primary and secondary verticals with descriptions and sub-areas
- Competitor watchlist
- Search keywords per vertical

**Do not infer target verticals from the CRM.** The reference file is the source of truth and is updated quarterly. If the file cannot be read, use the embedded fallback defaults at the end of this SKILL.md.

---

## Phase 2: Market Pulse

Use market pulse output to identify where activity is highest in Toast's focus verticals before beginning company discovery. Use the output to weight Phase 3 — sub-verticals with the most deal-making and funding activity get the deepest search effort.

**Source priority (check in order):**
1. If a `market_pulse_output` was passed as input (e.g., from the crm-auto-updater or a cowork daily schedule), use it directly — do not invoke crm-market-pulse.
2. If crm-market-pulse has already been run in this session and its output is visible in context, use it directly.
3. Otherwise, invoke `crm-market-pulse` with a **30-day lookback window** and use its output.

Never invoke `crm-market-pulse` more than once in the same session.

Market Pulse output feeds Phase 3 as a weighting signal:
> "Based on market pulse, highest activity in: [sub-vertical 1], [sub-vertical 2]. Key themes: [theme 1], [theme 2]. Prioritizing these in company discovery."

Do not present the full Market Pulse digest to James during a sourcing run — use it internally and surface only the weighting rationale. James can run crm-market-pulse separately for the full digest.

---

## Phase 3: Company Discovery

Search for recently announced companies in Toast's target verticals that are not in the CRM. Two search strategies, run in parallel — both feed the same candidate pool.

**Lookback window:** default 30 days; apply consistently across both strategies.

### 3a. Trade Press + Restaurant Tech Sites

Search named publications with high vertical specificity. Restaurant tech trade press surfaces deals before general tech outlets and often covers companies too small or niche to hit TechCrunch.

**Restaurant / hospitality trade press:**
- restaurantbusinessonline.com
- nrn.com (Nation's Restaurant News)
- qsrmagazine.com
- restauranttechnologynews.com
- foodondemand.com
- hospitalitytech.com

**General tech / deal press:**
- techcrunch.com
- axios.com/pro-rata
- fortune.com/term-sheet
- theinformation.com
- strictlyvc.com
- wsj.com
- ft.com

**Search strategy:** For each primary vertical, run:
```
site:restaurantbusinessonline.com OR site:nrn.com OR site:qsrmagazine.com OR site:restauranttechnologynews.com OR site:foodondemand.com [vertical keyword] funding OR raises OR launch
site:techcrunch.com OR site:axios.com OR site:fortune.com "[vertical keyword]" ("Series A" OR "Series B" OR "seed" OR funding OR raises)
```

Extract for each result: company name, what they do, funding stage and amount, lead investor, source URL, publication date, vertical.

### 3b. Broad Web Search

Open web searches to catch companies that don't appear in major publications — company blog posts, regional press, industry association announcements, accelerator cohorts.

**Per primary vertical (from reference file keywords):**
```
"[vertical keyword]" ("Series A" OR "Series B" OR seed OR funding OR raises OR launch) [lookback date range]
"[vertical keyword]" startup OR "new company" [lookback date range]
```

**Cross-source deduplication:** If the same company appears in both 3a and 3b results, keep one entry and note both sources — it is a stronger signal, not noise.

**Drop immediately:**
- Companies clearly outside Toast's addressable space (pure B2C consumer with no B2B angle, deep biotech, hardware with no software play, enterprise-only with no SMB or restaurant angle)
- Companies with no apparent relevance to restaurants, hospitality, SMB, payments, or adjacent ops
- Low-credibility sources: SEO content farms, unattributed press release aggregators

---

## Phase 4: Filter, Score & Rank

### 4a. CRM De-duplication

Cross-reference every candidate against the exclusion list from Phase 1a. Use fuzzy matching — "Olo Inc" matches "Olo", "HiAuto, Inc." matches "HiAuto". Drop any match silently.

When unsure if a candidate matches a CRM company, err on the side of including it and flag: "[Company] may already be in CRM as [similar name] — confirm before adding."

### 4b. Relevance Scoring

For each remaining candidate, assign a fit score (1–5) based on alignment with the primary and secondary verticals from Phase 1b, weighted toward the highest-activity sub-verticals identified by Market Pulse in Phase 2.

| Score | What it means |
|---|---|
| 5 | Strong fit: directly in a primary vertical, right funding stage, clear M&A or investment angle for Toast; or in a Market Pulse high-activity sub-vertical |
| 4 | Good fit: adjacent primary vertical, or right vertical with stage uncertainty |
| 3 | Plausible fit: secondary vertical or tangential relevance to Toast's areas |
| 1–2 | Weak fit: loosely connected; surface only if fewer than 5 candidates score 3+ |

Weight vertical alignment more heavily than funding stage. A perfect-vertical seed company is more interesting than a wrong-vertical Series B.

### 4c. Rank and Trim

Sort by score descending. Within the same score, prefer recency (more recently announced first), and prefer sub-verticals flagged as high-activity by Market Pulse. Target 5–10 candidates total. If more than 10 score 3+, keep the top 10. If fewer than 3 score 3+, loosen the filter and surface what's available with a note.

---

## Phase 5: Build Context Cards & Present

Present the ranked shortlist. Each candidate gets a context card:

```
[Rank]. [Company Name] · [Vertical / Sub-category] · [Stage] · Fit: [X/5]
[1–2 sentences: what they do and the specific reason they're relevant to Toast]
Funding: [amount, round, lead investor, date — or "undisclosed" if not public]
Fit signal: [one sentence tying the company to Toast's P0/P1 focus areas or Market Pulse activity]
Source: [publication] · [date] · [URL]
```

After the list, show:

```
─────────────────────────────────────────────────────────
[N] candidates | [M] sources scanned | [K] CRM companies screened out | Lookback: [window]
Market Pulse weighting: highest activity in [sub-vertical 1], [sub-vertical 2]

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
- Funding, investors, location from context card
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
| CRM unavailable | Skip de-duplication, note "CRM unavailable — de-duplication skipped". Continue with sourcing. |
| target_verticals.md not found | Use fallback defaults embedded below. Note that the reference file was not found. |
| crm-market-pulse unavailable | Skip Phase 2 weighting. Distribute Phase 3 effort evenly across primary verticals. |
| Trade press searches return 0 results | Try broader vertical queries. If still empty, note gap and continue to 3b. |
| Broad web search returns 0 results | Expected during slow news cycles — note and continue. |
| Fewer than 3 candidates after filtering | Surface what exists with a note: "Slow cycle — only [N] net-new candidates found. Consider broadening the lookback window." |
| Company already in CRM slips through | Drop silently in Phase 4a — expected and not an error. |

---

## Fallback Defaults

If `crm-market-pulse/references/target_verticals.md` cannot be read, use these defaults:

**Primary verticals:** Restaurant POS (incl. international), AI for Restaurants (Intelligence, Voice, Vision), Enterprise POS, Retail POS (Convenience, Beauty, Spa), Consumer, Tables / Reservations

**Secondary verticals:** Restaurant BOH (accounting, inventory, workforce), Restaurant Guest FOH (digital ordering, kiosk, loyalty, CRM), SMB Fintech (payments, banking, lending), Restaurant Suppliers

**Competitors:** Square, Lightspeed, Olo, Revel, Micros/Oracle, NCR (VYX), PAR Technology, SpotOn, SkyTab (Shift4), Qu, Clover (Fiserv), Chowbus, DoorDash

**Search keywords:** "restaurant POS", "restaurant technology", "restaurant AI", "voice AI restaurant", "enterprise POS", "retail POS", "table management", "restaurant reservations"

---

## Notes

- This skill surfaces net-new companies only. It does not monitor companies already in the CRM — that is crm-signal-monitor's job.
- Target verticals are loaded from a shared reference file, not inferred from the CRM on each run. Update the reference file quarterly or when CRM P0/P1 priorities shift.
- Fit scores are ranking signals, not verdicts. James decides who's actually worth a conversation.
- The goal is a tight, high-quality shortlist — 5–10 genuinely relevant leads beats a long list of loose matches.
- James's timezone is America/Los_Angeles.
