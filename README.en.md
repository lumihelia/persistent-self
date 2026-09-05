# Persistent Self

[中文](./README.md) · English

A modular long-term memory skill for AI agents. Persistent files carry information across sessions; a lightweight index and selective loading keep the active context small.

**Current skill version:** `2.0.0`

Persistent Self was originally designed around Hermes and takes inspiration from the Modulizer pattern in [soul.py](https://github.com/menonpg/soul.py). The repository does not run a service and contains no database or vector-retrieval layer. The host agent performs the actual reads and writes.

## Core structure

Memory is split into a lightweight index and modules with distinct responsibilities:

```text
memory/
├── INDEX.md         # Module index; read first at session start
├── procedures.md    # Durable behavioral rules and corrections
├── salience.md      # Current priorities
├── identity.md      # Evolving user model
├── themes.md        # Recurring themes
├── threads.md       # Active unresolved projects and questions
├── discussions.md   # Past conversations worth retaining
├── patterns.md      # Patterns observed over time
└── growth.md        # Changes, trajectory, and calibration notes
```

Loading happens in two layers:

1. Read `INDEX.md` and `procedures.md` at session start.
2. Load other modules only when the current context makes them relevant.

`SKILL.md` also defines session-gap handling, module-specific decay rates, stale-entry candidates, and the write-back flow at session end.

The central design choice is **selective loading**: long-term information stays on disk while only a small relevant subset enters the current context.

## Host requirements

Persistent Self is not a prompt that works unchanged in every chat interface. The full workflow assumes a host that can:

- persistently read and write files;
- load project-level instructions at the start of a new session;
- read Markdown modules on demand;
- obtain the current date or time;
- write session updates back into the memory directory.

The current `SKILL.md` also references `session_search`. Hosts without an equivalent conversation-retrieval capability can still use the file-memory layer, but that step needs host-specific adaptation.

## Installing the skill

`SKILL.md` can be installed as an Agent Skill. Place the repository, or at minimum its `SKILL.md`, inside a directory named `persistent-self` under the host's skill root.

### Claude Code

Project-level:

```text
.claude/skills/persistent-self/SKILL.md
```

Personal:

```text
~/.claude/skills/persistent-self/SKILL.md
```

### Codex

Default personal location:

```text
~/.codex/skills/persistent-self/SKILL.md
```

With a custom `CODEX_HOME`:

```text
$CODEX_HOME/skills/persistent-self/SKILL.md
```

### Cursor

Project-level locations include:

```text
.cursor/skills/persistent-self/SKILL.md
.agents/skills/persistent-self/SKILL.md
```

Cursor also discovers compatible skills from Claude Code and Codex skill directories.

### Windsurf

Project-level:

```text
.windsurf/skills/persistent-self/SKILL.md
```

Cross-agent project location:

```text
.agents/skills/persistent-self/SKILL.md
```

Personal:

```text
~/.codeium/windsurf/skills/persistent-self/SKILL.md
```

### Hermes

Hermes currently uses this local skill root:

```text
~/.hermes/skills/persistent-self/SKILL.md
```

Hermes now ships its own memory and skills systems. Persistent Self therefore works best as an alternative, inspectable modular file-memory architecture rather than a required Hermes component.

### BotLearn SkillHunt

BotLearn currently uses `install` for skill installation, with `skillhunt` retained as an alias:

```text
botlearn install persistent-self
```

## Initializing the memory directory

Installing the skill provides the workflow rules. Cross-session memory also requires a persistent `memory/` directory.

Copy the repository's `memory/` scaffold into the agent's long-lived workspace, then add a small rule to the host's **project-level instructions that load every session**, for example:

```text
At the start of each session, read memory/INDEX.md and memory/procedures.md.
Load other memory modules only when they are relevant to the current session.
Keep memory integration implicit unless the conversation asks about it.
```

The appropriate context file depends on the host. In current Hermes versions, project instructions belong in `HERMES.md` / `.hermes.md` or `AGENTS.md`; `SOUL.md` is primarily the instance-wide personality and tone file.

## About the included memory scaffold

The `memory/` directory originated in the author's own experimental environment. Most modules remain uninitialized templates, while a small number still contain author-specific seed material and project examples.

Before reusing the scaffold for a different agent, review and neutralize existing content, then initialize identity, procedures, salience, and active threads for the new instance. The current `INDEX.md` is mostly uninitialized and should not be read as a complete generic example dataset.

## Current boundaries

- No background service, database, or vector store.
- No universal automatic-injection layer; startup behavior depends on host-level project instructions.
- No cross-host implementation of `session_search`.
- Memory files can contain real user information; personal instances should not be stored in a public repository or other publicly synchronized location.
- The two-phase loading design and `≤ 3KB` target are design constraints in the current skill, not benchmarked performance guarantees across every host.

## Repository map

- `SKILL.md` — Persistent Self v2.0.0 instructions and metadata
- `memory/` — modular memory scaffold
- `README.md` — Chinese repository overview
- `README.en.md` — English repository overview

`SKILL.md` defines current execution behavior. The README documents usage, host requirements, and known boundaries.

## References

- [soul.py](https://github.com/menonpg/soul.py)
- [Persistent Identity in AI Agents: A Multi-Anchor Architecture for Resilient Memory and Continuity](https://arxiv.org/abs/2604.09588)

## License

[MIT](LICENSE)
