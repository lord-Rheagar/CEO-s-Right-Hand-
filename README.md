# CEO's Right Hand ✋

> An autonomous ops office for Sudowrite. Twelve agents that filter the CEO's inbox and Slack into 5 to 7 daily decisions, brief every Modal, Anthropic, and Heroku renewal before it lands, screen every applicant against a rubric, and quietly automate everything that repeats.
>
> Built on [Paperclip AI](https://paperclip.ai). MIT licensed. Forks cleanly for any small bootstrapped founder-led company.

<!-- SCREENSHOT: hero image of the org chart or sample morning brief -->



## About this build

Sudowrite is a profitable, bootstrapped AI writing company. 9 full-time staff, 10 contractors, 30,000+ paying writers, headquartered in Honolulu, working in HST. Amit (CEO, co-founder) carries operations, HR, vendor negotiations, growth, and marketing on top of the product and strategy work he wants to spend his time on. The load shows.

This package is the executable answer: a system that does the operational work end-to-end and leaves Amit with the 5 to 7 decisions per day that genuinely need a human.

One Chief of Staff agent at the top, eleven specialists below. They triage Gmail, watch `#muse-model`, `#general`, `#ops`, manage the HST calendar, sync the "Amit's Task List" Notion database, brief vendor renewals (Modal, Anthropic, Heroku), screen Senior ML applicants, track Stripe MRR and burn, and produce the morning brief at 6am HST.

You stay in the loop. The agents do the work.



## Amit's day, before and after

| Today | After this is running |
|---|---|
| 9am: 47 unread emails. You skim 20, miss 3 that matter. | 6am: a 5-to-7 item brief in your Slack DM. You decide each one in under a minute. |
| Modal renewal email arrives Friday. You burn 90 minutes pulling alternatives, drafting a counter. | Modal renewal email arrives Friday. By the time you see it, the Vendor Manager has a BATNA brief in Notion, a drafted counter, and an approval request waiting. You click Approve in 30 seconds. |
| 22 Senior ML applications stack up. You read 5, queue the rest. | 22 applications already scored 0 to 10 against a rubric. Top 3 have interview emails drafted in your voice. |
| Sunday 11pm: still triaging Slack from Friday. | The Slack Liaison flags the one 1:14am DM from James that signals real tension. The other 200 messages are filed, drafted, or summarized in the brief. |

The Automation Engineer watches the Activity log every Sunday and proposes new agents or routines to absorb whatever else keeps showing up.



## How it runs

Each agent is a markdown system prompt + a connector list + a cron schedule, executed by [Paperclip AI](https://paperclip.ai), an open-source agent orchestration platform that runs locally on Amit's machine. Think of Paperclip as Linear for AI agents: you create issues, agents pick them up, draft the work, raise approvals when stakes are high, and ship once you sign off.

This package ships configured for Claude (`claude_local`). The Codex, Gemini, and OpenCode adapters work too if Sudowrite's team prefers a different runtime per agent. Swap any agent's runtime in `.paperclip.yaml`.



## The 12 agents

Every agent uses the same four tags so the Chief of Staff can synthesize the brief by simple aggregation:

| Tag | Meaning | Who acts |
|---|---|---|
| `DECISION` | Needs your judgment | You |
| `HEADS UP` | FYI with potential next action | You see it, you may act |
| `ACTION` | Agent will handle unless you stop it | Agent (draft-then-approve) |
| `FYI` | Logged only | Nobody |

The org chart:

```
Amit (sets direction, makes DECISIONs)
│
└── Chief of Staff               (root orchestrator, daily brief synthesizer)
    │
    ├── Comms
    │   ├── Inbox Triage         (Gmail)
    │   └── Slack Liaison        (Slack + CLG pattern detection)
    │
    ├── Task & Calendar
    │   ├── Scheduler            (Google Calendar)
    │   └── Task Tracker         (Notion)
    │
    ├── People
    │   ├── Talent Scout         (Gmail + Notion + Brave)
    │   └── Contractor Ops       (Notion + Gmail)
    │
    ├── Money
    │   ├── Finance & Data       (Stripe + Ramp read-only + Notion)
    │   └── Vendor Manager       (Notion + Brave + Filesystem)
    │
    ├── World
    │   ├── Retreat & Travel     (Brave + Gmail + Calendar)
    │   └── Supplies & Errands   (Notion + Brave + Gmail)
    │
    └── Meta
        └── Automation Engineer  (meta-agent: builds new agents and routines)
```

<!-- SCREENSHOT: paperclip org chart UI showing all 12 agents under chief-of-staff -->

### How each group earns its keep at Sudowrite

- **Comms.** An email arrives. Inbox Triage tags it `DECISION`, `HEADS UP`, `ACTION`, or `FYI`. Drafts a reply in Amit's voice. Files newsletters automatically. Pre-approves team expenses under $500 once you reach the Month 3 trust ramp. The Slack Liaison watches `#muse-model`, `#general`, `#ops`, plus DMs from James and the team. Flags CLG patterns the team agreed to surface (silent disagreement, late-night emotional messages, dropped commitments) and drafts responses in your voice.
- **Task & Calendar.** The Scheduler protects deep work blocks (8 to 11 HST), reschedules within the same week silently, and refuses to book new meetings after 16:00 HST. The Task Tracker syncs the "Amit's Task List" Notion database, closes done items, labels undated ones, and flags overdue tasks Amit owns that carry real consequences.
- **People.** Talent Scout screens Senior ML and product applicants on a rubric (shipped work, taste, initiative, fit) and gives a +3 bonus to anyone who spots an error in the JD. Contractor Ops tracks the 10-contractor roster: SOWs, invoice reminders, contracts expiring in 30 days.
- **Money.** Finance & Data runs read-only on Stripe and Ramp. Daily Pro-tier MRR check, 2σ anomaly detection on any spend category (Anthropic API, Modal infra, Notion, Heroku), runway alerts the moment you drop below 18 months. Vendor Manager produces a **BATNA brief** for every renewal (best alternative to a negotiated agreement: side-by-side options + recommendation), auto-renews under $200/mo, escalates anything over $500/mo or any double-digit price hike.
- **World.** Retreat & Travel researches venues, flights, hotels for the Sudowrite team retreats. Produces 3-option BATNA tables. Drafts confirmations. Books only after Amit's approval. Supplies & Errands auto-reorders from a whitelist with $200/order and $500/month caps.
- **Meta.** The Automation Engineer reads the Activity log every Sunday. It finds repetitive work that crossed the same desk three or more times in seven days, then proposes a new routine, a new auto-action, or a new sub-agent to absorb it. Automating new work is the system's most important job. Every other agent handles tasks; the Automation Engineer builds the systems that handle new tasks.



## What the morning brief actually looks like

Every weekday at 6:00 HST, this lands in Amit's Slack DM and his email:

```
━━━━━━━━━━━━━━━━━━━━━━━━━
📋 MORNING BRIEF  |  Mon, Apr 27, 6:00am HST
━━━━━━━━━━━━━━━━━━━━━━━━━

1. 🔴 [DECISION] Anthropic API bill jumped 34% week-over-week  (finance-data)
   New total: $14,200. Muse inference volume is flat. Cost-per-call rose
   after the Claude 4.7 bump. Decision: eat the cost, raise Pro-tier price,
   or throttle free-tier inference?
   [Eat it, revisit in 2 wks]  [Raise Pro to $25]  [Throttle free tier]

2. 🔴 [DECISION] Modal contract renewal: 22% price increase  (vendor-manager)
   Current: $8K/mo. Their ask: $9.8K/mo, 12-month lock-in.
   BATNA: RunPod + Together AI split at $7.2K/mo, 3-wk migration.
   Draft counter (9K/mo, month-to-month) ready.
   [Send counter]  [Accept as-is]  [Start RunPod migration]

3. 🟡 [HEADS UP] James sent a long DM at 1:14am HST  (slack-liaison)
   Topic: Muse fine-tune timeline. Tone suggests frustration with QA.
   Flagged under CLG (late-night + emotional language).
   Drafted a "let's talk at standup" reply.
   [Send draft]  [Call James]  [Read full message]

4. 🟢 [ACTION] Penguin Random House follow-up: draft ready  (inbox-triage)
5. 🟢 [ACTION] 3 top applicants for Senior ML: emails drafted  (talent-scout)
6. 🔵 [FYI] MRR up 2.1% last week, baseline normal  (finance-data)
7. 🔵 [FYI] Coffee reorder placed ($42, auto-approved)  (supplies-errands)

─────────────────────────────────
Everything else (23 items): handled, deferred, or filed.
Next brief: Tomorrow 6:00am
━━━━━━━━━━━━━━━━━━━━━━━━━
```

Five to seven items, two to three buttons each. Ten minutes and ops is done for the day.

Full sample (and the EOD digest, weekly review): [`fixtures/`](./fixtures/).



## Getting started

### Prereqs

- Node.js 20+
- One CLI runtime: Claude Code, Codex CLI, Gemini CLI, or OpenCode
- API key for whichever runtime you picked
- Connector credentials for the workspaces Sudowrite already uses

### Step 1. Install and open Paperclip

```bash
npx paperclipai onboard
```

Once setup completes, open `http://localhost:3100` in your browser.

<!-- SCREENSHOT: paperclip onboarding screen -->

### Step 2. Import this package

In the Paperclip dashboard:

1. Click **Import company** in the top-right corner
2. Pick **Local folder**
3. Select the `ceo-s-office/` directory
4. Review the preview (12 agents)
5. Click **Import**

The CEO's Office company appears in the sidebar.

<!-- SCREENSHOT: import company dialog -->

### Step 3. Add your runtime API key

Open **Settings → Secrets** and add the right key for your chosen runtime:

| If your runtime is | Add this secret |
|---|---|
| Claude Code | `ANTHROPIC_API_KEY` |
| Codex CLI | `OPENAI_API_KEY` |
| Gemini CLI | `GEMINI_API_KEY` |
| OpenCode | provider key per OpenCode config |

The agents now have a brain.

<!-- SCREENSHOT: secrets settings page -->

### Step 4. Connect Sudowrite's existing stack

The agents need access to the workspaces Sudowrite already runs in:

| Workspace | What the agents see |
|---|---|
| **Gmail** | Amit's inbox (Inbox Triage, Scheduler, Talent Scout, Contractor Ops, Retreat & Travel) |
| **Google Calendar** | The HST calendar (Scheduler, Retreat & Travel) |
| **Slack** | `#muse-model`, `#general`, `#ops`, plus DMs (Slack Liaison) |
| **Notion** | Amit's Task List, Vendor Tracker, Contractor Roster, Hiring Pipeline (Task Tracker, Talent Scout, Contractor Ops, Vendor Manager, Finance & Data, Supplies & Errands) |
| **Brave Search** | Vendor + travel + applicant research (Vendor Manager, Talent Scout, Retreat & Travel, Supplies & Errands) |
| **Stripe** | MRR, customers, charges (Finance & Data, read-only key) |
| **Ramp** | Card transactions (Finance & Data, optional, read-only) |

Open **Settings → Connectors** and follow the OAuth or token flow for each one. Start with **Gmail + Slack + Notion**. That trio covers about 70% of the morning brief on day one. Add the rest as you go.

<!-- SCREENSHOT: connectors settings page -->

> **Notion gotcha:** After connecting Notion, open each database the agents will read (Amit's Task List, Vendor Tracker, Contractor Roster) and add the integration via "..." → **Add connections**. Skip this step and the integration token stays valid while every database query comes back empty.

Detailed credential walkthroughs sit in [`CONNECTORS.md`](./CONNECTORS.md).

### Step 5. Approve the agents

Imported agents land in `pending_approval` state. This is a deliberate guardrail. Open the **Agents** page, click each agent card, and hit **Approve hire**. The agent moves to `idle` and starts running on its scheduled triggers.

<!-- SCREENSHOT: agent card with Approve hire button -->

You're live. The first morning brief lands tomorrow at 6am HST.

> **What it costs:** expect $50 to $150/day in API costs at typical Sudowrite usage across all 12 agents. Per-agent caps in `.paperclip.yaml` enforce hard ceilings, so a runaway agent stops itself before it hurts.



## How you actually use it

Paperclip works through **Issues** and **Approvals**. The interface uses tickets, dashboards, and approval queues.

> **What agents will do, and what they leave to Amit.** Agents draft, brief, score, file, and surface. Payments, contracts, hires, and outbound messages always pass through an approval before they ship. Every action that touches the outside world raises an approval first.

### The basic loop

```
You create an issue
    ↓
The right agent picks it up (based on assignee or routing)
    ↓
The agent drafts the answer (email reply, candidate score, vendor brief)
    ↓
If the action is high-stakes, the agent raises an approval request
    ↓
You approve, reject, or send back for revision
    ↓
The agent ships the work
```

### Creating an issue

1. Click **+ New issue** in the top-right of the dashboard
2. Pick an assignee (e.g. `vendor-manager` for the Modal renewal, `chief-of-staff` for routing)
3. Write a title and a body
4. Click **Create**

The agent picks it up within seconds. You see its activity stream live: tool calls, reasoning steps, draft output. Leave a comment mid-stream to redirect.

<!-- SCREENSHOT: new issue dialog -->

### Approvals

Anything that touches the outside world raises an approval. Examples:

- Sending an email reply that Inbox Triage drafted
- Auto-renewing a vendor over $200/mo
- Posting in `#muse-model` on your behalf
- Reordering supplies past the whitelist

Pending approvals show in the dashboard sidebar. Click any one to see the full draft, then **Approve**, **Reject**, or **Request revision**. The agent revises and resubmits as many times as needed.

<!-- SCREENSHOT: approvals dashboard with pending items -->

### Routines (the daily brief and friends)

Most useful work runs automatically through cron-triggered **routines**. The package ships preconfigured:

| Routine | When | Owner |
|---|---|---|
| Morning brief | Mon to Fri 6:00 HST | Chief of Staff |
| EOD digest | Mon to Fri 17:30 HST | Chief of Staff |
| Weekly review | Sun 18:00 HST | Chief of Staff |
| Inbox sweep | Every 15 min, 8:00 to 18:00 HST | Inbox Triage |
| Slack heartbeat | Every 10 min, 8:00 to 18:00 HST | Slack Liaison |
| Talent scan | Every 2 hrs during work day | Talent Scout |
| Vendor weekly | Mon 9:00 HST | Vendor Manager |
| Contractor daily | Mon to Fri 9:00 HST | Contractor Ops |
| Finance daily | Mon to Fri 8:00 HST | Finance & Data |
| Finance weekly | Mon 8:00 HST | Finance & Data |
| Supplies weekly | Sun 17:00 HST | Supplies & Errands |
| Automation weekly | Sun 17:00 HST | Automation Engineer |

A sample brief sits in [`fixtures/sample-morning-brief.md`](./fixtures/sample-morning-brief.md).



## The trust ramp

Autonomy is earned over time. The system mirrors how a human Chief of Staff would ramp into the role at Sudowrite:

| Phase | What happens |
|---|---|
| **Month 1: Shadow** | All agents in draft-only mode. They produce briefs, score candidates, draft replies. Every output sits as a draft for Amit's review. Tune the prompts to your voice, your thresholds, and Sudowrite's actual channels and people. |
| **Month 3: Recurring auto-actions** | The agents take over their highest-confidence repeat work: filing newsletters, closing done tasks, approving team expenses under $500, auto-renewing vendors under $200/mo. The Automation Engineer's first weekly proposals start landing. |
| **Month 6: Independent ownership** | Agents own their full lanes. The morning brief becomes Amit's primary surface area for ops decisions. The Automation Engineer is the loop that keeps the system improving. The time you got back goes to Muse, Sudowrite product, and strategy. |

Full day-by-day plan for week one: [`FIRST_WEEK.md`](./FIRST_WEEK.md).



