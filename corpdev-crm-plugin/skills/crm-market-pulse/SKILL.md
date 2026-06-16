---
name: crm-market-pulse
description: >
  Daily scan of the restaurant and hospitality tech landscape, delivering intelligence insights
  for Toast CorpDev. Covers deal-making activity (M&A, partnerships, capital raises), competitor
  POS moves, customer wins, and AI developments across Toast's focus verticals. Outputs organized
  by insight rather than by vertical. Use whenever James says "run market pulse", "what's happening
  in the space", "any news in restaurant tech", "daily market scan", "what's new in [vertical]",
  or "run the daily intelligence scan". Also invoked as Phase 2 of crm-sourcing to provide market
  context before company discovery.
---

# CRM Market Pulse

Scans the restaurant and hospitality tech landscape daily and surfaces what matters to Toast CorpDev — deal flow, competitor moves, customer wins, and AI developments — organized as insights rather than a vertical-by-vertical report. The goal is to give James a fast, high-signal read on what's moving in the spaces Toast cares about.

**This skill scans and surfaces. It does not write to Notion or the CRM.**

---

## Inputs

| Input | Default | Override |
|---|---|---|
| **Daily briefing output** | Check session context first; invoke `daily-briefing` if not present | Pass briefing output directly to skip invocation |
| **Lookback window** | 7 days | "last 3 days", "last 30 days" (30-day window used when invoked from crm-sourcing) |
| **Vertical filter** | All primary verticals + competitors | "focus on AI for restaurants only", "primary verticals only" |
| **Depth** | Standard (named sources + broad web) | "headlines only" for a faster pass |

---

## Phase 1: Load Target Verticals

Read `references/target_verticals.md` from this skill's directory. This file contains:
- Primary and secondary verticals with descriptions and sub-areas
- Competitor watchlist
- Search keywords per vertical

**Do not query Notion for vertical data.** The reference file is the source of truth and is updated quarterly. If the file cannot be read, use the embedded fallback defaults at the end of this SKILL.md.

---

## Phase 2: Ingest Daily Briefing

The daily briefing covers newsletters (Money Stuff, Axios Pro Rata, StrictlyVC, Term Sheet, DealBook, Linas's Newsletter, Tomasz Tunguz, Benedict Evans) and surfaces deal flow, market moves, and tech news. Market Pulse filters that output through a vertical lens rather than re-reading the same sources.

**Check session context first:**
- If a daily briefing output has already been run and is available in this session (passed as input or visible in context), use it directly.
- If no briefing output is present, invoke the `daily-briefing` skill and use its output.
- Never invoke `daily-briefing` twice in the same session.

Extract from the briefing output any items that mention:
- Companies in the primary or secondary verticals
- Competitors from the watchlist
- Funding rounds, M&A activity, or partnerships involving restaurant / hospitality / SMB tech
- AI applications relevant to restaurants or retail

Tag these as `[from briefing]` in Phase 4 consolidation.

---

## Phase 3: Source Scanning

Run all three sub-phases in parallel.

### 3a. Trade Press — Always Scan

Search these restaurant and hospitality tech sources directly. These have the highest vertical specificity and often surface deals before general tech press.

**Restaurant / hospitality trade press:**
- Restaurant Business Online — restaurantbusinessonline.com
- Nation's Restaurant News — nrn.com
- QSR Magazine — qsrmagazine.com
- Restaurant Technology News — restauranttechnologynews.com
- Food on Demand — foodondemand.com
- Hospitality Technology — hospitalitytech.com

**Search strategy:** For each primary vertical keyword from the reference file, run:
```
site:restaurantbusinessonline.com OR site:nrn.com OR site:qsrmagazine.com OR site:restauranttechnologynews.com OR site:foodondemand.com OR site:hospitalitytech.com [vertical keyword]
```
Batch verticals to reduce query count. Scan all results within the lookback window.

### 3b. Deal / VC Press — Filter for Vertical Relevance

Search these sources for items relevant to the target verticals. Do not ingest everything — filter by vertical keyword before treating a result as a signal.

- Axios Pro Rata — axios.com/pro-rata
- DealBook (Andrew Ross Sorkin) — nytimes.com/dealbook
- Term Sheet (Fortune) — fortune.com/term-sheet
- StrictlyVC — strictlyvc.com
- TechCrunch — techcrunch.com
- The Information — theinformation.com
- WSJ — wsj.com
- Financial Times — ft.com

**Search strategy:**
```
site:axios.com OR site:nytimes.com OR site:fortune.com OR site:techcrunch.com OR site:wsj.com "[vertical keyword]" OR "[competitor name]"
```
Run once per primary vertical and once for each competitor name on the watchlist.

### 3c. Broad Web Scan — Catch-All

After named sources, run open web searches to catch items that don't appear in major publications — company blog posts, regional press, industry association announcements, LinkedIn-derived news aggregators.

**Per primary vertical:**
```
"[vertical keyword]" (funding OR acquisition OR partnership OR "customer win" OR launch) [lookback date range]
"[vertical keyword]" (AI OR "artificial intelligence") [lookback date range]
```

**Per competitor:**
```
"[competitor name]" (restaurant OR POS OR hospitality) [lookback date range]
```

Run all vertical and competitor queries. Apply credibility filter in Phase 4 — surface results from recognizable outlets only; skip SEO content farms and unattributed press release aggregators.

---

## Phase 4: Signal Identification

Review all content surfaced across Phases 2 and 3. Identify items that are materially meaningful to Toast CorpDev. The signal bar is intentionally open-ended — surface your judgment about what's worth James's attention.

### Particularly newsworthy — flag these explicitly

These signal types are high priority. When an item fits one of these, note the category in the insight output:

- **Deal-making**: M&A activity, capital raises (any meaningful round in the space), notable partnerships announced publicly
- **Competitor POS moves**: Product launches, pricing changes, new market entries, enterprise logo wins, or strategic pivots by Square, Lightspeed, Olo, Revel, Micros/Oracle, NCR, PAR, SpotOn, SkyTab (Shift4), Qu, Clover, Chowbus, DoorDash
- **Customer wins**: Named restaurant groups, chains, or operators announcing a platform switch or notable tech adoption
- **AI in the space**: New AI applications, model integrations, automation announcements, or VC thesis pieces on AI for restaurants / hospitality

### What to skip

- PR fluff with no material substance (product "enhancements" with no specifics, generic partnership announcements without terms or scope)
- Items already captured in the daily briefing and fully covered there (avoid duplicating briefing content verbatim — reference it but don't re-explain it)
- Items outside the lookback window
- Low-credibility sources: SEO blogs, unattributed wire pickups, content farms
- Items with no relevance to Toast's verticals or competitive landscape

---

## Phase 5: Consolidate & Output by Insight

Organize findings as insights, not vertical reports. Each insight should express a specific, distinct observation — a pattern, a move, a signal — not just "Company X raised money."

### Output format

```
**[Insight headline — the so-what in one line]**
[1–2 sentences of supporting evidence with source attribution]
Vertical: [tag from reference file] | [Deal-making / Competitor move / Customer win / AI / Other]
Relevance to Toast: [one sentence on why this matters]
Source: [publication] ([date])
```

**Ranking:** Lead with the most materially significant insights for Toast. Deal-making and competitor moves typically rank highest; general market color ranks lowest. Within the same category, lead with P0/P1 vertical items.

**Volume target:** 5–8 insights on an active news day. 2–3 on a quiet day. Do not pad with weak signals to hit a number — a short digest of strong signals beats a long list of noise.

**If a day is quiet:** Present what's available and note "Quiet news cycle — [N] signals surfaced." Do not manufacture insights.

### Digest footer

```
─────────────────────────────────────────────────────
[N] insights | Lookback: [window] | [date range]
Sources scanned: trade press, [briefing status: used existing / invoked fresh], deal/VC press, broad web
```

---

## Invocation from crm-sourcing

When invoked as Phase 2 of `crm-sourcing`:
- Use a 30-day lookback window instead of 7 days
- Skip the digest footer
- Return a structured summary of the hottest sub-verticals and most active signal types for use in Phase 3 company discovery weighting
- Format: "Based on market pulse, highest activity in: [sub-vertical 1], [sub-vertical 2]. Key themes: [theme 1], [theme 2]. Weight company discovery toward these areas."

---

## Fallback Defaults

If `references/target_verticals.md` cannot be read, use these defaults:

**Primary verticals:** Restaurant POS (incl. international), AI for Restaurants (Intelligence, Voice, Vision), Enterprise POS, Retail POS (Convenience, Beauty, Spa), Consumer, Tables / Reservations

**Secondary verticals:** Restaurant BOH (accounting, inventory, workforce), Restaurant Guest FOH (digital ordering, kiosk, loyalty, CRM), SMB Fintech (payments, banking, lending), Restaurant Suppliers

**Competitors:** Square, Lightspeed, Olo, Revel, Micros/Oracle, NCR (VYX), PAR Technology, SpotOn, SkyTab (Shift4), Qu, Clover (Fiserv), Chowbus, DoorDash

**Search keywords:** "restaurant POS", "restaurant technology", "restaurant AI", "voice AI restaurant", "enterprise POS", "retail POS", "table management", "restaurant reservations"
