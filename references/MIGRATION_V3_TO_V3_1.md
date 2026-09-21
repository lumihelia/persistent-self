# Migration: v3.0 project scaffold → v3.1

v3.1 preserves person/global memory and makes project-only adoption explicit. The old ten-file project scaffold is optional legacy structure; existing users can keep filenames while adopting the new semantics. No automated migration runs on installation.

## Safe migration

Inspect existing instructions, context, source ownership, and sharing boundaries. Preserve the original in an authorized location or existing version history; a backup must not retain information subject to an explicit deletion request. Never publish a private backup as part of migration.

Classify by meaning, not filename. Prefer existing docs/issues/PRs to new duplicate files. Reconcile stale descriptions against current evidence while preserving accepted intent. Keep uncertain material candidate and unresolved conflicts visible. Prepare changes without overwriting unrelated work, then update affected routing and optional digests.

## Mapping

| v3.0 source | v3.1 destination or meaning |
| --- | --- |
| `00_project_brief.md` | Existing project docs or `project.md`: accepted purpose, scope, invariants |
| `01_current_state.md` | Owning issue/PR or checkpoint; revalidate-on-use, scoped by workstream |
| `02_decision_log.md` | Existing ADRs or `decisions.md`; keep acceptance, rationale, supersession |
| `03_user_model.md` | Only useful scoped constraints/preferences in `knowledge.md`; inference stays candidate |
| `04_agent_roles.md` | Existing project instructions/ownership docs; merge rather than replace |
| `05_handoff_log.md` | Current owning checkpoint; keep valuable historical evidence off the active path |
| `06_open_questions.md` | Existing issue/research record or surviving blocker in a checkpoint |
| `07_rejected_ideas.md` | Rejected decisions with rationale and revisit conditions |
| `08_next_actions.md` | Existing task system or next checkpoint; avoid a parallel backlog |
| `09_source_index.md` | Existing provenance map or `sources.md` |
| `INDEX.md` | Router to actual sources with task/freshness cues and workstream entry points |
| `memory.md` | Optional brief boot cache; pointers rather than detailed copied truth |

Do not transfer unsupported personal narratives as confirmed knowledge. Do not erase useful historical evidence merely to reduce the number of files. Private data remains private during classification and movement.

## Behavior changes to apply even without renaming

- Remove any blanket claim that `.context/` outranks live evidence or project contracts.
- Distinguish desired state, observed state, and historical state.
- Merge the project routing snippet into host-recognized instructions without overwriting them.
- Project-only initialization does not require a personal memory root.
- Stop mandatory full-directory copying/loading and per-session diary updates.
- Separate concurrent workstream state; do not convert the newest branch result into shared current truth.
- Preserve relevant evidence and uncertainty while reducing duplicated context.

## Verify before retiring old sources

Check source/digest/index links, unresolved decisions, candidate status, privacy, and workstream scope. Give a fresh session a continuation task without the old chat and observe recovery if the host supports this test. Record what was actually verified. Rename or retire originals only within authority after the target is sound; never silently delete populated user stores.

Users migrating v2 personal modules can consult [the historical v2 → v3 mapping](MIGRATION_V2_TO_V3.md), then route project material directly to these current semantic roles without creating the intermediate ten-file layout.
