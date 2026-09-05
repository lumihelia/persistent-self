# Persistent Self

中文 · [English](./README.en.md)

一个面向 AI Agent 的**文件型长期记忆治理 Skill**。它负责决定什么值得跨会话保存、保存到哪里、哪些内容已经确认、哪些仍只是候选观察，以及旧信息如何被新信息覆盖。

**当前版本：** `3.0.0`

Persistent Self 本身不提供数据库、向量检索或云端记忆服务。真正的持久化由宿主 Agent 的文件系统完成；这个仓库提供协议、可复用 Skill、中性 global-memory scaffold，以及项目级 context scaffold。

## v3 的核心变化

v3 把长期记忆拆成几个彼此独立的判断层：

```text
当前对话
   ↓
判断是否值得持久化
   ↓
┌───────────────────────┬────────────────────────┐
│ Global memory         │ Project context        │
│ 跨项目长期成立         │ 只在当前项目中成立       │
└───────────────────────┴────────────────────────┘
   ↓                            ↓
confirmed / candidate       memory.md + .context/
```

四条边界贯穿整个版本：

1. **Global memory 与 project context 分开。** 项目状态不会因为出现过很多次就自动变成全局身份信息。
2. **Confirmed 与 candidate 分开。** Agent 的观察可以被保存为候选，不能静默升级成事实。
3. **Boot digest 与 canonical source 分开。** 启动摘要负责快速进入状态，精确信息回到 canonical context 核对。
4. **Protocol 与 instance data 分开。** 公开仓库只提供中性协议与模板，真实个人记忆默认留在私有、本地实例。

这套结构延续了 v2 的 selective loading 思路，同时把后续真实使用中暴露出的 provenance、scope、supersession 和 privacy 问题纳入协议。

## 两种记忆范围

### Global memory

`memory/` 是跨项目长期记忆的中性 starter scaffold：

```text
memory/
├── INDEX.md          # 轻量入口与模块摘要
├── procedures.md     # 已确认的持久行为规则
├── profile.md        # 已确认、长期稳定的事实与偏好
├── priorities.md     # 当前跨项目优先级
├── threads.md        # 跨项目长期问题或工作线
├── observations.md   # 尚未确认的候选观察
└── archive.md        # 已结束或被覆盖、但仍值得留档的历史
```

`observations.md` 不属于可信 profile。只有确认过的内容才进入正式模块并长期影响后续行为。

### Project context

项目自己的状态采用：

```text
memory.md        # boot digest，只负责快速启动
.context/        # canonical authority
```

可复制模板位于 [`assets/project-context/`](./assets/project-context/)。其中包含 project brief、current state、decision log、project-specific user model、agent roles、handoff、open questions、rejected ideas、next actions 与 source index。

这套结构适合需要多个 Agent、多轮迭代或长时间维护的项目。较小项目可以只保留真正会用到的模块。

## 记忆状态

Persistent Self v3 使用四个状态：

- `confirmed`：用户明确说过、确认过，或由项目 canonical artifact 直接支持；
- `candidate`：Agent 观察或推断，等待确认；
- `superseded`：已被更新信息覆盖；
- `archived`：不再活跃，但仍值得作为历史保留。

新信息与旧信息冲突时，新的 confirmed memory 应覆盖旧的 active truth。历史记录可以留下，两个互相矛盾的说法不能同时作为当前事实存在。

## Provenance

长期记忆需要知道“这条信息为什么会在这里”。最小可用格式即可：

```markdown
- 2026-09-05 · confirmed · user-correction — Prefer concise completion reports.
```

候选观察：

```markdown
- 2026-09-05 · candidate · agent-observation · medium — May prefer async review over live coordination. Needs confirmation.
```

不需要把 Markdown 变成数据库 schema。状态、来源与日期足以支持大多数人工审计和后续修正。

## 写入规则

Persistent Self 不再依赖“对话超过多少轮就保存”或固定时间衰减表。写入由信息性质决定：

- 用户明确要求保存的内容可以进入 durable memory；
- 明确纠正和持久决策可以直接成为 confirmed；
- Agent 自己推出来的偏好、人格模式或解释只能成为 candidate；
- 项目状态默认写入 project context；
- 临时情绪、一次性任务细节和弱证据内容留在当前 session；
- 写入前先读目标模块，检查重复、冲突与旧版本；
- 新的 confirmed truth 覆盖旧 truth；
- 用户要求 `forget` / `delete` 时，真正删除相关 active memory 与派生摘要，不在隐藏 archive 中偷偷保留一份。

完整规则见 [`SKILL.md`](./SKILL.md) 与 [`references/MEMORY_MODEL.md`](./references/MEMORY_MODEL.md)。

## 安装

Persistent Self 遵循当前 [Agent Skills](https://agentskills.io/) 结构：一个 skill 目录内包含 `SKILL.md`，更长的机制说明与模板按需放在 `references/`、`assets/` 等目录。

推荐把**整个仓库内容**作为 `persistent-self/` skill 目录安装，这样 Skill 可以按需读取 references 与 project-context assets。

常见宿主位置包括：

```text
# 通用 / 部分兼容宿主
~/.agents/skills/persistent-self/

# Codex
~/.codex/skills/persistent-self/

# Claude Code
~/.claude/skills/persistent-self/

# Cursor
~/.cursor/skills/persistent-self/

# Windsurf
~/.codeium/windsurf/skills/persistent-self/

# Hermes
~/.hermes/skills/persistent-self/
```

项目级 Skill 也可以放在宿主支持的 project skill root。具体路径与生命周期接入见 [`references/HOST_INTEGRATION.md`](./references/HOST_INTEGRATION.md)。

## 初始化 global memory

将 `memory/` scaffold 复制到**私有、可持久写入**的位置，再在宿主的长期指令中告诉 Agent 这个 memory root 在哪里。

会话开始时通常只需要：

1. 读 `INDEX.md`；
2. 读 `procedures.md`；
3. 根据当前任务选择其他模块。

不要把整个 memory 目录无差别注入每次请求。

## 初始化项目 context

复制：

```text
assets/project-context/memory.md
assets/project-context/.context/
```

到项目根目录。

`memory.md` 只保留能帮助 Agent 快速进入状态的短摘要。精确决策、当前状态、来源和历史由 `.context/` 维护。

公开仓库中的项目 context 需要额外检查隐私。项目特定的用户信息、内部决策或私有来源不适合因为使用了这个 scaffold 就自动公开。

## 从 v2 迁移

v2 的 `identity / salience / themes / discussions / patterns / growth` 等模块在 v3 中不再作为默认结构。

迁移时：

- `procedures.md` → 保留已确认规则；
- `identity.md` → 只把明确确认、长期成立的内容迁入 `profile.md`；
- `salience.md` → 跨项目优先级迁入 `priorities.md`；
- `threads.md` → 跨项目内容留在 global，项目内容进入 `.context/`；
- `patterns.md` / `growth.md` → 默认作为 candidate 重新审查，不直接当作用户事实；
- `discussions.md` → 只有仍有长期作用的结论进入正式 memory，其余作为历史或 source provenance 处理。

**已有 `memory.md`、memory 目录或 `.context/` 不直接覆盖。** 先保留原件，再迁移，验证完成后才处理旧结构。

完整映射见 [`references/MIGRATION_V2_TO_V3.md`](./references/MIGRATION_V2_TO_V3.md)。

## 当前边界

- 没有自己的数据库、向量存储或后台服务；
- 没有统一的自动 session-start hook；
- conversation search 是可选增强能力，不再假设存在名为 `session_search` 的工具；
- global memory 的真实实例应当保持私有；
- project context 是否进入 Git，取决于项目的公开边界；
- selective loading 是设计原则，具体上下文成本由宿主、模型和实际内容决定。

## 仓库结构

```text
SKILL.md                     # v3 执行协议
memory/                      # 中性 global-memory scaffold
references/
  MEMORY_MODEL.md            # scope / state / provenance / privacy
  HOST_INTEGRATION.md        # 宿主接入方式
  MIGRATION_V2_TO_V3.md      # v2 → v3 迁移
assets/project-context/      # copyable project memory.md + .context/
README.md                    # 中文说明
README.en.md                 # English overview
LICENSE                      # MIT
```

## 设计来源

Persistent Self 最初受 [soul.py](https://github.com/menonpg/soul.py) 的模块化记忆思路启发。v3 又吸收了后续真实 Agent 协作中的实践：轻量 boot digest、canonical project context、confirmed/candidate 分层、可追溯更新，以及全局与项目作用域的分离。

## License

[MIT](LICENSE)
