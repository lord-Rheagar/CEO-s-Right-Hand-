# Customize

How to adapt these prompts for your own company, voice, and rules.

## The "ABOUT" block — every agent prompt starts with one

Each `agents/*/AGENTS.md` begins with something like:

```
ABOUT SUDOWRITE:
AI writing tools for fiction authors. 9 full-time staff, 10 contractors,
30,000+ paying writers, profitable 3+ years, no VC board.
Team practices Conscious Leadership (CLG).
Key people: Amit Gupta (CEO), James (co-founder/CTO).
Products: Sudowrite (writing assistant) + Muse (AI model for fiction).
```

**Replace this block in every agent.** Use the same shape:
- What the company does (1 sentence)
- Size (FT + contractors + customers if relevant)
- Profitability / funding posture (important for cost decisions)
- Culture framework (CLG, Radical Candor, holacracy, etc. — influences Slack Liaison)
- Key people (CEO, co-founders, anyone the agents must treat differently)
- Products

Do this once across all 12 files. Consider writing a `config/company-profile.md` and referencing it with `@include` syntax when Paperclip adds that.

## Voice

Every agent that drafts outbound text (Inbox Triage, Slack Liaison, Scheduler, Talent Scout, Vendor Manager, Contractor Ops, Retreat & Travel) has a "VOICE" block.

For Amit:
```
* Short (3-5 lines max)
* Direct and warm, not corporate
* Amit signs emails with just "Amit"
* No "Hope this finds you well" filler
```

Replace with *your* voice. Give the agents 3–5 concrete rules. "Direct" / "warm" / "quirky" / "formal" — each anchor moves output meaningfully.

**Test your voice definition:** paste it into a fresh chat with Claude and ask it to rewrite a sample email. If it reads like you, the agents will sound like you.

## Rules — tight vs loose

Each agent has an "AUTO-ACTIONS" block (what it can do silently) and an "ALWAYS ESCALATE" block (what always reaches you).

**Start tight.** Move things from "always escalate" to "auto-action" only after you've seen the agent make the right decision 10+ times.

**When to loosen:** Automation Engineer's weekly proposals will surface patterns like "you approved this kind of expense 8 times this week, always the same answer — add an auto-action?" Accept those when the pattern is genuinely deterministic.

## Timezones

All cron schedules default to `Pacific/Honolulu`. Change them in `.paperclip.yaml`:

```yaml
routines:
  morning-brief:
    triggers:
      - kind: schedule
        cronExpression: "0 6 * * 1-5"
        timezone: America/Los_Angeles   # ← edit this per routine
```

Common options:
- `America/Los_Angeles` — PT
- `America/New_York` — ET
- `America/Chicago` — CT
- `Europe/London` — UK
- `Asia/Singapore` — SGT

Also update the HST-specific rules in individual agent prompts (Scheduler's "deep work 8–11 HST" and "hard stop at 16 HST").

## Dollar thresholds

Several agents have $-based rules:
- Vendor Manager auto-renew: under $200/mo
- Vendor Manager escalate: over $500/mo
- Inbox Triage auto-approve: expenses under $500
- Finance & Data anomaly: single transaction > $2,000
- Retreat & Travel escalate: trip > $5,000 solo, > $50,000 retreat
- Supplies & Errands cap: $200/order, $500/month

These are tuned for a small profitable co. Scale up or down based on your company's spend patterns. Don't make them round numbers — use thresholds that are slightly above your existing typical spend so the rules actually catch outliers.

## Channel / database names

Some prompts reference specific Slack channels or Notion databases:
- Slack Liaison: `#muse-model`, `#general`, `#ops`, `#engineering`
- Task Tracker: "Amit's Task List" database
- Vendor Manager: "Vendor Tracker" database
- Contractor Ops: "Contractor Roster"
- Talent Scout: "Hiring Pipeline"
- Finance & Data: "Weekly Financials"
- Supplies & Errands: "Supplies Inventory"

Create matching Notion databases (any schema — agents discover columns) and update channel names in `agents/slack-liaison/AGENTS.md`.

## Hiring rubric

Talent Scout has a specific rubric for Sudowrite (shipped work + taste + initiative + fit, with a +3 bonus for JD-error spotters).

Adapt the rubric to what *you* value in candidates:
- A design-led company might weight portfolio over GitHub
- A research lab might weight publications + novelty over ship cadence
- A devtools co might weight contributions to OSS and developer-facing writing

Keep the 0–10 total, keep the "+3 bonus for noticing X about the company" mechanic — it's a great filter for attention-to-detail.

## CLG / culture flags

Slack Liaison has a "CLG ESCALATION TRIGGERS" block specific to Conscious Leadership Group culture. If you use a different framework:
- **Radical Candor:** flag patterns of ruinous empathy (unspoken disagreement) and obnoxious aggression (harsh without care)
- **Holacracy:** flag tensions that aren't being processed in tactical meetings
- **No framework:** flag silence after conflict, passive-aggressive language, late-night emotional messages

The core mechanic — patterns > individual messages — is framework-agnostic.

## Re-import after editing

```bash
cd ceo-s-office
npx paperclipai import ./ --overwrite
```

Agents pick up new prompts on their next scheduled run.

## Version control

Fork this repo. Commit your customizations:
```bash
git checkout -b my-company
git add .
git commit -m "Adapt to [Your Co] — voice, thresholds, channels"
```

You now have a version-controlled company-specific agent config. The Automation Engineer's proposals land as PRs you can review, approve, and merge.
