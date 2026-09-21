# Persistent Self

[中文](README.md) · English

File-based rules for AI-agent memory and project context, helping separate conversations recover the information needed to keep working with the same person or on the same project.

**Protocol version: `3.1.0`**

The project mode aims to **restore enough context to continue correctly in the next conversation, with as little necessary reading as possible.**

This repository provides a reusable Skill, personal-memory and project-context templates, and acceptance scenarios. It requires an agent tool or platform with persistent file access and support for project instructions, referred to below as the “host.”

## Choose a use case

| Mode | What continues | How to use it |
| --- | --- | --- |
| Person-oriented | User-confirmed preferences, collaboration rules, corrections, and cross-project goals | Initialize private personal memory and load selectively |
| Project-oriented | Project goals, decisions, constraints, unfinished work, and verification boundaries | Configure a project context entry point and recover from checkpoints |
| Combined | Personal collaboration and project continuity together | Configure each independently; load only relevant, authorized context |

**Project mode works independently, without a personal profile or global-memory store.** Person-oriented describes a use case; global describes a storage scope. Repeated project preferences do not automatically become global personal facts.

## Returning to a project after a gap

> “Find the last checkpoint for this project, recover the necessary context, and continue the work.”

The agent uses project instructions, an INDEX, an optional short digest, or an existing issue/PR to locate the workstream matching the current request. The newest entry may belong to a different task, so timestamps alone cannot select it.

The checkpoint should recover the target, accepted constraints, stopping point, unfinished changes, what was verified, what still needs verification, and where to begin. The agent then checks facts that affect action, such as whether a branch merged, files still have changes, or a blocker remains.

If a decision's rationale is missing, search the relevant decision and source. If verification evidence is missing, find the corresponding record. Each expansion of reading should answer a specific question. Stop when the next action is sufficiently grounded. State essential unrecoverable gaps instead of inventing history.

See [project context](references/PROJECT_CONTEXT.md) for the full rules and [workstream rules](references/CONCURRENT_WORK.md) for parallel branches or tasks.

## Preserve context while controlling reading

A project can retain a large body of material while each conversation reads only a relevant subset.

- **INDEX is a map.** It explains where sources live, when they matter, and whether freshness needs checking. It does not copy detailed content or require reading every link.
- **Checkpoints preserve continuation state.** Replace stale content, retain evidence and unknowns, and avoid transcript or handoff diaries.
- **Decisions and knowledge preserve reasons and sources.** Prefer links to existing docs, issues, and PRs rather than duplicate current truth.
- **History is retrieved on demand.** Keep it off the default read path. Split into domain sub-indexes only when navigation becomes difficult.

“API complete” is short but may omit “only locally tested; not deployed.” Reducing reading must preserve information that changes the next action.

Improve names, routing, source pointers, and duplicates first. Consider stronger retrieval only when repeated missed context or excessive irrelevant reading survives those repairs. More project files alone do not require a vector database. See [retrieval scale and cost](references/RETRIEVAL_SCALING.md) for methods and measurement guidance.

## Initialize project context

Ask the agent to use this Skill with the target project:

> “Set up Persistent Self in project-only mode for this project. Inspect existing instructions, docs, and task records first. Reuse existing sources and create the smallest useful context recovery entry point. Preserve existing content and verify whether a fresh conversation can continue from a checkpoint.”

Merge the [project instruction snippet](assets/project-context/AGENTS.snippet.md), then adopt templates according to existing project sources. The [.context/ template](assets/project-context/.context/INDEX.md) matches the target project's directory layout.

A project can adopt the complete template or only the roles it needs. When existing documents serve the same purpose, INDEX points to them directly. The agent can inspect the current structure and merge the needed templates and instructions while preserving existing content.

```text
project/
├── AGENTS.md                 # or other host-recognized project instructions
├── memory.md                 # optional short recovery-entry cache
└── .context/
    ├── INDEX.md              # find context relevant to the current task
    ├── project.md            # purpose, scope, durable constraints
    ├── decisions.md          # tradeoffs and reasons for acceptance/rejection
    ├── knowledge.md          # non-obvious knowledge and important corrections
    ├── state.md              # current workstream checkpoint
    ├── sources.md            # source and evidence pointers
    ├── candidates/           # unconfirmed interpretations or hypotheses
    │   └── README.md
    └── archive/              # useful history outside active context
        └── README.md
```

### Which file changes in which situation?

| File | Read when | Update when |
| --- | --- | --- |
| [INDEX.md](assets/project-context/.context/INDEX.md) | Locating relevant context or a workstream | Source locations, purposes, statuses, or workstream entry points change |
| [project.md](assets/project-context/.context/project.md) | Project goals, scope, or constraints matter | An authorized decision changes purpose, boundaries, or success conditions |
| [decisions.md](assets/project-context/.context/decisions.md) | Understanding a prior choice or rejected alternative | A consequential option is accepted, rejected, or superseded |
| [knowledge.md](assets/project-context/.context/knowledge.md) | The task involves relevant non-obvious knowledge or constraints | Reusable knowledge is established with evidence or an important correction arrives |
| [state.md](assets/project-context/.context/state.md) | Resuming its workstream after interruption | A milestone completes, a handoff is needed, or blocker/verification state changes |
| [sources.md](assets/project-context/.context/sources.md) | Finding support or checking provenance | Evidence locations, supported claims, freshness, or access conditions change |
| [candidates/](assets/project-context/.context/candidates/README.md) | Reviewing a task-relevant unconfirmed hypothesis | A useful inference needs preservation, confirmation, narrowing, or withdrawal |
| [archive/](assets/project-context/.context/archive/README.md) | Tracing historical rationale | Material leaves active context but retains value and may be retained |

For example, discovering an undocumented API constraint can update `knowledge.md` with evidence. Choosing a different approach updates `decisions.md`. Stopping after local tests pass, with end-to-end validation still pending, updates the owning `state.md`. These changes do not require rewriting every other file.

Parallel tasks use `.context/state/<workstream>.md` or existing issues/PRs. INDEX points to the owning records so tasks do not overwrite one shared `state.md`.

**Supplying the complete template does not require reading or filling every file on each task.** After setup, INDEX lists only retained sources. Empty templates that remain are marked uninitialized and are not project facts. See the [asset guide](assets/project-context/README.md) for adoption details.

Accepted decisions describe intended behavior; live evidence describes current behavior. `memory.md` is a cache, and `.context/` organizes knowledge and pointers. A filename cannot turn an outdated record into current fact.

## Initialize personal memory

Copy the [memory/](memory/INDEX.md) templates to a separate private directory, such as `~/.persistent-self/memory/`, and configure that path in host instructions. Keeping memory separate from the Skill installation directory helps prevent Skill updates from overwriting it. Enable modules as needed:

| File | Purpose |
| --- | --- |
| `INDEX.md` | Lightweight routing |
| `procedures.md` | Confirmed collaboration rules, loaded when applicable |
| `profile.md` | User-confirmed stable facts and preferences |
| `priorities.md` | Cross-project priorities |
| `threads.md` | Long-running cross-project questions or workstreams |
| `observations.md` | Candidate observations awaiting confirmation |
| `archive.md` | Useful inactive history |

`observations.md` holds observations awaiting confirmation. Once confirmed, the relevant content can enter established memory.

## Updating memory

You can ask the agent to save, correct, or delete a memory, or enable ongoing maintenance in project instructions. Once enabled, the agent updates necessary state at meaningful milestones, corrections, blockers, or handoffs. Writes depend on future usefulness; files do not need updating after every turn.

Distinguish confirmed content, directly observed facts, candidate inferences, superseded information, and inactive history. Preserve scope, source, and freshness for consequential entries. Repetition does not turn an agent inference into a personal fact or accepted project decision.

Deleting a memory should remove the relevant entries and derived summaries. If the content entered Git history or backups, those copies need separate handling. The agent should explain what was deleted and where copies remain.

Personal memory is private by default. Teams can share project decisions and context they need in common while storing sensitive material separately. Source links and summaries in indexes should follow the same access boundaries.

See the [memory model](references/MEMORY_MODEL.md) and [Skill protocol](SKILL.md).

## Host integration and verification

Install the whole package in a host-supported Skill location so relative references and assets remain available. Actual paths and instruction mechanisms depend on the current host; project use does not require global installation. See [HOST_INTEGRATION.md](references/HOST_INTEGRATION.md).

After setup, check the integration in a fresh session without the old conversation. Ask the agent to resume a workstream and observe whether it discovers project instructions, finds the matching checkpoint, and continues from the current state.

Persistent Self runs through the host's file and instruction mechanisms. Use automatic project-instruction loading when the host supports it; otherwise provide the project path or context entry point in the new session. Session hooks and conversation search are optional host capabilities.

[Acceptance scenarios](evals/SCENARIOS.md) check both recovery of critical constraints and unnecessary reading. Actual token metrics must come from runs; character counts are only a reading-volume proxy.

## Migration

v3.1 updates the project side from a ten-file scaffold to selective recovery and workstream-owned state. Existing filenames may remain; the new semantics and routing matter. Inspect and preserve originals, migrate by source and scope, verify, and only then retire old structure. Installation does not automatically migrate user data.

- [v3.0 → v3.1 project migration](references/MIGRATION_V3_TO_V3_1.md)
- [Historical v2 personal-memory migration](references/MIGRATION_V2_TO_V3.md): project material can move directly into the current structure without creating the intermediate ten-file layout.

## Design lineage and license

Persistent Self was originally inspired by the modular-memory ideas in [soul.py](https://github.com/menonpg/soul.py). Its project-context rules developed through long-running agent collaboration.

[MIT](LICENSE)
