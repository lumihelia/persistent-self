---
name: persistent-self
description: Maintain durable, user-governed memory across AI-agent sessions using file-based global memory and project-local context. Use when saving, updating, reviewing, forgetting, migrating, or recovering long-term memory, durable corrections, decisions, preferences, or project state.
license: MIT
compatibility: Requires persistent file read/write access. Lifecycle hooks and conversation search are optional; hosts without them can use the same protocol manually.
metadata:
  author: Helia
  version: "3.0.0"
---

# Persistent Self

Persistent Self is a memory-governance protocol for AI agents with durable file access. It defines how to scope, load, classify, update, supersede, review, and delete memory. It does not provide storage, lifecycle hooks, or conversation search by itself.

## Core invariants

1. **The user governs durable memory.** Explicit requests to save, correct, forget, or delete take priority.
2. **Global memory and project context are separate scopes.** Project-specific state stays with the project unless it has a clear cross-project reason to become global memory.
3. **Confirmed memory and model inference are separate states.** An inference never becomes a durable fact silently.
4. **Provenance matters.** Durable entries should retain enough source information to distinguish user statements, corrections, artifacts, and agent observations.
5. **New truth supersedes old truth.** Do not keep contradictory statements simultaneously active.
6. **Load selectively.** A small boot digest points to canonical context; it is not a reason to inject the whole memory store into every session.
7. **Private by default.** Do not place personal memory in a public repository or externally share it unless the user explicitly intends that boundary.
8. **Read before write.** Check the existing target module and relevant conflicting entries before appending anything.

Read `references/MEMORY_MODEL.md` when implementing or changing the memory schema.

## Memory scopes

Classify every durable item before writing it.

### Global memory

Use for information that should survive across projects and conversations, such as:

- explicit behavioral corrections;
- stable preferences the user has clearly stated;
- durable cross-project goals or constraints;
- long-running cross-project threads;
- user-approved facts that repeatedly matter across contexts.

The repository's `memory/` directory is a neutral starter scaffold for this scope.

### Project context

Use for information whose meaning depends on one project, repository, product, research program, or workstream, such as:

- current implementation state;
- project decisions and rejected options;
- project-specific user constraints;
- next actions and open questions;
- source indexes and handoff notes.

The recommended project pattern is:

```text
memory.md        # short boot digest; not canonical authority
.context/        # canonical project context
```

A copyable scaffold lives under `assets/project-context/`.

### Session-only context

Keep information in the current conversation when it is temporary, one-off, weakly supported, or useful only for the current task. Do not create durable memory merely because something was mentioned.

When scope is ambiguous, prefer the narrower scope.

## Memory states

Use these states consistently:

- **confirmed** — directly stated or approved by the user, established by an authoritative project artifact, or recorded from an explicit correction/decision.
- **candidate** — a potentially useful agent observation or inference that has not been confirmed.
- **superseded** — replaced by newer confirmed information; retain only when an audit trail is useful.
- **archived** — no longer active but worth retaining as history.

Candidates do not guide durable personalization as if they were facts. Store them in `memory/observations.md` or a project inbox until reviewed.

Do not infer or store sensitive personal attributes as candidates. If the user explicitly asks to preserve sensitive information, store only the minimum necessary content and keep it private.

## Modes

Persistent Self has five operating modes:

1. **Load / recall**
2. **Save / update**
3. **Forget / delete**
4. **Review / calibrate**
5. **Initialize / migrate**

Choose the mode from the user's request and the current host capabilities.

## Mode 1 — Load / recall

### Global memory

1. Locate the configured global memory root.
2. Read `INDEX.md` first.
3. Read `procedures.md` when durable behavioral rules should apply.
4. Load only the additional modules relevant to the current task.
5. Treat `observations.md` as candidate material, not confirmed truth.
6. Do not announce loaded memory unless the user asks how continuity was established.

### Project context

1. Read the project's `memory.md` boot digest when present.
2. Treat `.context/` as canonical authority when the digest and context disagree.
3. Read `.context/INDEX.md`, then only the files needed for the current task.
4. For exact decisions, state, source provenance, or rejected ideas, consult the corresponding canonical file instead of relying on the digest.

If the host provides conversation search, use it only when file memory is insufficient or when provenance requires recovery from prior dialogue. Do not assume a tool named `session_search` exists.

## Mode 2 — Save / update

Trigger this mode when the user explicitly asks to remember/save/update something, when a durable correction or decision is made, or when host policy explicitly authorizes automatic memory maintenance.

Before writing:

1. Identify scope: global, project, or session-only.
2. Identify kind: procedure, profile fact/preference, priority, thread, project state, decision, source, observation, or other project-local context.
3. Identify state: confirmed or candidate.
4. Read the target module and any likely conflicting entry.
5. Check for duplication, contradiction, and existing supersession.

Write rules:

- Explicit user statements, corrections, and decisions may be written as confirmed.
- Facts taken from an authoritative project artifact may be written as confirmed within that project scope.
- Agent interpretations, personality judgments, inferred preferences, or pattern claims remain candidate unless the user confirms them.
- A newer confirmed entry that conflicts with an older active entry supersedes the old entry. Update the active module and preserve history only where useful.
- Project state goes to project context by default. Promote it to global memory only when it clearly matters across projects.
- Keep boot digests short. Update the canonical source first, then refresh the digest if the change affects startup context.

After writing, report concisely what scope was updated, which files changed, and whether anything remains candidate.

## Project-context routing

When a project uses the `memory.md + .context/` pattern, route updates as follows:

- `.context/00_project_brief.md` — durable project purpose, scope, and non-goals.
- `.context/01_current_state.md` — current implementation/research state and verified status.
- `.context/02_decision_log.md` — accepted decisions, date, rationale, and supersession.
- `.context/03_user_model.md` — project-specific collaboration constraints; do not duplicate the global profile without need.
- `.context/04_agent_roles.md` — agent/tool responsibilities and handoff boundaries.
- `.context/05_handoff_log.md` — concise continuity notes between working sessions or agents.
- `.context/06_open_questions.md` — unresolved questions and decision tensions.
- `.context/07_rejected_ideas.md` — intentionally rejected paths and why they were rejected.
- `.context/08_next_actions.md` — current executable next actions.
- `.context/09_source_index.md` — canonical files, references, evidence, and source provenance.

`memory.md` is a boot digest. It must not silently become the canonical authority for detailed facts that belong in `.context/`.

## Mode 3 — Forget / delete

When the user asks to forget or delete a memory:

1. Locate every active occurrence in the relevant scope.
2. Remove it from active memory.
3. Update indexes and boot digests that refer to it.
4. Do not preserve a hidden copy in `archive.md` when the request is to forget/delete the information itself.
5. If the request is only to retire an outdated item while preserving history, archive or supersede it instead.
6. Report what was removed and whether any derived summaries were also updated.

Deletion intent is stronger than archival intent.

## Mode 4 — Review / calibrate

Use review mode for memory hygiene, not constant self-commentary.

Check for:

- confirmed entries that conflict;
- candidates waiting for review;
- project facts that escaped into global memory;
- duplicated information across modules;
- stale priorities or threads;
- boot digests that no longer match canonical context;
- entries with missing provenance;
- personal data stored in public or shared locations.

Age alone does not make a memory false. Mark items for review based on changed evidence, inactivity, or project completion rather than fixed decay timers.

Do not auto-delete stale items. Ask for confirmation when the correct action is not established by newer evidence.

## Mode 5 — Initialize / migrate

### New global memory

Copy the neutral `memory/` scaffold into a private persistent location. Initialize only the modules that are actually needed.

### New project context

Copy `assets/project-context/memory.md` and its `.context/` directory into the project. Keep the boot digest short and use `.context/` as canonical authority.

### Existing memory

Never overwrite an existing `memory.md`, memory directory, or project context as a setup shortcut.

For v2 migration or any existing system:

1. Inventory current files and active memory.
2. Preserve a backup or untouched source copy.
3. Read `references/MIGRATION_V2_TO_V3.md`.
4. Classify each entry by scope, kind, state, and provenance.
5. Move project-specific material into project context.
6. Move unsupported inferences into candidates or drop them with the user's approval.
7. Verify the migrated store before retiring the old structure.

## Host portability

The skill is intentionally host-agnostic.

- Use the host's normal file tools for reads and writes.
- Use lifecycle hooks when available; otherwise perform load/write steps when explicitly invoked.
- Use the host's current-time capability when timestamps matter.
- Use conversation search only when the host provides it.
- Keep host-specific setup out of the memory data itself.

Read `references/HOST_INTEGRATION.md` when installing the protocol into a new agent host.

## Entry style

Keep durable entries compact and auditable. A simple Markdown entry is enough:

```markdown
- 2026-09-05 · confirmed · user-correction — Prefer concise completion reports.
```

Candidate example:

```markdown
- 2026-09-05 · candidate · agent-observation · medium — May prefer async review over live coordination. Needs confirmation.
```

Do not create elaborate schemas when a short source-marked entry is sufficient.

## Completion contract

After a memory-changing operation, report:

1. Scope changed: global / project.
2. Files changed.
3. Confirmed vs candidate status of new material.
4. Any superseded or deleted item.
5. Any unresolved conflict or migration risk.

Keep the report short.

## References

- `references/MEMORY_MODEL.md` — scopes, states, provenance, conflict handling, privacy.
- `references/HOST_INTEGRATION.md` — how to attach the protocol to a host agent.
- `references/MIGRATION_V2_TO_V3.md` — migration from the v2 module set and other existing stores.
- `assets/project-context/` — copyable project-local context scaffold.
