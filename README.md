# Harness Creator

<p align="center">
  <img src="assets/icon-large.svg" alt="Harness Creator icon" width="140" />
</p>

<p align="center">
  Design safer, sharper, production-ready agent harnesses instead of vague "AI agent" architectures.
</p>

## What It Is

`harness-creator` is an open `SKILL.md`-based agent skill for analyzing, designing, and implementing production-ready agent harnesses.

It is written to work well in Codex, OpenClaw, Claude Code, Hermes Agent, and other coding agents that can read skill-style Markdown instructions. The repo is not limited to Codex-only workflows.

It helps you turn a fuzzy request like:

- "Build a coding agent"
- "Make this production-grade"
- "Add subagents and planning"
- "Design an MCP-enabled agent runtime"

into a concrete, layered runtime design with clear module boundaries, implementation order, and safety rules.

## Compatibility

This repository follows the portable `SKILL.md` pattern used across the broader agent-skills ecosystem.

- Works well in: Codex, OpenClaw, Claude Code, Hermes Agent
- Usually portable to: other agents that ingest Markdown skill instructions
- Best fit: coding-agent runtimes, CLI agents, MCP-enabled assistants, orchestrators, and multi-step tool-using systems

If your agent supports custom skills, custom instructions, or task-scoped Markdown playbooks, this repo can usually be used directly or with light adaptation.

## Why People Install It

Most agent designs fail in one of two ways:

1. They stay too abstract and never become a buildable system.
2. They jump straight to orchestration and autonomy before the core loop, tool safety, persistence, and verification are dependable.

`harness-creator` fixes that by forcing the conversation toward:

- a real loop/tool/permissions/memory/orchestration model
- a progressive build order
- clear distinctions between facts, recommendations, and unknowns
- explicit safety and verification requirements

## What You Get

- A progressive architecture ladder for agent runtimes.
- A concrete implementation order from minimal loop to advanced orchestration.
- A required output contract so analysis does not stop at "I inspected these files."
- Strong guidance for permissions, planning, subagents, memory, compaction, integrations, and verification.

## What Makes This Repo Useful

Good skill homepages usually answer three questions fast: what problem it solves, what changes after installing it, and whether it fits your stack.

This repo is optimized around those three:

- it targets a specific high-value problem: agent harness architecture
- it shows behavioral difference before vs after the skill
- it is packaged as a reusable instruction asset, not a Codex-only experiment

## Skill Highlights

- `Loop first, orchestration last`: prevents premature swarm complexity.
- `Deny-first safety`: pushes shell/file/network safety into the harness, not just prompts.
- `Facts vs inference`: helps you avoid overclaiming when incomplete information exists.
- `Implementation-ready`: produces module boundaries and execution order, not just principles.

## Validated Value

This skill was pressure-tested in practical Codex workflow scenarios.

Observed difference:

| Scenario | Without the skill | With the skill |
| --- | --- | --- |
| Analyze a production-grade coding-agent runtime | The agent tended to slow down into clarification or open-ended exploration. | The agent produced a concrete architecture, implementation order, safety rules, and a clear facts-vs-inference split. |

That makes the skill useful not just as documentation, but as a behavior-shaping tool.

## Before / After

### Without `harness-creator`

- analysis often stops at repo reconnaissance
- answers drift into broad "AI agent" advice
- architecture is described, but implementation order is weak
- facts and inferred recommendations get mixed together

### With `harness-creator`

- the answer is pushed toward a layered runtime model
- module boundaries become explicit
- implementation order becomes actionable
- safety and verification are treated as first-class, not optional polish
- known facts are separated from inferred design choices

## Case Study

### Case: "Design a production-ready CLI coding agent"

**Baseline behavior**
- slowed down into clarification or exploratory framing
- did not always converge on a strong output contract

**With the skill**
- produced a concrete "current state -> architecture -> implementation order -> safety/verification -> facts vs inference" structure
- mapped the answer back to real runtime layers such as loop, tool contract, task tooling, permissions, memory, and compaction
- gave a practical build ladder instead of only conceptual guidance

### Why that matters

That difference saves time in real engineering work. Instead of spending another round asking "so what should we build first?", the team can move directly into implementation planning.

## Quick Start

Clone or copy this repository into your agent skills directory.

### Common skill locations

```text
Codex:       ~/.codex/skills/harness-creator
Claude Code: ~/.claude/skills/harness-creator
OpenClaw:    ~/.openclaw/skills/harness-creator
Hermes:      ~/.hermes/skills/harness-creator
```

If your tool uses a project-local skills folder instead of a global one, copy this repo into that location and keep the `SKILL.md` at the skill root.

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

Then copy or symlink the same folder into any other agent's skills directory if you want to share one canonical version across tools.

If you use `CODEX_HOME`, install it under:

```text
$CODEX_HOME/skills/harness-creator
```

Restart or refresh your agent after installation so the skill list reloads.

### Windows example for Claude Code

```powershell
git clone https://github.com/Arthurescc/harness-creator `
  "$env:USERPROFILE\\.claude\\skills\\harness-creator"
```

### macOS / Linux example for Claude Code

```bash
git clone https://github.com/Arthurescc/harness-creator \
  "${HOME}/.claude/skills/harness-creator"
```

## Example Prompts

- `Use $harness-creator to design a safe CLI coding agent for this repo.`
- `Use $harness-creator to refactor our current agent runtime into a layered harness.`
- `Use $harness-creator to propose how to add planning, subagents, and MCP support without breaking safety.`
- `Use $harness-creator to compare our current design against stronger runtime patterns and identify the gaps.`

## What This Skill Clarifies Better Than Generic Agent Advice

- where the loop ends and orchestration begins
- when to add planning, permissions, persistence, and compaction
- what should live in prompts vs tools vs runtime code
- how to sequence implementation so reliability arrives before autonomy
- how to separate facts, recommendations, and unknowns in architecture work

## Repository Layout

```text
SKILL.md
agents/openai.yaml
references/
  harness-workflow.md
  source-backed-patterns.md
assets/
  icon-small.svg
  icon-large.svg
```

## Development

This project is independently designed and developed for practical agent-skill workflows across multiple coding agents.

It is focused on improving output quality, implementation readiness, and safety discipline for agent-harness design work.

## Professional Use Cases

- internal engineering assistants
- coding-agent platform architecture
- CLI-based developer tools
- subagent and verifier orchestration
- MCP-enabled productivity runtimes
- research-to-implementation agent workflows

## Chinese Documentation

See [README.zh-CN.md](README.zh-CN.md).

## More Skills

Browse the collection page: [codex-skills-hub](https://github.com/Arthurescc/codex-skills-hub)

## FAQ

### Is this only for Codex?

No. It is authored in the open `SKILL.md` format and is meant to be portable across Codex, OpenClaw, Claude Code, Hermes Agent, and similar tools.

### Do I need to change the skill for each agent?

Usually no. In many tools the same `SKILL.md` works directly. Some ecosystems may prefer a different install path or wrapper format, but the core instructions remain reusable.

### Who should use this?

Use it when you are building or refactoring:

- coding agents
- CLI assistants
- tool-calling runtimes
- multi-step agent backends
- MCP-aware orchestration systems

## License

MIT
