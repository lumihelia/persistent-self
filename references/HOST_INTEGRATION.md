# Host integration

The host supplies persistent file access, instruction loading, and permissions. Optional hooks and conversation search can help but are not required by the protocol. Verify a host's current supported instruction/skill locations before installing; the package does not implement a universal loader.

## Choose scope first

| Mode | Setup | Default loading |
| --- | --- | --- |
| Person-only | Neutral `memory/` copied to a private persistent root; host instructions point there | Index, applicable procedures, relevant modules |
| Project-only | Project instruction snippet plus routing to project sources | Task-relevant project context; no personal memory dependency |
| Combined | Both independently configured | Relevant authorized sources from each scope |

Install the whole package in a skill location supported by the host so relative references remain available. Keep real instance data outside this public package and outside directories that package upgrades may replace. Project-local use does not require global installation.

## Distribution integrations

When distributing through BotLearn / SkillHunt or another catalog, keep distributor-specific facets and publishing metadata in that platform's supported publishing interface. Preserve the portable Skill frontmatter. Verify current distributor commands separately; protocol maintenance does not authorize publishing a catalog release.

## Personal memory

Choose a private root, such as `~/.persistent-self/memory/`, and copy only needed neutral modules after checking existing files. This is an example location, not an automatic search target.

A host instruction can say:

```text
Personal memory root: [configured private path].
Read its INDEX when personal continuity is relevant, and applicable procedures.
Load other modules only for the current task within authorized scope.
Use persistent-self for authorized saves, corrections, review, and deletion.
```

Do not place the user's real memory in the skill's neutral `memory/` scaffold.

## Project setup

Inspect existing instructions, project docs, task tracking, context stores, and sharing rules. Merge [AGENTS.snippet.md](../assets/project-context/AGENTS.snippet.md) into the host-recognized project instruction file or equivalent project rules. The package's root AGENTS.md governs contributors and must not be copied as the user's project contract.

Create an index only if it improves discovery, using [the asset guide](../assets/project-context/README.md). Populate it with actual sources. The supplied `.context/` directory mirrors the target layout and includes all reference roles. For new projects it can be adopted as a scaffold; for existing projects merge only needed roles without overwriting populated files. An optional boot digest can help discovery. Empty templates remain explicitly uninitialized and are not evidence. Existing filenames and docs are valid; avoid a second canonical copy.

The snippet authorizes scoped project maintenance only if the project owner adopts it and the host permits it. It does not grant public publishing, deployments, personal-memory ingestion, or access to another account.

## Session lifecycle

At task start, recognized project instructions should expose the context route. For continuation, find the matching checkpoint and revalidate relevant live state. During work, persist load-bearing state at meaningful boundaries under the adopted policy. Avoid writes after every turn and avoid relying solely on an end hook that may not run after interruption.

Without hooks, use the host's normal project instruction mechanism or explicitly invoke the skill with the project and continuation request. If the host cannot load project rules automatically, each new session needs an explicit entry point. State that limitation; copying a file alone does not solve it.

Conversation search is optional and subject to existing data authority. Never assume a particular search tool name or access to another session/account's history.

## Installation acceptance

Distinguish these claims:

1. **Created:** intended templates/instructions exist without overwriting user work.
2. **Configured:** the chosen host is configured to discover the project instructions and sources.
3. **Behavior observed:** a fresh session, given the project and current task without the old chat, actually found the appropriate checkpoint and resumed correctly.

Record which was verified. The third requires observing a real session; file inspection proves only the first two to the extent inspected. The [scenarios](../evals/SCENARIOS.md) support this check. Do not publish token-saving percentages without actual measurements.

## Git, sharing, and deletion

Project context can be committed only within its sharing boundary. Keep approved shared project knowledge separate from private state. `.gitignore` does not remove already tracked content or history. Public indexes must not reveal private source metadata. Do not commit personal profiles, credentials, raw conversations, or private runtime paths.

Deletion of active memory does not erase all backups or Git history. Report retained/inaccessible copies and obtain appropriate authority for broader deletion. Cross-account continuity needs shared authorized sources, not assumptions about another account's memory.
