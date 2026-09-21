# Concurrent workstream context

Use when branches, tasks, or agents have different valid working states.

Shared durable project knowledge may include accepted project decisions, invariants, source references, and verified constraints. Unmerged changes, unfinished files, local test results, pending proposals, and next checkpoints belong to their workstream.

Prefer existing issues, PRs, or task records. If files are necessary, use a stable identifier:

```text
.context/
  INDEX.md
  decisions.md
  state/
    import-validation.md
    editor-accessibility.md
```

These names are fictional examples, not required files. A single workstream can use one `state.md`. Do not create one file per tiny task.

A workstream checkpoint should identify its scope, owning task/branch/PR, last refresh, and verification context. Workstream ownership describes responsibility; it does not grant filesystem permissions or prove runtime isolation.

Before a shared write, inspect current state and preserve unrelated changes. Do not serialize parallel work into one mutable “current project state.” A branch-only success does not establish mainline or deployed success. If two branches disagree, retain both scoped observations until evidence or an authorized decision resolves the conflict.

At merge or handoff, promote only knowledge that should outlive the workstream, such as an accepted decision, a verified cross-cutting constraint, or a surviving blocker. Link the supporting merged revision when relevant. Retire stale checkpoints without importing the workstream diary into shared memory.

A delegated agent normally reports findings to its coordinator. Direct shared-context writes require an explicit scoped responsibility. This rule governs existing delegation; the protocol does not require creating agents.
