# Harness Creator

<p align="center">
  <img src="assets/icon-large.svg" alt="Harness Creator icon" width="140" />
</p>

<p align="center">
  Design safer, sharper, production-ready agent harnesses instead of vague "AI agent" architectures.
</p>

## What It Is

`harness-creator` is a Codex skill for analyzing, designing, and implementing production-ready agent harnesses.

It helps you turn a fuzzy request like:

- "Build a coding agent"
- "Make this production-grade"
- "Add subagents and planning"
- "Design an MCP-enabled agent runtime"

into a concrete, layered runtime design with clear module boundaries, implementation order, and safety rules.

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

Clone or copy this repository into your Codex skills directory:

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

If you use `CODEX_HOME`, install it under:

```text
$CODEX_HOME/skills/harness-creator
```

Restart Codex after installation so the skill list refreshes.

## Example Prompts

- `Use $harness-creator to design a safe CLI coding agent for this repo.`
- `Use $harness-creator to refactor our current agent runtime into a layered harness.`
- `Use $harness-creator to propose how to add planning, subagents, and MCP support without breaking safety.`
- `Use $harness-creator to compare our current design against stronger runtime patterns and identify the gaps.`

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

This project is independently designed and developed for practical Codex skill workflows.

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

## License

MIT
