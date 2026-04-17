# Harness Creator

<p align="center">
  <img src="assets/icon-large.svg" alt="Harness Creator icon" width="140" />
</p>

`harness-creator` 是一个基于开放 `SKILL.md` 约定的 agent skill，专门用于分析、设计和实现更安全、更可落地的 agent harness。

它并不只服务于 Codex，也可以用于 OpenClaw、Claude Code、Hermes Agent，以及其他能够读取 skill 式 Markdown 指令的主流智能体工具。

它不是泛泛而谈的提示模板，而是把问题收束到真正能落地的几个层次：

- 核心 loop
- tool contract 和 registry
- deny-first 权限系统
- planning / tasks
- subagents
- persistence / memory
- compaction
- integrations
- verification

## 兼容性

这个仓库遵循的是更通用的 `SKILL.md` 技能写法，而不是某一个产品私有的格式。

- 直接适配较好的工具：Codex、OpenClaw、Claude Code、Hermes Agent
- 一般也容易迁移到：支持自定义 skill / instructions / playbook 的其他智能体工具
- 最适合的任务：coding agent runtime、CLI agent、MCP 集成助手、长流程 tool-using agent

## 它的价值

很多 agent 设计最后做不出来，通常有两个原因：

1. 只停留在抽象层，没有变成可实施的系统结构。
2. 一开始就上复杂编排，却没有把 loop、安全、持久化和验证打牢。

这个技能的作用，就是强制把输出变成：

- 可执行的模块边界
- 清晰的实施顺序
- 明确的安全规则
- 事实与推断的区分

## 压测后的实际效果

基于真实 Codex 工作流做对比测试时：

- 不使用 skill：结果更容易停留在澄清问题或宽泛分析。
- 使用 `$harness-creator`：结果会更快收束到具体架构、实现顺序、安全规则和事实/推断边界。

## 更直观的前后差异

### 不使用 skill

- 更容易停在“先不要再细化一下范围”
- 更容易给出泛化的 agent 建议
- 会讲架构，但不一定讲清楚先做什么、后做什么
- 容易把事实和推断建议混在一起

### 使用 `$harness-creator`

- 输出会更稳定地落到分层 runtime 模型
- 模块边界会更清晰
- 实施顺序会更像真正可执行的计划
- 安全和验证不再是附带说明，而是核心组成
- 会更明确地区分“已经确认的内容”和“建议这样设计的内容”

## 为什么这个首页要这样写

很多优秀的开源 skill 首页，都会很快回答这几个问题：

- 这是干什么的
- 用了之后会具体改善什么
- 我现在用的智能体能不能直接装

这个 README 也按这个思路组织，避免读者看到一半还不知道它是不是只能给 Codex 用。

## 案例

### 场景：设计一个生产可用的 CLI coding agent

**baseline**
- 容易停在探索和澄清阶段
- 输出结构不够稳定

**使用 skill 后**
- 会更稳定地产出“当前状态 -> 推荐架构 -> 实施顺序 -> 安全/验证 -> 事实/推断”的结构
- 能更自然地把结果映射回真实代码区域，例如 `QueryEngine`、`query.ts`、`Tool.ts`、task tools、权限层、memory、compaction
- 给出的不只是概念，而是更接近下一步可执行方案

## 安装方式

把仓库克隆到你的技能目录即可。

### 常见技能目录

```text
Codex:       ~/.codex/skills/harness-creator
Claude Code: ~/.claude/skills/harness-creator
OpenClaw:    ~/.openclaw/skills/harness-creator
Hermes:      ~/.hermes/skills/harness-creator
```

如果你的工具使用项目内 skills 目录，也可以直接把本仓库放进去，只要保持 `SKILL.md` 在 skill 根目录即可。

### Windows

```powershell
git clone https://github.com/Arthurescc/harness-creator `
  "$env:USERPROFILE\\.codex\\skills\\harness-creator"
```

### macOS / Linux

```bash
git clone https://github.com/Arthurescc/harness-creator \
  "${HOME}/.codex/skills/harness-creator"
```

如果你希望多个智能体共用同一份 skill，可以把同一个目录再复制或软链接到其他 agent 的 skills 目录。

如果你使用 `CODEX_HOME`，请安装到：

```text
$CODEX_HOME/skills/harness-creator
```

安装后重启或刷新对应智能体，让技能列表刷新。

### Claude Code 安装示例

```bash
git clone https://github.com/Arthurescc/harness-creator \
  "${HOME}/.claude/skills/harness-creator"
```

## 使用示例

- `Use $harness-creator to design a safe CLI coding agent for this repo.`
- `Use $harness-creator to refactor our current agent runtime into a layered harness.`
- `Use $harness-creator to propose how to add planning, subagents, and MCP support without breaking safety.`
- `Use $harness-creator to compare our current design against stronger runtime patterns and identify the gaps.`

## 这个 skill 比泛化 agent 建议更擅长讲清楚什么

- loop 和 orchestration 的边界
- planning、permissions、persistence、compaction 应该在什么时候引入
- 什么应该放在 prompt，什么应该沉到 tools 或 runtime
- 实施顺序怎么排，才能先得到可靠性，再得到自治能力
- 架构分析里哪些是事实，哪些是推荐设计，哪些还属于未知

## 仓库内容

```text
SKILL.md
agents/openai.yaml
references/
assets/
```

## 开发说明

本项目为自研开发，面向多种 coding agent 的实际技能工作流使用。

重点在于提升 agent harness 设计任务中的输出质量、可实施性和安全性。

## 更多技能

总导航页见：[codex-skills-hub](https://github.com/Arthurescc/codex-skills-hub)

## FAQ

### 这是只给 Codex 用的吗？

不是。它采用开放的 `SKILL.md` 结构，目标就是尽量在 Codex、OpenClaw、Claude Code、Hermes Agent 这类工具之间复用。

### 不同智能体要分别改写一份吗？

多数情况下不用。通常只需要调整安装位置；核心技能内容本身是可复用的。

### 适合谁用？

适合以下场景：

- 做 coding agent
- 做 CLI assistant
- 做 tool-calling runtime
- 做多步骤 agent backend
- 做 MCP-aware orchestration system

## License

MIT
