---
schema: agentcompanies/v1
kind: company
slug: ceo-s-office
name: CEO's Office
description: A 12-agent Chief of Staff operating system for a founder-led company. Portable, open-source, BYO-connectors.
version: 0.1.0
license: MIT
authors:
  - name: Bodhi Chattopadhyay
    email: bodhiswattwac@gmail.com
homepage: https://github.com/bodhiswattwac/ceo-s-office
requireBoardApproval: false
tags:
  - chief-of-staff
  - founder-ops
  - paperclip
  - agent-company
goals:
  - Amit reads zero emails and zero Slack messages himself
  - Every renewal, hire, and contract is briefed before it lands on Amit's desk
  - Morning brief at 6:00 HST surfaces only items that need a human call
  - Automation engineer closes the loop by spotting new repetitive work weekly
requirements:
  secrets:
    - ANTHROPIC_API_KEY
    - GOOGLE_OAUTH_TOKEN
    - SLACK_BOT_TOKEN
    - NOTION_TOKEN
    - BRAVE_API_KEY
---

# CEO's Office — A Chief of Staff Operating System

A full 12-agent org chart that runs a founder's operational life: inbox, Slack, calendar, tasks, vendors, finance, contractors, hiring, travel, supplies, and a meta-agent that builds new agents.

**Built for:** Founders of ~10-person, profitable, CLG-aligned companies. Originally designed for the Sudowrite / Amit Gupta shape (9 FT + 10 contractors, no VC board, Hawaii timezone). The prompts ship with Sudowrite context — change `CUSTOMIZE.md` to adapt to your own company.

**Philosophy:**
- **Escalate less, do more.** Every agent has hard rules for what it handles silently and what requires a DECISION tag.
- **Never act on behalf of the principal without a draft-then-approve loop** (except items explicitly whitelisted under auto-action budgets).
- **Output contract: `DECISION` / `HEADS UP` / `ACTION` / `FYI`** — consistent across all 12 agents so the Chief of Staff can synthesize the morning brief without translation.

**Org chart:** see `ARCHITECTURE.md`.

**To run this:** see `SETUP.md`.

**To customize for your company:** see `CUSTOMIZE.md`.
