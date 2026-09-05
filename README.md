# Persistent Self

中文 · [English](./README.en.md)

一个面向 AI Agent 的模块化长期记忆 Skill。通过持久文件保存跨会话信息，再用轻量索引与按需加载控制上下文成本。

**当前 Skill 版本：** `2.0.0`

Persistent Self 最初面向 Hermes 设计，结构受 [soul.py](https://github.com/menonpg/soul.py) 的 Modulizer pattern 启发。当前仓库本身不运行服务，也不包含数据库或向量检索层；真正的记忆读写由宿主 Agent 完成。

## 核心结构

记忆被拆成一个轻量索引和多个职责不同的模块：

```text
memory/
├── INDEX.md         # 模块索引；会话开始时优先读取
├── procedures.md    # 持久行为规则与纠正
├── salience.md      # 当前优先级
├── identity.md      # 持续更新的用户模型
├── themes.md        # 反复出现的主题
├── threads.md       # 尚未结束的问题与项目
├── discussions.md   # 值得保留的历史讨论
├── patterns.md      # 长期观察到的模式
└── growth.md        # 随时间发生的变化与校准
```

加载过程分成两层：

1. 会话开始时读取 `INDEX.md` 与 `procedures.md`。
2. 根据当前上下文选择其他相关模块。

`SKILL.md` 还定义了 session gap、不同模块的 decay 节奏、过期条目的归档候选，以及会话结束时的更新流程。

这里追求的是 **selective loading**：跨会话信息留在磁盘上，进入当前上下文的只是一小部分相关内容。

## 宿主要求

Persistent Self 不是所有聊天模型都能直接运行的 prompt。完整工作流需要宿主具备以下能力：

- 持久文件读写；
- 在新会话开始时加载项目级指令；
- 按需读取 Markdown 模块；
- 获取当前日期或时间；
- 在需要时把本轮内容写回 memory 文件。

当前 `SKILL.md` 还引用了 `session_search`。没有等价会话检索能力的宿主仍可使用文件记忆部分，历史对话检索步骤需要按宿主能力调整。

## 安装 Skill

`SKILL.md` 可以作为 Agent Skill 安装。推荐把整个仓库或至少 `SKILL.md` 放进名为 `persistent-self` 的 skill 目录。

### Claude Code

项目级：

```text
.claude/skills/persistent-self/SKILL.md
```

个人级：

```text
~/.claude/skills/persistent-self/SKILL.md
```

### Codex

默认个人目录：

```text
~/.codex/skills/persistent-self/SKILL.md
```

使用自定义 `CODEX_HOME` 时，对应位置为：

```text
$CODEX_HOME/skills/persistent-self/SKILL.md
```

### Cursor

项目级可使用：

```text
.cursor/skills/persistent-self/SKILL.md
.agents/skills/persistent-self/SKILL.md
```

Cursor 也会发现兼容的 Claude Code 与 Codex skill 目录。

### Windsurf

项目级：

```text
.windsurf/skills/persistent-self/SKILL.md
```

跨 Agent 项目目录：

```text
.agents/skills/persistent-self/SKILL.md
```

个人级：

```text
~/.codeium/windsurf/skills/persistent-self/SKILL.md
```

### Hermes

Hermes 当前的本地 skill 目录为：

```text
~/.hermes/skills/persistent-self/SKILL.md
```

Hermes 本身已经提供独立的 memory 与 skills 系统。Persistent Self 因此更适合作为一种可替换、可研究的模块化文件记忆架构，而不是 Hermes 运行所必需的组件。

### BotLearn SkillHunt

BotLearn 当前使用 `install` 安装 Skill，`skillhunt` 仍是别名：

```text
botlearn install persistent-self
```

## 初始化 memory 目录

安装 Skill 只提供工作流规则。跨会话记忆还需要一个持久存在的 `memory/` 目录。

将仓库中的 `memory/` 复制到 Agent 的长期工作目录，再在宿主的**项目级、持续加载的指令文件**中加入类似规则：

```text
At the start of each session, read memory/INDEX.md and memory/procedures.md.
Load other memory modules only when they are relevant to the current session.
Keep memory integration implicit unless the conversation asks about it.
```

不同宿主使用不同的持续上下文文件。例如 Hermes 更适合使用项目级 `HERMES.md` / `.hermes.md` 或 `AGENTS.md`；`SOUL.md` 当前主要负责 Hermes 实例的全局人格与语气。

## 关于仓库中的 memory scaffold

`memory/` 起初来自作者自己的实验环境。大部分模块仍是未初始化模板，少数文件保留了作者侧的 seed 内容与项目示例。

把这套目录用于新的 Agent 前，建议先检查并中性化已有内容，再建立对应的 identity、procedures、salience 与 active threads。当前 `INDEX.md` 仍以未初始化状态为主，不能被视为一套已经完成的通用示例数据。

## 当前边界

- 没有后台服务、数据库或向量存储；
- 没有自动注入机制，加载行为依赖宿主的项目指令；
- 没有统一的跨宿主 `session_search` 实现；
- memory 文件会真实保存用户相关信息，公开仓库与公开同步目录不适合直接存放私人实例数据；
- 两阶段加载与 `≤ 3KB` 目标来自当前 Skill 的设计约束，不代表所有宿主上的实测性能保证。

## 仓库结构

- `SKILL.md` — Persistent Self v2.0.0 的执行规则与元数据
- `memory/` — 模块化 memory scaffold
- `README.md` — 中文仓库说明
- `README.en.md` — 英文仓库说明

`SKILL.md` 决定当前执行行为；README 负责说明仓库如何使用、宿主能力与已知边界。

## 参考

- [soul.py](https://github.com/menonpg/soul.py)
- [Persistent Identity in AI Agents: A Multi-Anchor Architecture for Resilient Memory and Continuity](https://arxiv.org/abs/2604.09588)

## License

[MIT](LICENSE)
