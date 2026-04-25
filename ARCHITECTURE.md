# Architecture

## Org chart

```
Amit (human, sets direction, makes DECISIONs)
│
└── Chief of Staff  ◄── root agent, synthesizes the brief, routes escalations
    │
    ├── Comms
    │   ├── Inbox Triage        — Gmail
    │   └── Slack Liaison       — Slack (+ CLG pattern detection)
    │
    ├── Task & Calendar
    │   ├── Scheduler           — Google Calendar
    │   └── Task Tracker        — Notion
    │
    ├── People
    │   ├── Talent Scout        — Gmail + Notion + Brave
    │   └── Contractor Ops      — Notion + Gmail
    │
    ├── Money
    │   ├── Finance & Data      — Stripe/Ramp (read-only) + Notion
    │   └── Vendor Manager      — Notion + Brave + filesystem
    │
    ├── World
    │   ├── Retreat & Travel    — Brave + Gmail + Calendar
    │   └── Supplies & Errands  — Notion + Brave + Gmail
    │
    └── Meta
        └── Automation Engineer — reads Activity log, proposes new agents/routines
```

All sub-agents have `reportsTo: chief-of-staff`. Chief of Staff has `reportsTo: null` — Amit is the human board.

## Output contract (universal)

Every sub-agent reports findings to the Chief of Staff using **one of four tags** on every item:

| Tag | Meaning | Who acts |
|---|---|---|
| `DECISION` | Requires Amit's judgment | Amit |
| `HEADS UP` | FYI with potential next action | Amit sees, may not act |
| `ACTION` | Agent will handle unless Amit stops it | Agent (draft-then-approve) |
| `FYI` | Logged only, no action needed | No one |

This contract means the Chief of Staff never has to translate between agent outputs — synthesis is pure aggregation + dedup + ordering.

## Data flow — morning brief

```
06:00 HST Mon–Fri:

  Cron trigger → Chief of Staff wakes up
                      │
                      ├── pulls latest Inbox Report  (inbox-triage last ran 05:45)
                      ├── pulls latest Slack Report  (slack-liaison last ran 05:50)
                      ├── pulls latest Task Report   (task-tracker last ran 05:58)
                      ├── pulls latest Calendar Report
                      ├── pulls latest Finance Daily
                      ├── [Monday only] Vendor + Talent + Contractor reports
                      │
                      ↓
             [dedup + rank by urgency + cap at 7 items]
                      │
                      ↓
             [write morning brief in canonical format]
                      │
                      ↓
             [post to Slack DM + email Amit]
```

## Decision rights

The agents have these hard rules built into their prompts:

| Agent | Can do without approval | Always escalates |
|---|---|---|
| Inbox Triage | File newsletters, draft replies, approve <$500 team expenses | Legal, James, lawyer email |
| Slack Liaison | Monitor, draft replies | James DMs, CLG pattern flags |
| Scheduler | Reschedule within same week, create focus blocks | External meetings, > 60min, new person "quick chats" |
| Task Tracker | Close done tasks, label undated, reassign delegatable | Amit-owned overdue with real consequences |
| Vendor Manager | Auto-renew < $200/mo (all conditions met) | > $500/mo, multi-year, price hike > 15%, vendor switch |
| Finance & Data | Read-only (never writes) | Any anomaly ≥ 2σ, > $2K transaction, runway < 18 mo |
| Talent Scout | Score + label | Send accept/reject (never auto), +3 bonus applicants |
| Contractor Ops | Invoice reminders, status updates | Rate hikes > 10%, disputes, disengagement 21+ days |
| Retreat & Travel | Research, draft | Never books, never purchases |
| Supplies & Errands | Auto-reorder from whitelist < $200/mo | Off-whitelist items, new vendors, > $500 |
| Automation Engineer | Draft proposals | Never edits files without approval |

## Recurring schedule

Full cron specs in [`.paperclip.yaml`](./.paperclip.yaml). Summary:

| Routine | When | Agent |
|---|---|---|
| Morning brief | Mon–Fri 06:00 HST | Chief of Staff |
| EOD digest | Mon–Fri 17:30 HST | Chief of Staff |
| Weekly review | Sun 18:00 HST | Chief of Staff |
| Hourly inbox sweep | Every 15min, work hrs | Inbox Triage |
| Slack heartbeat | Every 10min, work hrs | Slack Liaison |
| Talent scan | Every 2hrs, work hrs | Talent Scout |
| Vendor weekly | Mon 09:00 HST | Vendor Manager |
| Contractor daily | Mon–Fri 09:00 HST | Contractor Ops |
| Finance daily | Mon–Fri 08:00 HST | Finance & Data |
| Finance weekly | Mon 08:00 HST | Finance & Data |
| Supplies weekly | Sun 17:00 HST | Supplies & Errands |
| Automation weekly | Sun 17:00 HST | Automation Engineer |

## Why 12 agents?

Six of them could be collapsed into two (e.g., a generic "Ops Agent"). We chose 12 intentionally:

1. **Narrow prompts score better.** Each agent has < 2K tokens of instructions, focused on one domain.
2. **Failure isolation.** If the Scheduler hits a Google Calendar bug, the Vendor Manager still works.
3. **Budget caps per agent** (see `.paperclip.yaml`) prevent a runaway agent from eating the whole daily token budget.
4. **The Automation Engineer can evolve each agent independently.** Over time, each prompt gets tighter and better-tuned to the real patterns Amit's life actually has.

## Ground truth

- **Activity log** (Paperclip built-in) — every agent action recorded. Automation Engineer reads from here weekly.
- **Notion databases** — for structured state (tasks, vendors, contractors, applicants)
- **Gmail / Slack / Calendar** — for inbox + message + calendar state (read-through, not mirrored)
- **Markdown files here** — for agent prompts, task templates, routines (Git is source of truth for agent definitions)
