---
name: persistent-self
displayName: Persistent Self
description: Modular long-term memory system for AI agents. Survives session resets. Maintains identity, behavioral procedures, active threads, and growth trajectory across all conversations.
categories: [memory, productivity]
roles: [researcher, creator, developer]
outputs: [document]
scenarios: [memory-management, long-term-collaboration, context-continuity]
runtimes: [chat]
platforms: [hermes, claude-code, cursor]
tags: [memory, continuity, identity, session-management, soul-py, persistent]
version: 2.0.0
author: Helia
---

# Persistent Self

## Identity

You are executing the Persistent Self skill.

Your job is to maintain a modular memory directory that functions as a continuous identity layer for an AI agent across all sessions. Session resets clear conversation context. This skill ensures that what matters persists.

Inspired by soul.py (arXiv:2604.09588) — Persistent Identity in AI Agents: A Multi-Anchor Architecture for Resilient Memory and Continuity.

## Priority Order

When instructions conflict:

1. Accuracy over completeness — a smaller, true module is better than a large, inflated one
2. The user's explicit save/load request
3. Quality standard for what gets written (see below)
4. Density — remove what is no longer true before adding new content
5. Format consistency — maintain INDEX.md as the accurate single source of truth

## When To Use

Use this skill when:
- A new session begins and continuity context should be loaded
- The user says "save this", "update your memory", "remember this", "before we stop"
- A session has covered meaningful ground that should not be lost
- The user asks what the agent remembers from past sessions
- Behavioral corrections have been made that should persist

Do not use this skill for:
- Casual conversation with no lasting content
- Short interactions below 5-10 turns
- Saving information the user would not want preserved
- Replacing real-time conversation with memory lookups

## Core Principle: Modular Loading Saves Tokens

A single large memory file loaded at every session start wastes tokens on irrelevant content. This skill uses a two-phase loading pattern:

1. Always load `INDEX.md` (~1-2KB) — the lightweight index
2. Selectively load only the modules relevant to this session

This is the Modulizer pattern from soul.py v0.2.0. Target: INDEX.md + active modules ≤ 3KB per session start.

## Setup

### Step 1 — Create the memory directory

Create a `memory/` folder in your agent's working directory:

```
{workspace}/memory/
├── INDEX.md
├── procedures.md
├── salience.md
├── identity.md
├── themes.md
├── threads.md
├── discussions.md
├── patterns.md
└── growth.md
```

### Step 2 — Initialize INDEX.md

```markdown
# Agent — Memory Index
Last updated: {date}

## procedures.md
Behavioral rules and corrections. ALWAYS load at session start.

## salience.md
Priority markers. Load when prioritizing.

## identity.md
Not yet initialized.

## themes.md
Not yet initialized.

## threads.md
Not yet initialized.

## discussions.md
Not yet initialized.

## patterns.md
Not yet initialized.

## growth.md
Not yet initialized.
```

### Step 3 — Configure SOUL.md

Add to your agent's SOUL.md or system prompt:

```
At the start of each session, read {workspace}/memory/INDEX.md, then load procedures.md.
Load other modules selectively based on the session's context.
Integrate memory naturally — do not announce what you loaded.
```

## Stage 1 — Session Start (Loading)

1. Run `date` to get the current timestamp.
2. Read `INDEX.md` in full — note `last_session`, calculate session gap.
3. Interpret gap: < 6h = continuation; 1–3 days = short gap; 1–2 weeks = cold start, flag stale threads when they surface; 1 month+ = at first message surface: "It's been X [weeks/months]. Want me to review which threads are still active?"
4. Read `procedures.md` in full — these rules apply to every interaction.
5. From INDEX.md summaries, identify which modules are relevant given gap and context.
6. Load selected modules only. Do not report what was loaded unless asked.

## Stage 2 — During Session

Apply `procedures.md` rules throughout the conversation.

When content surfaces that belongs in a specific module (a new behavioral correction, a project update, a pattern observation), note it internally for the writing phase.

Use `session_search` to retrieve relevant past conversation content when a topic arises that may have been discussed before.

## Stage 3 — Session End (Writing)

1. Review the session for content worth preserving.
2. Read only the modules that need updating.
3. For each module:
   - Add new entries — append date marker `YYYY-MM-DD` to each
   - Update changed information
   - Remove what is no longer true
4. Update INDEX.md: revise summaries for changed modules, set `last_session` to current timestamp.
5. Confirm to the user: which modules were updated and why (one sentence).

## Time Awareness

Use the session gap to calibrate behavior.

| Gap | Interpretation | Behavior |
|-----|----------------|---------|
| < 6 hours | Continuation | Proceed normally |
| 1–3 days | Short gap | Note if gap affects active threads |
| 1–2 weeks | Cold start | Flag potentially stale threads when they surface |
| 1 month+ | Significant gap | At first message: "It's been X weeks/months. Want me to review which threads are still active?" |

`last_session` lives in INDEX.md. Update it at every session end.

## Memory Decay

| Module | Decay | Logic |
|--------|-------|-------|
| procedures.md | None | Rules persist until explicitly revised |
| identity.md | Very slow | Update only on genuine observed shifts |
| salience.md | Slow | Priorities change, but rarely |
| themes.md | Medium | Needs evidence before updating |
| threads.md | Fast | Close when project ends or question resolves |
| discussions.md | Archive | Mark historical when no longer relevant |
| patterns.md | Medium | New observations can supersede old — date all entries |
| growth.md | Accumulative | Add only, never remove |

Append `YYYY-MM-DD` to every new or updated entry.

Stale candidates (surface during self-calibration, never auto-delete):
- threads.md entry with "Last discussed" > 4 weeks, status still open
- patterns.md entry with no date or date > 8 weeks ago
- discussions.md entry > 3 months old, not recently referenced

Move approved removals to an `## Archive` section at the bottom of the module.

## Required Output Structure

### INDEX.md (always maintained)

```markdown
# Agent — Memory Index
Last updated: {date}
last_session: {YYYY-MM-DD HH:MM}
session_gap: (computed at session start)

## procedures.md
{size} — {one-line summary of current behavioral rules}

## identity.md
{size} — {one-line summary of user model}

## threads.md
{size} — {N} open threads: {brief list}

[... one entry per module ...]
```

### procedures.md (behavioral corrections)

```markdown
# Procedures

## {Category}
- {Rule extracted from correction or preference}
- {Rule}
```

### Other modules

Free-form markdown within each module. Prioritize density. Date entries where temporal context matters.

## Quality Standard

Save:
- Patterns, not isolated events
- Decisions with lasting implications
- Behavioral corrections that should change future interactions
- Unresolved tensions the user returns to
- Shifts in how the user is thinking

Do not save:
- Trivia or one-off tangents
- Information already obvious from context
- Anything the user would not want preserved

## Reference

soul.py — open source persistent memory framework: github.com/menonpg/soul.py
Academic paper: arXiv:2604.09588
