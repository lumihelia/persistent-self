# Project assets

These are neutral templates. Copy/adapt the smallest useful subset after inspecting the target project. Never overwrite existing instructions or populated memory.

## Minimal setup

- Merge [AGENTS.snippet.md](AGENTS.snippet.md) into recognized project instructions.
- Adapt [.context/INDEX.md](.context/INDEX.md) into a router to actual existing docs, decisions, and workstream checkpoints. If the existing docs already route well, use them instead.

Do not copy this directory wholesale into a project. The root package AGENTS.md is for package contributors, not project users.

## Optional additions

| Template | Possible destination | Add when |
| --- | --- | --- |
| [memory.md](memory.md) | project root `memory.md` | A short boot cache materially improves resumption |
| [project.md](optional/project.md) | `.context/project.md` | Accepted project meaning has no adequate existing home |
| [decisions.md](optional/decisions.md) | `.context/decisions.md` | Important decision rationale is otherwise lost |
| [knowledge.md](optional/knowledge.md) | `.context/knowledge.md` | Non-obvious constraints/corrections need a home |
| [sources.md](optional/sources.md) | `.context/sources.md` | Evidence needs a discoverable provenance map |
| [checkpoint.md](optional/checkpoint.md) | `.context/state.md` or `.context/state/<workstream>.md` | Existing issues/PRs do not preserve continuation state |

Use established project names and docs when available. Do not create empty modules or list uncreated files in the index. Unknown fields stay explicitly unknown; example prompts are not evidence.

Keep candidates explicitly marked in the relevant knowledge record, or add a candidate directory if volume justifies it. Historical records stay off the default read path. A public template does not make populated context safe to publish.
