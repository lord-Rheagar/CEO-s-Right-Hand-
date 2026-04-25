# Setup

Detailed install, secrets, and connector wiring for the CEO's Office.

## Prerequisites

- **Node.js 20+** ([nodejs.org](https://nodejs.org))
- **Claude Code** ([claude.com/download](https://claude.com/download)) — required for the `claude_local` adapter
- **Anthropic API key** with access to `claude-opus-4-7`
- **Accounts & API tokens** for the services you'll connect (see [`CONNECTORS.md`](./CONNECTORS.md))

## 1. Install Paperclip

```bash
npx paperclipai onboard --yes
```

This installs Paperclip locally with an embedded PGlite database. No Docker, no external DB needed for dev.

## 2. Clone + import this package

```bash
git clone https://github.com/bodhiswattwac/ceo-s-office.git
cd ceo-s-office
npx paperclipai import ./
```

Paperclip will:
- Read `COMPANY.md` as the root manifest
- Walk `agents/`, `teams/`, `tasks/` by convention
- Apply `.paperclip.yaml` sidecar (adapters, cron triggers, budgets)
- Create all 12 agents with correct `reportsTo` hierarchy
- Schedule all recurring tasks

Confirm in the UI at `http://localhost:3000` — you should see the full org chart.

## 3. Wire your secrets

All secrets are stored in Paperclip's local vault (never exported, never sent anywhere except to the specific MCP connector that needs them).

### Required
```bash
npx paperclipai secrets set ANTHROPIC_API_KEY=sk-ant-...
npx paperclipai secrets set GOOGLE_OAUTH_TOKEN=...           # Gmail + Calendar
npx paperclipai secrets set SLACK_BOT_TOKEN=xoxb-...
npx paperclipai secrets set NOTION_TOKEN=secret_...
```

### Optional (for full functionality)
```bash
npx paperclipai secrets set BRAVE_API_KEY=BSA-...            # Web search for Vendor Manager, Retreat & Travel, Talent Scout
npx paperclipai secrets set STRIPE_API_KEY=sk_live_...       # Finance & Data (read-only scope only)
npx paperclipai secrets set RAMP_API_KEY=...                 # Finance & Data
```

### Minimum viable set

If you want to start with the smallest possible wiring to see it work:
- `ANTHROPIC_API_KEY` (required for all agents)
- `GOOGLE_OAUTH_TOKEN` (Inbox Triage + Scheduler)
- `SLACK_BOT_TOKEN` (Slack Liaison)

That's enough to see the morning brief work with the top 3 most active agents. Add Notion next, then the rest.

## 4. Seed your Notion databases

Several agents expect specific Notion databases to exist:
- **Amit's Task List** — for Task Tracker
- **Vendor Tracker** — for Vendor Manager
- **Contractor Roster** — for Contractor Ops
- **Hiring Pipeline** — for Talent Scout
- **Weekly Financials** — for Finance & Data
- **Supplies Inventory** — for Supplies & Errands

You can create these manually in Notion with any schema; the agents' first run will tell you what columns they expect. Or we'll add a seeding helper in Phase 2.

## 5. First run

```bash
npx paperclipai run
```

Open `http://localhost:3000`. Your first automatic run will be the next scheduled cron (see [`.paperclip.yaml`](./.paperclip.yaml)). To trigger anything manually, click any agent → "Run now."

**Recommended first manual runs:**
1. **Chief of Staff** → "Run now" → should produce a shell morning brief (empty sections without real data — expected)
2. **Inbox Triage** → "Run now" → should pull your latest unread, categorize, and write a report

See [`FIRST_WEEK.md`](./FIRST_WEEK.md) for the recommended trust-ramp sequence — don't turn on write-access for all agents on day 1.

## 6. Timezone

All cron triggers in `.paperclip.yaml` default to `Pacific/Honolulu` (HST). If you're not in Hawaii, edit `.paperclip.yaml` and change `timezone:` on each routine. Re-import after editing:

```bash
npx paperclipai import ./ --overwrite
```

## Troubleshooting

| Problem | Fix |
|---|---|
| Import fails with schema error | Check YAML frontmatter in all `*.md` files — quotes around values with colons |
| Agent not appearing in UI | Confirm `reportsTo` points to a valid slug that was imported before this agent |
| Cron not firing | Check timezone in `.paperclip.yaml` matches your expectation; confirm Paperclip daemon is running |
| "Anthropic API: 404" | Verify model string is exactly `claude-opus-4-7` (no date suffix) |
| MCP connector not connecting | Run `npx paperclipai mcp list` to see registered connectors; re-run `secrets set` if keys are stale |

## Update

When you pull new prompts or edits:
```bash
cd ceo-s-office
git pull
npx paperclipai import ./ --overwrite
```
