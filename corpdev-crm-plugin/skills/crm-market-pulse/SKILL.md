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

---

## Phase 1: Load Target Verticals

Read `references/target_verticals.md` from this skill's directory. This file contains:
- Primary and secondary verticals with descriptions and sub-areas
- Competitor watchlist
- Search keywords per vertical

**Do not query Notion for vertical data.** The reference file is the source of truth and is updated quarterly. If the file cannot be read, use the embedded fallback defaults at the end of this SKILL.md.

---

## Phase 2: Ingest Daily Briefing

The daily briefing covers newsletters and deal press — Money Stuff, Axios Pro Rata, StrictlyVC, Term Sheet, DealBook, Linas's Newsletter, Tomasz Tunguz, Benedict Evans — and surfaces deal flow, market moves, and tech news. Market Pulse filters that output through a vertical lens rather than re-reading those same sources.

**Check session context first:**
- If a daily briefing output has already been run and is available in this session (passed as input or visible in context), use it directly.
- If no briefing output is present, invoke the `daily-briefing` skill and use its output.
- Never invoke `daily-briefing` twice in the same session.

Extract from the briefing output any items that mention:
- Companies in the primary or secondary verticals
- Competitors from the watchlist
- Funding rounds, M&A activity, or partnerships involving restaurant / hospitality / SMB tech
- AI applications relevant to restaurants or retail

Tag these as `[from briefing]` and carry them forward into Phase 4 consolidation.

---

## Phase 3: Source Scanning

Run both phases in parallel. Do not re-scan sources already covered by the daily briefing in Phase 2.

### 3a. Trade Press — Date Filter Only

Search the following restaurant and hospitality tech publications using a date filter only — no keyword requirements. Everything published on these sites is in scope by definition; Phase 4 handles relevance filtering.

**Sources:**
- Restaurant Business Online — restaurantbusinessonline.com
- Nation's Restaurant News — nrn.com
- QSR Magazine — qsrmagazine.com
- Restaurant Technology News — restauranttechnologynews.com
- Food on Demand — foodondemand.com
- Hospitality Technology — hospitalitytech.com

**Search strategy:**
```
site:restaurantbusinessonline.com newer_than:[lookback]
site:nrn.com newer_than:[lookback]
site:qsrmagazine.com newer_than:[lookback]
site:restauranttechnologynews.com newer_than:[lookback]
site:foodondemand.com newer_than:[lookback]
site:hospitalitytech.com newer_than:[lookback]
```

Skim headlines across all results. Pull full content only for items that look relevant at the headline level — Phase 4 will make the final call on what surfaces.

### 3b. Broad Web Scan

Three query types, all run in parallel. These complement the trade press scan and the daily briefing by catching items that don't appear in those sources — PR wire announcements, company blog posts, regional press, and general web coverage.

**Query type 1 — Per named competitor (no vertical keyword):**
```
"[competitor name]" newer_than:[lookback]
```
Run once per competitor from the watchlist in `references/target_verticals.md`. No "restaurant OR POS" requirement — that gate was responsible for missing CEO departures, divestitures, and customer win announcements that don't use vertical language in their headline. Phase 4 filters irrelevant results.

**Query type 2 — PR wire scan:**
```
site:businesswire.com OR site:prnewswire.com OR site:globenewswire.com "[vertical keyword]" OR "[competitor name]" newer_than:[lookback]
```
Run once per primary vertical keyword and once per competitor name. PR wires are where companies publish funding announcements, customer wins, and partnerships before press picks them up. This is the source most likely to catch announcements from smaller companies not yet covered in trade press.

**Query type 3 — Per vertical, general web:**
```
"[vertical keyword]" (funding OR acquisition OR partnership OR launch OR "customer win") newer_than:[lookback]
```
Run once per primary vertical using keywords from `references/target_verticals.md`. Catches broader web coverage not indexed on the named trade sources.

---

## Phase 4: Signal Identification

Review all content surfaced across Phases 2 and 3. This phase does the filtering work — queries were cast wide intentionally; Phase 4 is where relevance is determined.

### What to surface

The signal bar is intentionally open-ended — surface judgment about what's worth James's attention. These categories are particularly newsworthy and should be flagged explicitly when they appear:

- **Deal-making**: M&A activity, capital raises (any meaningful round in the space), notable partnerships announced publicly
- **Competitor POS moves**: Product launches, pricing changes, new market entries, enterprise logo wins, leadership changes, divestitures, or strategic pivots by any company on the competitor watchlist
- **Customer wins**: Named restaurant groups, chains, or operators announcing a platform switch or notable tech adoption
- **AI in the space**: New AI applications, model integrations, automation announcements, or VC thesis pieces on AI for restaurants / hospitality

### What to drop

- Banking, insurance, or financial services news about competitors with no restaurant/hospitality angle (e.g., Fiserv regulatory filings, Lightspeed retail-only news)
- PR fluff with no material substance — generic "enhancements" with no specifics, boilerplate partnership announcements without scope or terms
- Items already fully covered in the daily briefing (reference it, don't restate it)
- Items outside the lookback window
- Low-credibility sources: SEO blogs, unattributed wire pickups, content farms
- Promotional / consumer-facing announcements (DoorDash discount campaigns, etc.)

### Escalate secondary vertical items

Secondary verticals receive the same scanning depth as primary in Phases 3a and 3b. Apply the same signal bar — if a deal in a secondary vertical involves a raise above $10M or a notable industry investor (e.g., Enlightened Hospitality Investments, Levy Family Partners), treat it as primary significance in the output.

---

## Phase 5: Consolidate & Output by Insight

Organize findings as insights, not vertical reports. Each insight expresses a specific, distinct observation — a pattern, a move, a signal — not just "Company X raised money."

### Output format

```
**[Insight headline — the so-what in one line]**
[1–2 sentences of supporting evidence with source attribution and date]
Vertical: [tag from reference file] | [Deal-making / Competitor move / Customer win / AI / Other]
Relevance to Toast: [one sentence on why this matters]
Source: [publication] ([date])
```

**Ranking:** Lead with the most materially significant insights for Toast. Deal-making and competitor moves typically rank highest; general market color ranks lowest. Within the same category, lead with primary vertical items.

**Volume target:** 5–8 insights on an active news day. 2–3 on a quiet day. Do not pad with weak signals to hit a number — a short digest of strong signals beats a long list of noise.

**If a day is quiet:** Present what's available and note "Quiet news cycle — [N] signals surfaced."

### Digest footer

```
─────────────────────────────────────────────────────
[N] insights | Lookback: [window] | [date range]
Sources: trade press, daily briefing ([status: used existing / invoked fresh]), broad web + PR wires
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
