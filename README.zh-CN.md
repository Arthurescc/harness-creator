# Harness Creator

<p align="center">
  <img src="assets/icon-large.svg" alt="Harness Creator icon" width="140" />
</p>

`harness-creator` 是一个面向 Codex 的技能，专门用于分析、设计和实现更安全、更可落地的 agent harness。

它不是泛泛而谈的 “AI agent 提示词模板”，而是把问题收束到真正能落地的几个层次：

- 核心 loop
- tool contract 和 registry
- deny-first 权限系统
- planning / tasks
- subagents
- persistence / memory
- compaction
- integrations
- verification

## 它的价值

很多 agent 设计最后做不出来，通常有两个原因：

1. 只停留在抽象层，没有变成可实施的系统结构。
2. 一开始就上 swarm / autonomy，却没有把 loop、安全、持久化和验证打牢。

这个技能的作用，就是强制把输出变成：

- 可执行的模块边界
- 清晰的实施顺序
- 明确的安全规则
- 源码事实与推断的区分

## 压测后的实际效果

基于公开 Claude Code 重建源码做对比测试时：

- 不使用 skill：结果更容易停留在澄清问题或宽泛分析。
- 使用 `$harness-creator`：结果会明显更快收束到具体架构、实现顺序、安全规则和事实/推断边界。

## 安装方式

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

如果你使用 `CODEX_HOME`，请安装到：

```text
$CODEX_HOME/skills/harness-creator
```

安装后重启 Codex，让技能列表刷新。

## 使用示例

- `Use $harness-creator to design a safe CLI coding agent for this repo.`
- `Use $harness-creator to refactor our current agent runtime into a layered harness.`
- `Use $harness-creator to compare our design with Claude Code-style patterns.`

## 仓库内容

```text
SKILL.md
agents/openai.yaml
references/
assets/
```

## 来源说明

本技能参考了公开的非官方分析项目：

- [oboard/claude-code-rev](https://github.com/oboard/claude-code-rev)
- [sanbuphy/claude-code-source-code](https://github.com/sanbuphy/claude-code-source-code)

与 Anthropic 无官方关联。

## License

MIT
