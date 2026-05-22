# corpdev-crm

CorpDev CRM workflow plugin for Claude Code. Provides the `corpdev-crm-auto-updater` agent and a full suite of CRM skills for pipeline management, signal monitoring, deal sourcing, and Notion writes.

## Components

### Agent
- **corpdev-crm-auto-updater** — Full CRM pipeline orchestrator. Sequences all four phases (startup meetings → signal monitor → sourcing → Notion writes) and manages handoffs between them. Triggers on "update the CRM", "run the CRM update", "sync the CRM", "it's Friday, update the pipeline".

### Skills

| Skill | Triggers on |
|---|---|
| `startup-meeting-manager` | "log this meeting", "process my meeting with [company]", "meeting recap for [company]", "add meeting notes" |
| `crm-signal-monitor` | "run signal monitor", "scan for new signals", "check for CRM updates", "what's new with our pipeline" |
| `crm-sourcing` | "find new companies", "source new targets", "who should we be talking to in [vertical]", "run sourcing" |
| `crm-add-enrich` | "add [company]", "log [company] to the CRM", "enrich [company]", "new company [name]" |
| `crm-migration-toast` | CRM migration workflows for Toast-specific pipeline setup |
| `crm-setup-toast` | Initial CRM setup and configuration for Toast's CorpDev Notion workspace |

## Orchestration Flow

```
corpdev-crm-auto-updater
  │
  ├─ Phase 1: startup-meeting-manager   (process recent meetings)
  ├─ Phase 2: crm-signal-monitor        (scan for net-new signals)
  ├─ Phase 3: crm-sourcing              (discover new pipeline companies)
  └─ Phase 4: crm-add-enrich            (write all approved updates to Notion)
```

Nothing is written to Notion without explicit approval at each phase.

## Installation

```bash
cc --plugin-dir /path/to/corpdev-crm-plugin
```

Or copy to your project's `.claude-plugin/` directory for project-scoped use.

## Prerequisites

- **Notion MCP** — required for all CRM reads and writes
- **Google Calendar** — startup-meeting-manager meeting detection
- **Google Drive** — meeting transcript and doc access
- **Slack MCP** — signal monitoring across DMs and channels
- **Gmail MCP** — deal thread scanning and daily briefing
- **PitchBook** (`mcp__pitchbook__*`) — funding data for signal enrichment
