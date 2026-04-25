# Roadmap

Phase 2 additions and directions the CEO's Office can grow.

## Phase 2 agents (not yet written)

These are documented here for the Automation Engineer to consider spinning up, or for you to draft yourself:

### Investor & Board Coordinator
- Scope: investor email threads, board update drafting, data-room management
- Connectors: Gmail, Notion, Filesystem
- Escalation: all investor comms stay drafted-only forever
- Why not Phase 1: Sudowrite specifically has no VC board. Most small profitable cos don't need this.

### Legal Liaison
- Scope: contract redlining, NDA triage, terms-of-service updates
- Connectors: Gmail, Filesystem, possibly DocuSign/Ironclad
- Hard rule: never executes a contract; every legal item is DECISION-tagged
- Why not Phase 1: legal is high-stakes; needs a heavier review cycle to trust with drafts

### Customer Escalation Triage
- Scope: paid-customer complaints that reach the CEO's inbox
- Connectors: Gmail + customer DB (Zendesk/Intercom/Stripe)
- Output: root-cause analysis + draft reply + loop-in to CS lead
- Why not Phase 1: requires customer DB integration and tier-aware logic

### Personal Life Coordinator
- Scope: medical appointments, home maintenance, personal trips, gift-giving
- Connectors: Gmail + Calendar + Notion
- Hard rule: separate vault; personal data never crosses into company context
- Why not Phase 1: personal/professional boundary deserves careful design

### Social / Brand Monitor
- Scope: company mentions on Twitter/X, HN, Reddit, relevant podcasts
- Connectors: Twitter API + Brave + RSS
- Output: sentiment shifts, viral mentions, press opportunities
- Why not Phase 1: too easy to drown in noise without heavy filtering

### Fundraise Ops (if/when the company raises)
- Scope: investor pipeline, data room, update cadence, term-sheet negotiation
- Connectors: Notion + Gmail + DocSend/Dropbox
- Activates only when company enters "fundraising mode"

## Guardrails to add over time

### Observability
- **Dashboard** showing agent health, token spend, escalation ratio, error rate
- **Alerts** on budget breach, unusual activity patterns, anomalous decisions
- **Audit log export** to S3/GCS for long-term storage

### Safety
- **Red-team exercises:** prompt the agents with adversarial messages and confirm they don't leak data, impersonate, or auto-action inappropriately
- **Prompt-injection hardening:** every inbound email/Slack message is treated as untrusted input
- **Runaway prevention:** hard circuit breakers per agent (max actions per hour, max cost per day — already in `.paperclip.yaml`)

### Quality
- **Human eval loop:** every Sunday, sample 5 random agent actions from the week and rate them 1–5. Feed scores back into prompt tuning.
- **A/B prompt testing:** fork an agent, run two prompts in parallel for a week, measure escalation rate + Amit-override rate
- **Drift detection:** compare this month's decisions to last month's — are the patterns changing?

### Multi-principal support
- Right now this is built around one CEO. Phase 3: support a small leadership team where some agents (Finance, Vendor Manager) serve multiple principals with routing rules.

## Observability ideas

| Metric | Why it matters |
|---|---|
| Auto-action / escalation ratio | North star for "is the system getting better?" |
| Amit override rate | How often does Amit override an agent decision? Target <5%. |
| Median time-to-brief | From cron trigger to delivered brief. Target <90 seconds. |
| Tokens per morning brief | Trending up means prompts are getting verbose. Check every 2 weeks. |
| Silent auto-actions per day | Too low → system isn't doing its job. Too high → Amit has lost visibility. |
| CLG flag false-positive rate | How often does Slack Liaison flag a pattern that wasn't real conflict? |

## Community direction

If this starter kit gets traction beyond Sudowrite:
- Separate the Sudowrite-specific context into a `config/company-profile.yaml` so the prompts are generic by default
- Package variants: "Startup COS" / "Indie operator" / "Ops team of one"
- Skill library (matching `skills/` convention in the Paperclip spec) for common tasks: BATNA table, CLG flagging, rubric scoring

## Non-goals

What this will never be:
- **Autonomous agents that move money.** Finance & Data is and stays read-only. Money is always Amit's click.
- **Replace the human Chief of Staff entirely.** These agents augment. A real COS still makes judgment calls the agents can't.
- **Replace existing product tools.** Notion is still Notion. Slack is still Slack. Gmail is still Gmail. The agents are a thin layer on top.
