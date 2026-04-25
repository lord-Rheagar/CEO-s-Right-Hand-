# Connectors

Each agent needs access to specific external systems. This file lists what's needed, where to get credentials, and what scopes matter.

## Minimum viable setup

If you want to start with the smallest possible wiring:

| Secret | Used by | Why minimal |
|---|---|---|
| `ANTHROPIC_API_KEY` | All 12 agents | Required for any agent to think |
| `GOOGLE_OAUTH_TOKEN` | Inbox Triage, Scheduler | Unlocks the two highest-value agents |
| `SLACK_BOT_TOKEN` | Slack Liaison | Closes the comms triage loop |

This gives you a working morning brief on day one. Add the rest as you expand.

## Full connector table

| Agent | MCP Connector | Scopes needed | Where to get it |
|---|---|---|---|
| Inbox Triage | Gmail | `gmail.readonly`, `gmail.modify`, `gmail.compose` | [Google Cloud Console](https://console.cloud.google.com) → OAuth 2.0 Client IDs |
| Slack Liaison | Slack | `channels:history`, `groups:history`, `im:history`, `chat:write`, `users:read` | [api.slack.com/apps](https://api.slack.com/apps) → create app → install to workspace |
| Scheduler | Google Calendar | `calendar.readonly`, `calendar.events` | Same OAuth client as Gmail |
| Task Tracker | Notion | Read + write on a specific Notion integration | [notion.so/my-integrations](https://www.notion.so/my-integrations) |
| Talent Scout | Gmail + Notion + Brave Search | Gmail read, Notion write, Brave search | See above + [brave.com/search/api](https://brave.com/search/api/) |
| Contractor Ops | Notion + Gmail | Notion read/write, Gmail read/compose | Reuse Notion + Google clients |
| Finance & Data | Stripe + Ramp + Notion | **Read-only only** — `read_only` restricted key | [dashboard.stripe.com/apikeys](https://dashboard.stripe.com/apikeys) + Ramp API settings |
| Vendor Manager | Notion + Brave + Filesystem | Notion read/write, Brave search | Reuse keys |
| Retreat & Travel | Brave + Gmail + Calendar | Read + compose | Reuse keys |
| Supplies & Errands | Notion + Gmail + Brave | Read/write | Reuse keys |
| Automation Engineer | Filesystem only | Paperclip Activity log + agent file reads | No external credentials |

## Google OAuth — step by step

The single most important credential. Gmail + Calendar share the same OAuth client.

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or use existing)
3. Enable APIs: **Gmail API**, **Google Calendar API**
4. Credentials → Create OAuth 2.0 Client ID → "Desktop app"
5. Download `credentials.json`
6. Run `npx paperclipai mcp auth google` — follows the OAuth flow and stores the token
7. First time: a browser opens, you grant scopes, token is stored in Paperclip's vault

**Scopes to grant:**
- `https://www.googleapis.com/auth/gmail.readonly`
- `https://www.googleapis.com/auth/gmail.modify`
- `https://www.googleapis.com/auth/gmail.compose`
- `https://www.googleapis.com/auth/calendar.readonly`
- `https://www.googleapis.com/auth/calendar.events`

## Slack — step by step

1. Go to [api.slack.com/apps](https://api.slack.com/apps) → "Create New App" → "From scratch"
2. Name: `CEO's Office Agent` (or whatever), pick your workspace
3. OAuth & Permissions → Bot Token Scopes → add:
   - `channels:history`, `channels:read`
   - `groups:history`, `groups:read`
   - `im:history`, `im:read`, `im:write`
   - `chat:write`, `chat:write.public`
   - `users:read`
4. Install to Workspace → copy the `xoxb-...` Bot User OAuth Token
5. `npx paperclipai secrets set SLACK_BOT_TOKEN=xoxb-...`

**Invite the bot to channels it should monitor:** `/invite @ceo-s-office-agent` in each channel.

## Notion — step by step

1. Go to [notion.so/my-integrations](https://www.notion.so/my-integrations)
2. "+ New integration" → name it, pick your workspace, select "Internal"
3. Capabilities: Read content, Update content, Insert content
4. Copy the `secret_...` internal integration token
5. `npx paperclipai secrets set NOTION_TOKEN=secret_...`
6. **In Notion:** open each database the agents will read (Task List, Vendor Tracker, etc.) → click "..." → "Add connections" → select your integration

Without step 6, the agents will see an empty Notion even with a valid token.

## Stripe — step by step (read-only only)

1. Go to [dashboard.stripe.com/apikeys](https://dashboard.stripe.com/apikeys)
2. "Create restricted key" (not a live secret key)
3. Name: `ceo-s-office-read-only`
4. Grant **Read** permissions to: Customers, Subscriptions, Charges, Balance, Invoices
5. Grant **None** to: everything else (this agent has no write access by design)
6. `npx paperclipai secrets set STRIPE_API_KEY=rk_live_...`

The `.paperclip.yaml` sidecar also enforces this via `permissions.denied: [stripe:write]` as a belt-and-suspenders.

## Brave Search — optional

For Vendor Manager, Retreat & Travel, and Talent Scout to do web research.

1. [brave.com/search/api](https://brave.com/search/api/) → sign up
2. Free tier: 2,000 queries/month (plenty for this use case)
3. `npx paperclipai secrets set BRAVE_API_KEY=BSA-...`

## Security notes

- Paperclip's secret vault is local. Secrets never export. When you share this package, the vault is excluded.
- The `.paperclip.yaml` sidecar declares which env vars each agent needs — it's documentation, not a secret file.
- For Finance & Data specifically: the Stripe restricted key is scope-limited at the API level AND the Paperclip permission layer. Two independent guards.
- Consider rotating tokens quarterly.

## Debugging

```bash
# List registered MCP connectors
npx paperclipai mcp list

# Test a specific connector
npx paperclipai mcp test gmail

# View recent agent runs
npx paperclipai logs --agent inbox-triage --limit 20
```
