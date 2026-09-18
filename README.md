# Executive Productivity Agent

An agent that turns a VP's messy week — a meeting transcript, calendars, email threads, and voice notes — into a single reconciled daily brief, with a Q&A interface on top.

Built for AIONOS Agentic AI Factory, Assignment 1.

**Live demo:** https://vartiktripathi.github.io/executive-productivity-agent/
**Demo video:** https://drive.google.com/file/d/1lwIpjubmX_SFJcW6Vj1urMAiAoZqAx3e/view?usp=drivesdk

## What it does

The user (Arjun Malhotra, VP Sales) generates commitments and open items across five different channels during a normal week. The agent:

- Identifies commitments Arjun made himself (in meetings, emails, and voice notes)
- Separates "my actions" from "waiting on others"
- Detects deadlines, and flags anything overdue against the current time
- Deduplicates the same action when it appears in multiple sources
- Flags unclear ownership instead of guessing who owns something
- Produces a daily brief
- Answers direct questions like "What did I promise Raghav?" or "What needs action today?"

## Architecture / Process Flow

```
Ingest  →  Extract  →  Reconcile  →  Classify  →  Brief / Q&A
```

1. **Ingest** — all raw sources are loaded: 1 meeting transcript, 4 calendars, 5 email threads (25 messages total), 2 personal voice notes.
2. **Extract** — each source is scanned for candidate commitments: who said it, who it's to, what was promised, and by when. Every candidate keeps a verbatim anchor back to its source line.
3. **Reconcile (deduplication)** — candidates are grouped by subject and counterparty. Example: the vendor list is mentioned in the transcript, 5 emails, and a voice note — these collapse into one action, not six. Within a group, the most recent statement is treated as current, but earlier statements are kept visible as a "slip trail" rather than deleted, since the number of times something slipped is itself useful signal.
4. **Classify** — an item is only assigned to a person as "my action" when there is an explicit first-person commitment ("I'll send…"), and only marked "waiting on X" when X explicitly accepted it. Anything hedged with language like "I think," "I believe," or "not sure" is routed to an **unclear ownership** state and is never auto-assigned.
5. **Brief / Q&A** — the reconciled store is used to generate the daily brief (grouped into overdue / due today / unclear ownership / waiting on others / upcoming / closed) and to answer direct natural-language questions, always with a source citation attached.

## Inputs, Sources & Assumptions

**Inputs used (all from the supplied data pack, week of 21–25 Sep 2026):**
- 1 meeting transcript (Leadership Sync, Mon 21 Sep)
- 4 calendars (Arjun, Neha, Raghav, Divya)
- 5 email threads, 5 messages each (Vendor List, Q3 Campaign Deck, Call Reschedule, Expense Variance Report, Mumbai Office Lease Renewal)
- 2 personal voice notes recorded by Arjun

**Key assumptions:**
- Voice notes are treated as Arjun's own commitments to himself, not as instructions from a third party.
- When the same commitment appears with different dates across sources, the most recently timestamped statement is treated as the operative one.
- Ownership is never inferred from a hedge. "I think that's supposed to be Facilities" is a guess, not a confirmation, and is not enough to assign an action.
- The agent's sense of "now" is a settable clock rather than a fixed date, since the brief is meant to be read fresh on any given day of the week — the same data produces a different brief on Monday morning than on Friday afternoon.
- No information outside the supplied data pack was used or invented.

## AI Tools Used

- **Claude (Anthropic)** — used to design the reconciliation logic (deduplication and ownership-classification rules), and to generate the front-end prototype (`index.html`) implementing that logic as a working, clickable tool.

## Repo Structure

```
executive-productivity-agent/
├── index.html          — the working agent (open directly or via GitHub Pages)
├── README.md            — this file
├── data/
│   └── datapack.md       — full source data the agent reads from
└── docs/
    └── architecture.png  — process-flow diagram (add your own export)
```
