# First Week — The Trust Ramp

**Rule of thumb: autonomy is earned, not granted.** Don't turn on all 12 agents with full write access on day one. Here's the recommended 7-day rollout.

## Day 1 — Shadow mode

**Goal:** Watch what the agents *would* do without letting them do anything.

- [ ] Complete [`SETUP.md`](./SETUP.md) through step 4 (Notion databases seeded)
- [ ] Set **all** agents to "draft-only" mode:
  ```bash
  npx paperclipai config set agents.*.mode=draft-only
  ```
- [ ] Turn on only: **Inbox Triage**, **Slack Liaison**, **Task Tracker**, **Chief of Staff**
- [ ] Let them run for 24 hours
- [ ] Tomorrow morning: read the first real morning brief

**What to watch for:**
- Did Inbox Triage mis-categorize anything? (False DECISIONs, missed real ones)
- Did Slack Liaison flag real CLG patterns or noise?
- Does the morning brief read like something you'd actually want at 6am?

## Day 2 — Fix the prompts

**Goal:** Tune the Sudowrite-specific context to your actual company voice.

- [ ] Open [`CUSTOMIZE.md`](./CUSTOMIZE.md)
- [ ] Edit `agents/inbox-triage/AGENTS.md` → update "ABOUT SUDOWRITE" and "AMIT'S EMAIL RULES" to your specifics
- [ ] Do the same for Slack Liaison (voice, channels list) and Chief of Staff (escalation thresholds)
- [ ] Re-import: `npx paperclipai import ./ --overwrite`
- [ ] Run another day in draft-only

## Day 3 — Enable read-only additions

**Goal:** Add agents that don't write anywhere.

- [ ] Enable **Scheduler** in draft-only mode (reads your calendar, drafts replies, doesn't send)
- [ ] Enable **Finance & Data** (read-only by nature — safe to fully enable)
- [ ] Enable **Contractor Ops** in draft-only mode

The morning brief now has 6 sources of input. Look for noise and silence both.

## Day 4 — First write privileges

**Goal:** Grant write access to the lowest-stakes agents.

- [ ] **Task Tracker** → enable auto-actions: "mark clearly completed tasks as done", "label undated tasks"
- [ ] **Inbox Triage** → enable: "file newsletters / automated alerts"
- [ ] Watch the Activity log — any unexpected actions? Roll back immediately if yes.

```bash
npx paperclipai config set agents.task-tracker.mode=auto-actions-limited
npx paperclipai config set agents.inbox-triage.auto-file=true
```

## Day 5 — Vendor + hiring

**Goal:** Turn on the research-and-draft agents (they don't send anything).

- [ ] Enable **Vendor Manager** (drafts only, escalates >$500)
- [ ] Enable **Talent Scout** (screens only, never sends accepts/rejects)
- [ ] Enable **Retreat & Travel** (researches only, never books)

## Day 6 — Supplies + auto-actions

**Goal:** Most-trusted auto-actions on.

- [ ] Enable **Supplies & Errands** with whitelist mode (the items you already auto-reorder manually)
- [ ] Enable **Scheduler** auto-reschedule-within-week (still drafts external decline emails)
- [ ] Enable **Inbox Triage** approve-team-expenses-under-$500

At this point, you should be seeing ~70% auto-action ratio in the morning brief.

## Day 7 — Automation Engineer

**Goal:** Turn on the meta-agent.

- [ ] Enable **Automation Engineer**
- [ ] It will run Sunday 17:00 HST and produce the first weekly proposals
- [ ] Review proposals in Monday's weekly review
- [ ] Approve 0–3 of them (pick the ones with highest confidence + low risk)

## After week 1

From here, the system evolves itself. The weekly review is your checkpoint:
- Every Sunday, read the Automation Engineer's proposals
- Approve, reject, or workshop each
- Track the auto-action / escalation ratio trend — it should climb

**When to stop:** When the ratio plateaus around 85–90% auto-actions. That's healthy — the remaining 10–15% are the decisions that genuinely need you.

**Warning signs:**
- Ratio below 50% after 4 weeks → your escalation thresholds are too tight. Loosen.
- Same DECISION item appearing week after week with the same resolution → add an auto-action rule.
- Any agent error rate > 10% → check the Activity log for the root cause before disabling.

## Hard-stop conditions

Instantly pause (not disable) an agent if:
- It takes an action you explicitly didn't authorize
- It escalates something clearly confidential to the wrong channel
- It hallucinates facts in a drafted reply
- Error rate spikes above 20% in 24 hours

```bash
npx paperclipai agents pause <slug>
```

Resume only after inspecting the Activity log and updating the prompt or the rule that failed.
