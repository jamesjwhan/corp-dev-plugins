# corp-dev-plugins

Toast CorpDev plugin suite for Claude Code — M&A research, deal memos, financial diligence, CRM pipeline, and deal execution workflows.

## Plugins

| Plugin | Description |
|---|---|
| [`corp-dev-analyst`](corpdev-analyst-plugin/) | Senior corp dev analyst: deep-dive research, sector briefs, deal memos, earnings analysis, financial diligence, cap table modeling, data room review, daily briefings |
| [`corpdev-crm`](corpdev-crm-plugin/) | CRM pipeline orchestration: signal monitoring, deal sourcing, company enrichment, startup meeting management, Notion CRM writes |
| [`corpdev-execution`](corpdev-execution-plugin/) | Deal execution and integration: confirmatory diligence planning and coordination, sign-to-close planning, deal comms runbook, post-close integration planning and OKR tracking |

## Installation

### Step 1 — Add the marketplace (one-time per machine)

```bash
/plugin marketplace add jamesjwhan/corp-dev-plugins
```

> **Important:** Use the `owner/repo` shorthand — the full `https://github.com/jamesjwhan/corp-dev-plugins` URL does **not** work (Claude Code treats bare HTTPS GitHub URLs as direct links to a `marketplace.json` file, not git clones).

### Step 2 — Install the plugins you want

```bash
/plugin install corp-dev-analyst@corp-dev-plugins
/plugin install corpdev-crm@corp-dev-plugins
/plugin install corpdev-execution@corp-dev-plugins
```

### Step 3 — Reload

```bash
/reload-plugins
```

### Team / project scope

To install for everyone on a project (written to `.claude/settings.json`):

```bash
/plugin install corp-dev-analyst@corp-dev-plugins --scope project
/plugin install corpdev-crm@corp-dev-plugins --scope project
/plugin install corpdev-execution@corp-dev-plugins --scope project
```

### Keeping up to date

```bash
/plugin marketplace update corp-dev-plugins
```

## About the "Code" tab

When you open `/plugin` and view an installed plugin, you may see a **Code** tab (or a Code section in the Discover detail pane). This is a standard Claude Code UI element — it shows plugin **skills** that are invocable as slash commands from the Code tab (e.g. `/corp-dev-analyst:company-deep-dive`). It is not an error and requires no action.
