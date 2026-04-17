---
name: harness-creator
description: Design and implement safe, layered agent harnesses and orchestration workflows. Portable across Codex, OpenClaw, Claude Code, Hermes Agent, and similar SKILL.md-compatible coding agents. Use when the user wants a coding agent, CLI agent, tool-calling assistant, multi-agent runtime, plan/task system, subagent framework, MCP-integrated harness, or Claude Code/Codex-style agent workflow built or refactored.
---

# Harness Creator

## Overview

Build agent harnesses in progressive layers instead of jumping straight to a large autonomous system. Treat the harness as a runtime with five first-class concerns: loop, tools, safety, memory, and orchestration.

Read `references/harness-workflow.md` for the full build sequence. Read `references/source-backed-patterns.md` when the user wants Claude Code-like behavior or asks for rationale grounded in reverse-engineered source.

## Workflow

1. Classify the requested harness.
   - Determine whether the target is a single-agent loop, safe coding agent, persistent orchestrator, or multi-agent team.
   - Identify environment: CLI, editor/desktop, web backend, or hybrid.
   - Identify the highest-risk operations: shell, file edits, network, git, browser, deployment, secrets.

2. Choose the smallest viable maturity tier.
   - Tier 1: basic loop + tool registry + transcript.
   - Tier 2: add permission gates, plan/tasks, and verification.
   - Tier 3: add memory, persistence, compaction, and background work.
   - Tier 4: add subagents, teams, MCP resources, and worktree isolation.
   - Do not start at Tier 4 unless the user explicitly needs delegation or persistent orchestration.

3. Sketch the module boundaries before editing code.
   - `core/query-engine`: model loop, streaming, turn lifecycle, retries.
   - `tools`: tool contract, registry, execution wrapper, normalization.
   - `permissions`: allow/ask/deny rules, mode handling, safety checks.
   - `planning`: plan mode, todos/tasks, task dependencies, approval gates.
   - `agents`: spawn/resume/wait/message, context isolation, optional worktrees.
   - `memory`: persistent memories, session history, scoped recall.
   - `compact`: token budgeting, summarization, context trimming.
   - `integrations`: MCP, external resources, auth, transport adapters.
   - `persistence`: transcripts, resumability, background state, run metadata.

4. Implement in production order.
   - Build the minimal assistant loop first.
   - Add tool execution and normalization second.
   - Add deny-first permissions before broadening tool power.
   - Add planning/tasks before adding subagents.
   - Add persistence and compaction before long-running autonomy.
   - Add teams/worktrees only when single-agent execution is already reliable.

5. Verify after each layer.
   - Loop: one ask, one tool call, one final answer.
   - Safety: confirm deny rules win over allow rules.
   - Planning: confirm non-trivial tasks create and update plan/tasks.
   - Subagents: confirm isolated context and bounded write scopes.
   - Persistence: confirm resume restores enough state to continue.
   - Compaction: confirm the harness sheds context without losing task intent.

6. Shape the response for analysis requests.
   - Default to a best-effort concrete answer instead of optional clarification.
   - Ask a clarifying question only when a missing decision would materially change the architecture or create hidden risk.
   - Separate proven source-backed facts, recommended implementation choices, and unknown or internal-only areas.

## Operating Rules

- Prefer source-backed patterns over speculative architecture.
- Re-implement public patterns; do not attempt to clone proprietary internal-only modules.
- Keep agent prompts small and move detailed guidance into tools, references, and persisted state.
- Treat subagents as isolated workers with explicit ownership, not invisible copies of the parent.
- Put verification on the critical path, not in the final summary.
- When adapting an existing codebase, fit the harness to existing conventions instead of forcing a brand-new framework shape.
- Do not stop at repo reconnaissance. Turn findings into a concrete architecture, implementation order, and risk list.

## Output Contract

Unless the user asks for something narrower, include:

- current-state read
- recommended architecture
- implementation order
- safety and verification rules
- source-backed facts vs inferred areas

## When To Read References

- Always read `references/harness-workflow.md` before substantial harness work.
- Read `references/source-backed-patterns.md` when:
  - the user mentions Claude Code, Codex, or Anthropic-style harnesses
  - you need concrete file-backed precedent for a design choice
  - you are deciding whether a feature belongs in loop, tools, permissions, memory, or orchestration
