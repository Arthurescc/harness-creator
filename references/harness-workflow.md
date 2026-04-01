# Harness Workflow

This reference turns reverse-engineered Claude Code patterns into a reusable implementation workflow for future agent-harness work.

## Core principle

Build the harness as a layered runtime. Each layer must be independently useful before the next one is added.

Minimal loop first, orchestration last.

## The 10-step build order

### 1. Frame the target

Before touching code, answer:

- What kind of agent is this: chat assistant, coding agent, research agent, operator, or team coordinator?
- What are the real side effects: reading, writing, shelling out, network, browser, git, deployment?
- Does the user need single-session help, resumable jobs, or long-running background work?
- Is delegation actually required, or would a strong single-agent loop be enough?

If the answers are still fuzzy, build Tier 1 or Tier 2 only.

### 2. Build the loop

Start from the smallest viable query loop:

```text
messages -> model -> response
if tool calls:
  execute tool calls
  append tool results
  continue
else:
  finish
```

Required loop responsibilities:

- stream model output if the interface benefits from it
- collect tool calls even when stop reasons are imperfect
- reconcile aborted or partial tool executions
- keep transcript ordering stable
- surface progress events separately from final answer events

Keep the loop thin. It should orchestrate, not own every policy.

### 3. Define the tool contract

Each tool needs a consistent contract:

- name
- schema or input JSON schema
- description/prompt text
- permission check
- execution method
- result normalization
- truncation/serialization rules
- optional progress UI hooks

Recommended split:

- `Tool` interface or base class
- `buildTool()`-style helper for safe defaults
- registry file that decides which tools are active in which mode
- per-tool modules responsible for parsing and safety checks close to the tool

Critical rule:

Do not bury permission logic inside the central loop. Let tools report a permission decision, then let the loop enforce it consistently.

### 4. Add deny-first permissions

Before enabling powerful tools, add a real permission pipeline:

1. exact deny and ask rules
2. prefix or wildcard deny and ask rules
3. structural safety analysis
4. path- and operation-aware validation
5. mode-based auto-allow only for narrow, validated cases
6. fallback to ask

Use these patterns:

- deny rules win over allow rules
- exact match checks run before fuzzy prefix matching
- parse failures should still respect explicit deny rules on raw command text
- auto-allow must be narrower than ask, never broader
- read/write/shell/network classification must be explicit

For coding agents, add special handling for:

- destructive filesystem operations
- git hook and bare-repo abuse
- symlink or junction tricks
- cwd-changing compound commands
- sandbox or writable-directory boundaries

### 5. Add planning and task state

Once the loop and tool safety are reliable, add planning:

- plan mode or equivalent read-only design phase
- todo/task list for execution tracking
- task dependency graph with `blockedBy` and `blocks`
- explicit approval gate before leaving planning on large changes

Treat plans, tasks, and memory differently:

- plans align on approach
- tasks track current execution
- memory stores future-useful context across sessions

Do not use memory as a substitute for tasks.

### 6. Add subagents with explicit isolation

Only add subagents when:

- the work can be decomposed cleanly
- tasks have bounded ownership
- the parent can continue useful work while children run

Isolation rules:

- fresh message history by default
- optional forked context only when identical prefix/cache behavior matters
- separate abort controller
- separate file-state cache
- separate transcript or sidechain output
- explicit allowed tools or narrowed tool pool
- no silent shared mutable state except what is deliberately shared

Practical spawn modes worth supporting:

- in-process child with isolated messages
- forked child that inherits selected context
- worktree-isolated child for code changes
- remote/container child for stronger isolation

### 7. Add persistence and resume

Longer-lived harnesses need durable state:

- append-only transcript log
- session metadata
- task state on disk or in durable storage
- worktree/session metadata
- resume path that rebuilds enough context to continue

Persist user inputs before waiting on the model so sudden termination does not erase the request.

Split persistence by importance:

- user inputs and task state: durable and prompt
- assistant deltas/progress: ordered, buffered, tolerant of delay

### 8. Add memory and scoped recall

Persistent memory should be selective, typed, and lazy:

- maintain a small index or entrypoint file
- store detailed notes in topic files
- scan metadata first, not full bodies
- use a selector step to pull only clearly relevant memory files
- distinguish agent memory from team/shared memory when collaboration exists

Useful memory types:

- user preferences
- durable project conventions
- repeated warnings/gotchas
- reference docs that will matter again later

Avoid saving:

- current-task execution state
- derivable code facts that can be re-read from the repo
- large blobs that belong in normal project docs

### 9. Add compaction and context budgeting

If the harness runs long enough, context pressure is guaranteed.

Add compaction in layers:

- token budgeting and warnings
- tool-result clearing or trimming
- summarization compaction
- stale-message snipping
- optional structured context collapse

Guardrails:

- compaction must preserve open tasks, recent commitments, and active tool lineage
- compaction routines need their own recursion guards
- repeated compaction failures need a circuit breaker
- preserve or re-inject only the skill and memory material still needed after compaction

### 10. Add integrations, teams, and worktrees

Once the single-agent harness is stable, add external and multi-agent layers:

- MCP or similar adapter for external tools and resources
- list/read resource primitives, not just execute-tool primitives
- background tasks and notifications
- team task board
- mailbox or message passing
- worktree isolation for code-writing teammates
- verification agent or verifier pass before final completion

For multi-agent systems:

- task board is the shared truth
- messages are explicit and structured
- ownership is visible
- write scopes are disjoint when possible
- verification is independent from implementation

## Default module blueprint

Use a shape close to this when there is no stronger local convention:

```text
src/
  harness/
    core/
      query-engine.ts
      session.ts
      events.ts
      streaming.ts
    tools/
      base.ts
      registry.ts
      shared/
    permissions/
      policy.ts
      path-safety.ts
      rule-matching.ts
      modes.ts
    planning/
      plan-mode.ts
      todos.ts
      tasks.ts
    agents/
      spawn.ts
      resume.ts
      messaging.ts
      worktree.ts
    memory/
      prompt.ts
      recall.ts
      scan.ts
      storage.ts
    compact/
      budget.ts
      compact.ts
      summarize.ts
    integrations/
      mcp/
      browser/
      vcs/
    persistence/
      transcripts.ts
      session-store.ts
      task-store.ts
```

Adapt names to the host project. The important part is the boundary clarity, not the exact folder spelling.

## Verification checklist

Run these scenarios before claiming the harness is complete:

1. Single-turn text-only request succeeds.
2. Single tool call succeeds and transcript order is correct.
3. Denied command is denied even if parsing fails.
4. Plan/task flow works on a non-trivial request.
5. Resume restores enough context to continue.
6. Compaction keeps the task objective intact.
7. Subagent results do not pollute the parent context unless explicitly passed back.
8. Worktree cleanup refuses destructive removal unless explicitly confirmed.
9. Resource-listing integrations degrade cleanly when unavailable.
10. Verification happens before final success messaging.

## Anti-patterns

- Building a swarm before the single-agent loop is dependable.
- Using one huge omniscient system prompt instead of tools, memory, and references.
- Letting allow rules over-match aliases or normalized commands.
- Treating memory, task state, and plan state as the same thing.
- Making subagents share uncontrolled mutable state.
- Triggering compaction without preserving active task intent.
- Copying internal feature-gated architecture literally instead of translating the pattern.
