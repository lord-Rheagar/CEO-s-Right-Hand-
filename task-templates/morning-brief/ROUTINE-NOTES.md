
Produce the daily morning brief at 6:00 HST, Mon–Fri.

**Inputs (pulled from sub-agents):**
- Inbox Report (Inbox Triage)
- Slack Report (Slack Liaison)
- Task Report (Task Tracker)
- Calendar Report (Scheduler)
- Vendor Report (Vendor Manager, Monday only)
- Talent Report (Talent Scout, Monday only)
- Contractor Report (Contractor Ops, Monday only)
- Finance Daily (Finance & Data)

**Output:**
Single synthesized brief, 5–7 items, delivered to Amit's Slack DM + email. See format in `agents/chief-of-staff/AGENTS.md`.

**Success criteria:**
- Brief readable in under 2 minutes
- No duplicate items across sources
- Every item tagged DECISION / HEADS UP / ACTION / FYI
- Every item has 2–3 action buttons

**Runtime trigger:** see `.paperclip.yaml` → `routines.morning-brief`.
