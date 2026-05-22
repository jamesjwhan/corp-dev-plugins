# corp-dev-analyst

Corporate development analyst plugin for Claude Code. Provides the `corp-dev-analyst` agent and a full suite of research and diligence skills for M&A sourcing, deal evaluation, and strategic intelligence.

## Components

### Agent
- **corp-dev-analyst** — Senior corp dev analyst agent. Proactively surfaces acquisition targets, evaluates opportunities, orchestrates downstream research skills, and delivers executive-ready recommendations. Triggers reactively (user asks for analysis) and proactively (new signals from CRM or briefings).

### Skills

| Skill | Triggers on |
|---|---|
| `company-deep-dive` | "research [company]", "deep dive on [company]", "profile for [company]", "prep me for a call with [company]" |
| `sector-research` | "market overview of [vertical]", "landscape for [space]", "sector brief on [topic]", "who are the players in [market]" |
| `deal-memo-writer` | "write a deal memo", "IC memo for [company]", "write this up", "1-pager for IC", "put something together on [company]" |
| `cap-table-analyst` | "cap table analysis", "waterfall", "dilution scenarios", "liquidation preference analysis" |
| `data-room-analyst` | "review the data room", "analyze these diligence files", "what's in the data room" |
| `earnings-call-analyst` | "analyze earnings call", "earnings transcript for [company]", "what did [company] say on their call" |
| `financial-diligence` | "financial diligence on [company]", "review the financials", "unit economics", "model the scenarios" |
| `daily-briefing` | "run the daily briefing", "morning summary", "what's in my newsletters", "pull newsletter updates" |

## Installation

**Step 1 — Add the marketplace** (one-time per machine):

```bash
/plugin marketplace add jamesjwhan/corp-dev-plugins
```

> Use the `owner/repo` shorthand — not the full `https://github.com/...` URL.

**Step 2 — Install this plugin:**

```bash
/plugin install corp-dev-analyst@corp-dev-plugins
```

**Step 3 — Reload plugins in the current session:**

```bash
/reload-plugins
```

### Team / project scope

To share across your whole repo (adds to `.claude/settings.json`):

```bash
/plugin install corp-dev-analyst@corp-dev-plugins --scope project
```

## Prerequisites

Some skills use optional MCP connectors for richer data:
- **PitchBook** (`mcp__pitchbook__*`) — funding history, valuations, investor rosters
- **Google Drive** — data room file access, doc creation
- **Gmail** — daily briefing newsletter retrieval

Skills degrade gracefully when connectors are unavailable, noting data gaps with `[to confirm via PitchBook]` markers.
