# Host Integration

Persistent Self is a protocol layered on top of an agent host. The host supplies storage and lifecycle behavior.

## Required capability

The minimum useful host can:

- persistently read and write files;
- load project or user instructions;
- read Markdown files on demand.

Useful optional capabilities:

- session-start / session-end hooks;
- current time;
- conversation-history search;
- permissioned background writes.

The skill must adapt to available capabilities instead of assuming a specific tool name.

## Installing the skill

Install the whole `persistent-self/` directory when possible so `references/` and `assets/` remain available through relative paths.

Common locations include:

```text
~/.agents/skills/persistent-self/
~/.codex/skills/persistent-self/
~/.claude/skills/persistent-self/
~/.cursor/skills/persistent-self/
~/.codeium/windsurf/skills/persistent-self/
~/.hermes/skills/persistent-self/
```

Several clients also support project-level skill roots.

## BotLearn / SkillHunt

BotLearn can distribute the same skill package through SkillHunt. Persistent Self v3 keeps `SKILL.md` on the portable Agent Skills frontmatter model rather than adding BotLearn-specific taxonomy fields at the top level.

When publishing or updating the skill on BotLearn, provide the platform's current facets (`categories`, `roles`, `outputs`, `scenarios`, `runtimes`, `platforms`) through the BotLearn publishing CLI or Web form. This keeps the repository portable while preserving SkillHunt discoverability.

Current installation command:

```text
botlearn install persistent-self
```

`skillhunt` may remain available as an alias depending on the installed BotLearn SDK version.

## Global-memory storage

Do not use the public skill directory itself as a live personal memory store.

Choose a private persistent location, for example:

```text
~/.persistent-self/memory/
```

or a host-specific private workspace.

Then add a small host-level protocol that tells the agent:

```text
Persistent memory root: [path]
At session start, read INDEX.md and procedures.md.
Load other modules only when relevant.
Use the persistent-self skill for memory writes, review, migration, and deletion.
```

The host-level instruction should define the protocol and location. Personal memory content stays in the memory store itself.

## Project context

Project context stays with the project:

```text
project/
├── memory.md
└── .context/
```

The project instruction file can point the agent to this context. Exact filenames differ by host (`AGENTS.md`, `CLAUDE.md`, project rules, etc.).

Do not duplicate the full project context inside a global host instruction file.

## Session start

With lifecycle hooks:

1. locate global memory;
2. read `INDEX.md` + `procedures.md`;
3. if inside a project, read project `memory.md`;
4. selectively load additional global/project context.

Without hooks, perform the same load when the user asks to continue prior work or when the host starts a new task with project instructions.

## During a session

Do not write memory after every turn. Durable writes should follow explicit save requests, durable corrections/decisions, or a host policy the user has knowingly enabled.

## Session end

A session-end hook may update memory, but it must still follow the v3 evidence and scope rules. Session-end automation does not grant permission to promote agent inference to confirmed memory.

## Conversation search

Conversation search is an optional provenance/recovery layer. It is useful when:

- a memory entry references a past discussion whose exact wording matters;
- file memory is incomplete;
- migration needs to recover a previous decision.

If the host lacks conversation search, continue with file memory and state the retrieval limitation when it matters.

## Git and privacy

Global personal memory normally should not be committed to a public repository.

Project context may be committed when the project boundary allows it. For public projects, review `.context/03_user_model.md`, private source notes, internal decisions, and other sensitive files before committing. Use `.gitignore` or a private context store when needed.
