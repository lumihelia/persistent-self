# Project context templates

The complete neutral `.context/` template is provided directly in this directory, using the same layout as a target project. There is no separate directory to assemble into `.context/`.

```text
project-context/
├── AGENTS.snippet.md
├── memory.md
└── .context/
    ├── INDEX.md
    ├── project.md
    ├── decisions.md
    ├── knowledge.md
    ├── state.md
    ├── sources.md
    ├── candidates/
    │   └── README.md
    └── archive/
        └── README.md
```

Template availability does not require creating, filling, or loading every file in every project. A project can adopt the complete scaffold or only the roles it needs. Empty headings and template notes are not project facts.

## Adoption

Inspect the project's existing instructions, docs, tasks, and privacy boundary first. Merge [AGENTS.snippet.md](AGENTS.snippet.md) into recognized project instructions, not the package's contributor AGENTS.md. In a new project, the `.context/` scaffold may be copied as a starting structure; for existing projects, merge selectively and never overwrite populated files. The agent can perform this setup within authorization; users do not need to move files manually.

Adapt [INDEX.md](.context/INDEX.md) to actual project sources. Replace a template entry with an existing doc/ADR/issue when that source already owns the information. Remove entries for omitted files; retain an explicit uninitialized marker for empty templates kept in the project. Read only the sources relevant to the current task.

## Read and update routing

| File | Read when | Update when | Keep out |
| --- | --- | --- | --- |
| [INDEX.md](.context/INDEX.md) | Locating relevant context or a workstream | Sources move, their role/status changes, or workstream entry points change | Copied source bodies and a full project history |
| [project.md](.context/project.md) | Purpose, scope, invariants, or success conditions affect the task | An authorized project decision changes accepted meaning | Unaccepted proposals and volatile implementation status |
| [decisions.md](.context/decisions.md) | A prior choice, rejection, or tradeoff matters | A consequential decision is accepted, rejected, or superseded | Every small technical choice or raw discussion |
| [knowledge.md](.context/knowledge.md) | Non-obvious semantics, constraints, or corrections affect the work | Evidence establishes or corrects reusable scoped knowledge | Cheaply inspectable facts and broad personal profiling |
| [state.md](.context/state.md) | Resuming the owning workstream | A meaningful milestone, interruption/handoff, blocker, or verification changes continuation | Other workstreams' state, transcript diaries, duplicate backlogs |
| [sources.md](.context/sources.md) | Checking evidence, provenance, or source availability | Evidence pointers, supported claims, or freshness/access boundaries change | Whole source bodies or private metadata in public context |
| [candidates/](.context/candidates/README.md) | Reviewing a relevant unresolved hypothesis | A useful inference needs preservation, confirmation, narrowing, or removal | Sensitive inferred traits or automatic promotion to facts |
| [archive/](.context/archive/README.md) | Historical rationale or provenance is specifically needed | Superseded material retains explanatory value and retention is permitted | Active instructions or hidden copies of information requested deleted |
| [memory.md](memory.md) | A short boot cache helps locate continuation state | A load-bearing pointer or recovery note changes | Duplicated decisions, detailed state, and a single global status for parallel work |

## Single and concurrent workstreams

The supplied `state.md` is the single-workstream checkpoint template. If several workstreams coexist, adapt its contents to `.context/state/<workstream>.md`, or use existing issues/PRs instead. Preserve each stream's identity and evidence. Update INDEX to the chosen entry points; do not keep a second aggregate `state.md` that competes with the owning records.

Candidate and archive directories are available when useful; small projects can use clearly marked entries in existing records. Public templates do not make populated instance data public. Keep private context and private routing within their authorized stores.
