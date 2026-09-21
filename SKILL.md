---
name: persistent-self
description: Maintain user-governed personal memory and project continuity across AI-agent sessions. Use for saving, correcting, forgetting, migrating, or recovering durable preferences, decisions, constraints, and unfinished project work from checkpoints.
license: MIT
compatibility: Requires persistent file access and a host instruction entry point. Lifecycle hooks and conversation search are optional.
metadata:
  author: Helia
  version: "3.1.0"
---

# Persistent Self

A file-based continuity protocol. The host supplies storage, tools, instruction loading, and permissions. Installing this skill does not itself activate lifecycle hooks or share conversation history.

## Choose the use case

- **Person-oriented:** preserve user-confirmed preferences, corrections, and cross-project goals in a configured private memory root. The neutral starter is [memory/INDEX.md](memory/INDEX.md).
- **Project-oriented:** recover the decisions, constraints, evidence, and unfinished work needed to continue one project. This mode works independently, without a personal profile or global-memory root.
- **Combined:** use both selectively. Project information remains local; personal context is loaded only when relevant and authorized. A project-specific choice does not automatically become a global preference.

Person-oriented is a use case; global is a storage scope. Scope each item as global, project, workstream, or session-only. Prefer the narrowest useful scope.

## Invariants

- Current user instructions and applicable project contracts govern the work. Memory does not create permission to inspect, write, publish, or share data.
- Distinguish desired behavior from observed behavior. Accepted decisions govern intent; live code, tests, and runtime evidence establish current implementation. A canonical filename does not make every claim in it authoritative or current.
- Keep confirmed statements, observed facts, and candidate interpretations distinguishable. Agent inference never silently becomes a personal fact or accepted project decision.
- Persist only information that will prevent repeated work, preserve a meaningful decision or correction, protect an invariant, or enable continuation. Do not save raw transcripts, hidden reasoning, secrets, or speculative sensitive traits.
- Read before writing; reconcile duplicates and conflicts. Preserve source, scope, and freshness where they change future decisions. Correct or supersede stale active context instead of appending contradictory truth.
- Shared durable project knowledge and workstream-local state have different owners. Do not let the last writer overwrite another workstream's checkpoint.
- The public package contains neutral templates. Real personal memory is private by default; project context follows the project's explicit sharing boundary.

## Load / recover

For personal memory, locate the configured root, read its index, and load relevant modules. Load procedures when applicable; observations remain candidates. Project-only work does not require finding a personal memory root.

For a project, start from the current request, applicable project instructions, and enough live evidence to understand the task. Use `.context/INDEX.md` as a routing map when present. Existing docs and task systems can fulfill the same roles; do not insist on new filenames.

When asked to continue after a gap, locate the matching workstream checkpoint through the index, optional `memory.md`, or existing issue/PR. Recover the target, accepted constraints, current position, evidence, unresolved work, and next checkpoint. Revalidate volatile claims before relying on them. Search for specific missing information, widen only when needed, and stop retrieving once the next action is sufficiently grounded. If an essential gap cannot be recovered, state it precisely instead of inventing history.

Read [PROJECT_CONTEXT.md](references/PROJECT_CONTEXT.md) for checkpoint recovery, source authority, and selective loading; [CONCURRENT_WORK.md](references/CONCURRENT_WORK.md) when several workstreams coexist. Do not preload all references.

## Save / update

Write when explicitly requested, when a durable correction or decision is made within the authorized task, or under a knowingly enabled host/project maintenance policy. A skill invocation does not authorize unrelated memory collection.

Route to an existing authoritative document when one already owns the information. Otherwise use the smallest useful project template:

- accepted purpose and invariants → project docs or `project.md`;
- accepted/rejected/superseded decisions with reasons → decision records or `decisions.md`;
- non-obvious constraints and corrections → `knowledge.md`;
- unfinished work and verification boundaries → the owning issue/PR or workstream checkpoint;
- provenance → source links or `sources.md`;
- potentially useful, non-sensitive inference → clearly marked candidates.

Dates and provenance can be lightweight. Observed implementation facts need evidence and freshness; an observation does not establish user approval. Persist direct user corrections within scope without a mandatory candidate queue. Refresh routing and optional boot digests only when the change affects discovery or continuation.

For global-memory states, promotion, and deletion, read [MEMORY_MODEL.md](references/MEMORY_MODEL.md). Project lessons do not become global merely through repetition; promotion requires an authorized cross-project purpose and must preserve data boundaries.

## Forget / delete

Within the authorized scope, remove the requested information from active entries and derived indexes/digests. Do not keep a hidden archive copy of information the user asked to forget. Distinguish deletion from retiring an outdated item with useful historical value.

Check for known derived copies and report inaccessible stores or version-history retention. Removing a working-tree file does not erase Git history, backups, or other accounts; do not claim complete erasure or rewrite shared history without authority.

## Review / maintain

Review when stale context, repeated retrieval failures, conflicts, or explicit requests justify it. Consolidate duplicates, narrow broad claims, refresh evidence, and retire superseded material with useful provenance. Preserve unresolved disagreements. Age alone does not invalidate a decision. Do not create a diary or maintenance work after every turn.

Use [RETRIEVAL_SCALING.md](references/RETRIEVAL_SCALING.md) when indexes or history become hard to navigate. Improve file organization and targeted search before introducing infrastructure. Reduced reading is useful only if important constraints are still recovered.

## Initialize / migrate

Read [HOST_INTEGRATION.md](references/HOST_INTEGRATION.md) for setup. Choose person-only, project-only, or combined. Inspect existing instructions, docs, and permissions first. Merge the [project instruction snippet](assets/project-context/AGENTS.snippet.md) into the host's recognized project instructions; never replace an existing instruction file wholesale.

Use only the necessary [project assets](assets/project-context/README.md). Preserve existing stores and source provenance; filenames are optional. For the old ten-file project layout read [MIGRATION_V3_TO_V3_1.md](references/MIGRATION_V3_TO_V3_1.md). For v2 personal modules also consult the [v2 migration reference](references/MIGRATION_V2_TO_V3.md).

## Completion evidence

After a change, report the scope and files updated, material candidate/supersession/deletion status, and unresolved limitations. For setup, distinguish template creation, instruction configuration, and observed fresh-session recovery. Do not claim automatic inheritance or token savings without measured evidence. Behavioral acceptance scenarios are in [evals/SCENARIOS.md](evals/SCENARIOS.md).
