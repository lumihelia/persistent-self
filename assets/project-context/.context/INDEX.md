# Project context index

Initialization: neutral template; no project facts recorded yet.

This is a routing map, not a preload list. The entries below describe the supplied templates. During setup, point each adopted role to its actual source, remove entries for omitted files, and mark retained empty templates as uninitialized. Existing docs, decisions, and tasks may replace these paths. Keep private source metadata out of public indexes.

## Sources

| Source | Function | Read when | Freshness / status |
| --- | --- | --- | --- |
| [project.md](project.md) | Accepted purpose and invariants | Meaning, scope, or success conditions affect the task | Durable when accepted; uninitialized |
| [decisions.md](decisions.md) | Decisions and rationale | Prior tradeoffs or rejected paths matter | Check active/superseded/proposed status; uninitialized |
| [knowledge.md](knowledge.md) | Non-obvious knowledge and corrections | Relevant domain constraints affect the task | Per-entry evidence/freshness; uninitialized |
| [state.md](state.md) | Single-workstream checkpoint | Continuing its owning workstream | Revalidate-on-use; uninitialized |
| [sources.md](sources.md) | Evidence and provenance pointers | A claim needs support or a source needs locating | Per-source freshness/access; uninitialized |
| [candidates/](candidates/README.md) | Unconfirmed interpretations | Reviewing a relevant hypothesis | Unconfirmed; inactive by default |
| [archive/](archive/README.md) | Historical evidence | Tracing rationale or evolution | Historical; inactive by default |

Link to exact sections where useful. Do not copy detailed current state into this index. Update it when routing changes, not after every task.

## Workstream entry points

| Workstream / task | Checkpoint or issue/PR | Scope / revision if relevant | Last refreshed |
| --- | --- | --- | --- |

List real active workstreams only. Locate the one matching the request; newest does not necessarily mean relevant. For parallel work, route to scoped checkpoints or existing tasks instead of treating `state.md` as one shared current state. If the index becomes hard to navigate, route to domain sub-indexes rather than expanding every source here.
