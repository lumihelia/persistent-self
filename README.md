# Persistent Self

Modular long-term memory system for AI agents. Survives session resets.

Built for [Hermes](https://github.com/hermesagent/hermes) — works on any chat-based AI agent.

Inspired by [soul.py](https://github.com/menonpg/soul.py) (arXiv:2604.09588).

---

## The problem

AI agents forget everything when a session ends. The memory systems built into most agents are either too small (a few KB of flat text) or too slow (semantic search on every message). Neither gives you a real persistent self.

## The solution

A modular memory directory that lives on disk, outside the session context. Session resets don't touch it.

```
memory/
├── INDEX.md         ← Always loaded (~1-2KB). One-line summary per module.
├── procedures.md    ← Behavioral rules. Loaded every session.
├── salience.md      ← Priority markers.
├── identity.md      ← Living user model.
├── themes.md        ← Recurring interests across time.
├── threads.md       ← Active unresolved projects and questions.
├── discussions.md   ← Key past conversations.
├── patterns.md      ← Observed cognitive patterns.
└── growth.md        ← Trajectory and calibration findings.
```

Two-phase loading keeps token cost low:
1. Always load `INDEX.md` + `procedures.md` (~2-3KB total)
2. Selectively load other modules based on what's relevant to the current session

Target: ≤ 3KB loaded per session start.

---

## Setup

1. Copy the `memory/` directory into your agent's working directory
2. Add to your agent's system prompt / SOUL.md:

```
At the start of each session, read {workspace}/memory/INDEX.md, then load procedures.md.
Load other modules selectively based on the session's context.
Integrate memory naturally — do not announce what you loaded.
```

3. Tell your agent to run the `persistent-self` skill when you say **"save this"** or **"update your memory"**

---

## Usage

| What you say | What happens |
|---|---|
| _(new session starts)_ | Agent reads INDEX.md + procedures.md automatically |
| "save this" | Agent distills session into relevant modules, updates INDEX.md |
| "update your memory" | Same as above |
| "what do you remember about X?" | Agent loads the relevant module and responds |

---

## Reference

- soul.py: [github.com/menonpg/soul.py](https://github.com/menonpg/soul.py)
- Paper: [arXiv:2604.09588](https://arxiv.org/abs/2604.09588)
