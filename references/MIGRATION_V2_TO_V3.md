# Migration: Persistent Self v2 → v3

v3 changes both the module set and the memory-governance model. Treat migration as classification, not file renaming.

## Safety rule

Never overwrite an existing `memory.md`, memory directory, or `.context/` during migration.

1. Inventory the current store.
2. Preserve an untouched source copy or backup.
3. Create the v3 target structure separately.
4. Migrate entries by scope and evidence state.
5. Verify the target.
6. Retire the old structure only after verification.

## Module mapping

### `procedures.md` → `memory/procedures.md`

Keep explicit behavioral corrections and durable collaboration rules.

Remove rules inferred from isolated behavior unless the user confirms them.

### `identity.md` → `memory/profile.md`

Migrate only stable, explicitly confirmed facts, preferences, goals, and constraints.

Model-authored identity claims do not migrate as confirmed facts. Put potentially useful non-sensitive observations in `observations.md` as candidates or drop them.

### `salience.md` → `memory/priorities.md` or project context

Cross-project priorities may move to `priorities.md`.

Project-specific priorities, milestones, and status move to `.context/01_current_state.md` or `.context/08_next_actions.md`.

### `themes.md`

Confirmed cross-project themes can become `profile.md` entries or `threads.md` when they represent ongoing work.

Project-specific themes stay in project context.

### `threads.md`

Cross-project threads stay in global `memory/threads.md`.

Project-local threads move to `.context/06_open_questions.md` and `.context/08_next_actions.md`.

### `discussions.md`

Do not import every past conversation into active memory.

Keep durable decisions in the relevant active module. Keep important historical context in `archive.md` or a project source/handoff log. Drop low-value summaries.

### `patterns.md` and `growth.md`

These files are the highest-risk v2 modules because agent interpretation can become indistinguishable from user fact.

Default migration state is `candidate`, not confirmed.

- Move concrete, non-sensitive, potentially useful observations to `memory/observations.md` with provenance and confidence.
- Promote only after confirmation.
- Drop abstract personality narratives that do not change future work reliably.

## Project-state extraction

Search every v2 module for information that belongs to a specific project:

- implementation status;
- project decisions;
- open questions;
- next actions;
- rejected paths;
- project-specific user constraints;
- source references.

Move these into the project's `memory.md + .context/` structure rather than keeping them global.

## Conflict review

During migration, look for multiple active claims about the same topic.

- newer confirmed evidence supersedes older confirmed evidence when the change is clear;
- candidates do not override confirmed memory;
- unresolved conflicts are surfaced for user review.

## Verification checklist

Before retiring v2:

- [ ] No author/example seed data remains in the user's live store by accident.
- [ ] Global memory contains only cross-project durable material.
- [ ] Project state has moved to project context.
- [ ] Candidates are visibly separated from confirmed memory.
- [ ] Conflicting current truths have been resolved or flagged.
- [ ] Boot digests match canonical files.
- [ ] Explicit forget/delete requests are not preserved in archive copies.
- [ ] The original v2 store remains recoverable until the user accepts the migration.
