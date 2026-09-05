# Persistent Self

[中文](./README.md) · English

A **file-based memory-governance skill** for AI agents. It defines what should persist across sessions, where it belongs, what is confirmed, what is still only a candidate observation, and how newer information supersedes older memory.

**Current version:** `3.0.0`

Persistent Self does not provide a database, vector store, or cloud memory service. The host agent supplies durable file access. This repository provides the protocol, a portable Agent Skill, a neutral global-memory scaffold, and a project-context scaffold.

## What changed in v3

v3 separates several decisions that v2 mixed together:

```text
current conversation
   ↓
is this worth persisting?
   ↓
┌───────────────────────┬────────────────────────┐
│ Global memory         │ Project context        │
│ durable across work   │ meaningful in one      │
│ and projects          │ project                │
└───────────────────────┴────────────────────────┘
   ↓                            ↓
confirmed / candidate       memory.md + .context/
```

Four boundaries define the release:

1. **Global memory is separate from project context.** Project state does not become global identity merely because it appears often.
2. **Confirmed memory is separate from candidate inference.** Agent observations do not silently become facts.
3. **Boot digests are separate from canonical sources.** A short startup summary points to precise context instead of replacing it.
4. **Protocol is separate from instance data.** The public repository contains neutral templates; real personal memory stays in a private/local instance by default.

v3 keeps the selective-loading idea from v2 and adds scope, provenance, supersession, deletion, and privacy governance learned from longer real-world use.

## Two memory scopes

### Global memory

`memory/` is the neutral starter scaffold for cross-project durable memory:

```text
memory/
├── INDEX.md          # lightweight entry point and module summaries
├── procedures.md     # confirmed durable behavioral rules
├── profile.md        # confirmed stable facts and preferences
├── priorities.md     # current cross-project priorities
├── threads.md        # long-running cross-project threads
├── observations.md   # unconfirmed candidate observations
└── archive.md        # historical material worth retaining
```

`observations.md` is not a trusted profile. Candidate material must be confirmed before it becomes durable personalization.

### Project context

Project-specific state follows this pattern:

```text
memory.md        # short boot digest; not canonical authority
.context/        # canonical project context
```

A copyable scaffold lives in [`assets/project-context/`](./assets/project-context/). It includes project brief, current state, decision log, project-specific user model, agent roles, handoff notes, open questions, rejected ideas, next actions, and a source index.

Small projects can keep only the modules they actually need.

## Memory states

Persistent Self v3 uses four states:

- `confirmed` — directly stated or approved by the user, or established by a canonical project artifact;
- `candidate` — an agent observation or inference awaiting confirmation;
- `superseded` — replaced by newer confirmed information;
- `archived` — no longer active but still useful as history.

When a newer confirmed entry conflicts with older active memory, the newer truth supersedes the old one. Historical records may remain, but contradictory statements should not both stay active.

## Provenance

Durable memory should retain a lightweight answer to “why is this here?” A simple Markdown entry is enough:

```markdown
- 2026-09-05 · confirmed · user-correction — Prefer concise completion reports.
```

Candidate example:

```markdown
- 2026-09-05 · candidate · agent-observation · medium — May prefer async review over live coordination. Needs confirmation.
```

The goal is auditability, not turning Markdown into a database schema.

## Write policy

v3 no longer uses turn-count heuristics or fixed memory-decay timers. Persistence depends on the kind of information:

- explicit save requests can create durable memory;
- explicit corrections and durable decisions can become confirmed immediately;
- inferred preferences, personality claims, and agent interpretations remain candidates;
- project state goes to project context by default;
- temporary emotions, one-off task details, and weak evidence stay session-only;
- read the target module before writing so duplicates and conflicts are visible;
- newer confirmed truth supersedes older active truth;
- when the user asks to `forget` or `delete`, remove the active memory and derived summaries rather than preserving a hidden copy in an archive.

See [`SKILL.md`](./SKILL.md) and [`references/MEMORY_MODEL.md`](./references/MEMORY_MODEL.md) for the full protocol.

## Installation

Persistent Self follows the current [Agent Skills](https://agentskills.io/) structure: a skill directory with `SKILL.md`, plus optional references and assets loaded on demand.

Install the **whole repository contents** as the `persistent-self/` skill directory so the skill can load its references and project-context assets when needed.

Common user-level locations include:

```text
# generic / supported by some clients
~/.agents/skills/persistent-self/

# Codex
~/.codex/skills/persistent-self/

# Claude Code
~/.claude/skills/persistent-self/

# Cursor
~/.cursor/skills/persistent-self/

# Windsurf
~/.codeium/windsurf/skills/persistent-self/

# Hermes
~/.hermes/skills/persistent-self/
```

Project-level skill roots are also supported by several hosts. See [`references/HOST_INTEGRATION.md`](./references/HOST_INTEGRATION.md) for lifecycle and storage guidance.

## Initialize global memory

Copy the `memory/` scaffold into a **private, durable location**, then tell the host agent where that memory root lives.

A typical session-start load is small:

1. read `INDEX.md`;
2. read `procedures.md`;
3. selectively load other modules relevant to the current task.

Do not inject the entire memory directory into every request.

## Initialize project context

Copy:

```text
assets/project-context/memory.md
assets/project-context/.context/
```

into the project root.

`memory.md` stays short and startup-oriented. Precise decisions, state, sources, and history live under `.context/`.

Project context can contain private information. A public repository should not automatically publish project-specific user data, internal decisions, or private source notes merely because it uses this scaffold.

## Migrating from v2

The old default modules `identity / salience / themes / discussions / patterns / growth` are not part of the v3 starter layout.

Migration rules:

- `procedures.md` → keep confirmed durable rules;
- `identity.md` → move only explicitly confirmed, stable content into `profile.md`;
- `salience.md` → move cross-project priorities into `priorities.md`;
- `threads.md` → keep cross-project threads global; move project-specific threads into `.context/`;
- `patterns.md` / `growth.md` → review as candidates instead of importing them as user facts;
- `discussions.md` → keep only durable conclusions in active memory; otherwise preserve as history/provenance if useful.

**Never overwrite an existing `memory.md`, memory directory, or `.context/` during setup.** Preserve the source, migrate, verify, then retire the old structure.

See [`references/MIGRATION_V2_TO_V3.md`](./references/MIGRATION_V2_TO_V3.md).

## Current boundaries

- No database, vector store, or background service.
- No universal session-start hook.
- Conversation search is optional; the skill no longer assumes a tool named `session_search` exists.
- Real global-memory instances should remain private.
- Whether project context belongs in Git depends on the project's privacy boundary.
- Selective loading is a design principle; actual context cost depends on host, model, and content.

## Repository map

```text
SKILL.md                     # v3 execution protocol
memory/                      # neutral global-memory scaffold
references/
  MEMORY_MODEL.md            # scope / state / provenance / privacy
  HOST_INTEGRATION.md        # host integration
  MIGRATION_V2_TO_V3.md      # v2 → v3 migration
assets/project-context/      # copyable project memory.md + .context/
README.md                    # Chinese overview
README.en.md                 # English overview
LICENSE                      # MIT
```

## Design lineage

Persistent Self was originally inspired by the modular-memory ideas in [soul.py](https://github.com/menonpg/soul.py). v3 also incorporates later practice from long-running agent collaboration: lightweight boot digests, canonical project context, confirmed/candidate separation, traceable updates, and global/project scope separation.

## License

[MIT](LICENSE)
