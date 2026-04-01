# Source-Backed Patterns

This note captures the practical patterns derived from these repositories:

- `oboard/claude-code-rev` at commit `64915d7`
- `sanbuphy/claude-code-source-code` at commit `2ca5dda`

GitHub sources:

- `https://github.com/oboard/claude-code-rev`
- `https://github.com/sanbuphy/claude-code-source-code`

Use these findings as architectural precedent, not as a mandate to reproduce Anthropic internals exactly.

## 1. The loop is the nucleus

Primary files:

- `src/query.ts`
- `src/QueryEngine.ts`

Observed patterns:

- The core loop is still fundamentally "model -> inspect tool calls -> execute -> append tool results -> continue".
- `query.ts` keeps the orchestration loop, while `QueryEngine.ts` wraps it in a higher-level session/runtime surface with `AsyncGenerator` streaming.
- Tool-use detection is treated as more trustworthy than raw `stop_reason`, which is explicitly noted as unreliable in the source.
- Missing or interrupted tool results are reconciled so transcripts do not break downstream consumers.
- Tool execution can start while the assistant is still streaming, then be re-ordered into transcript-safe output later.

Translation:

- Keep the loop explicit and inspectable.
- Separate low-level turn execution from higher-level session APIs.
- Treat streamed event processing as part of the harness, not as UI-only behavior.

## 2. Tool contracts are centralized, tool policies are local

Primary files:

- `src/Tool.ts`
- `src/tools.ts`
- `src/tools/MCPTool/MCPTool.ts`

Observed patterns:

- `buildTool()` provides uniform defaults and a stable tool surface.
- Tools can define either Zod-style schemas or direct JSON schema.
- Registry selection in `src/tools.ts` decides which tools appear in which contexts or modes.
- Special tool categories exist for standard tools, MCP-backed tools, planning tools, task tools, and worktree tools.

Translation:

- Create one consistent tool interface and one registry.
- Let each tool own its schema, user-facing label, permission logic, and result mapping.
- Make tool availability mode-aware instead of hardcoding everything into the system prompt.

## 3. Permission design is deny-first and structure-aware

Primary files:

- `src/tools/PowerShellTool/powershellPermissions.ts`
- `src/tools/PowerShellTool/modeValidation.ts`
- `src/tools/PowerShellTool/pathValidation.ts`
- `src/tools/BashTool/*`

Observed patterns:

- Deny and ask rules are checked before permissive fallbacks.
- Permission matching normalizes aliases and canonical command names.
- Parse failures do not bypass explicit deny rules.
- Accept-edits style auto-allow exists, but only behind narrow structural checks.
- Path validation is aware of writes vs reads, deny rules, internal editable paths, sandbox allowlists, symlink risk, and dangerous removal.

Translation:

- Never rely on prompt-only safety.
- Model-friendly permission modes are fine, but every auto-allow path needs structural validation.
- Put path and command safety close to the shell tool, not in generic middleware.

## 4. Planning and tasks are first-class, not an afterthought

Primary files:

- `src/tools/EnterPlanModeTool/EnterPlanModeTool.ts`
- `src/tools/ExitPlanModeTool/ExitPlanModeV2Tool.ts`
- `src/tools/TodoWriteTool/TodoWriteTool.ts`
- `src/tools/TaskCreateTool/TaskCreateTool.ts`
- `src/tools/TaskUpdateTool/TaskUpdateTool.ts`
- `src/tools/TaskGetTool/TaskGetTool.ts`
- `src/tools/TaskListTool/TaskListTool.ts`

Observed patterns:

- Plan mode changes permission/context behavior before implementation starts.
- Exit from plan mode can require approval and can feed an approved plan back into implementation.
- Tasks support status, ownership, blockers, and verification nudges.
- Verification is deliberately pushed before final wrap-up, not merely mentioned in prose.

Translation:

- For non-trivial agent work, a harness should support explicit planning and tracked execution state.
- Keep a distinction between informal todos and richer task graphs.
- Put approval and verification hooks inside the workflow, not just in docs.

## 5. Subagents are isolated runtime contexts

Primary files:

- `src/tools/AgentTool/runAgent.ts`
- `src/tools/AgentTool/forkSubagent.ts`
- `src/tasks/LocalAgentTask/LocalAgentTask.tsx`
- `src/tools/shared/spawnMultiAgent.ts`

Observed patterns:

- Subagents can run with fresh messages, forked context, isolated abort controllers, and isolated file caches.
- Read-only or specialized agents can omit large context such as full `CLAUDE.md` or stale git status to save tokens.
- Async/background agents preserve transcripts separately and surface notifications back to the parent.
- Team spawns add mailbox-based initial prompts, visible teammate metadata, and explicit runtime identity.

Translation:

- Child agents should be isolated by default.
- Context inheritance should be selective and motivated.
- Team coordination should rely on task boards and message passing, not invisible shared cognition.

## 6. Skills are knowledge injection, not just static docs

Primary files:

- `src/tools/SkillTool/SkillTool.ts`
- `src/commands.ts`

Observed patterns:

- Skills are validated before execution.
- Fork-context skills can run in isolated agent contexts.
- Skill invocation is serialized and tracked.
- Marketplace/local/MCP-provided prompt skills are normalized into one command space.

Translation:

- Treat reusable procedural knowledge as a first-class runtime primitive.
- Keep skill payloads lean and offload detail to references.
- When a skill materially changes behavior, run it as an explicit action with observable lineage.

## 6.5 Prompt layering matters

Primary files:

- `src/constants/prompts.ts`
- `src/constants/systemPromptSections.ts`
- `src/utils/queryContext.ts`
- `src/utils/systemPrompt.ts`

Observed patterns:

- Stable prompt sections are assembled separately from volatile runtime context.
- Dynamic or cache-breaking material is injected after an explicit boundary.
- CLAUDE.md, memory, and other late-bound context are treated as controlled layers, not random prompt concatenation.

Translation:

- Design your harness prompt builder as a pipeline.
- Keep cache-friendly base instructions stable.
- Inject volatile context, memory, and user-specific state through explicit late-bound stages.

## 7. Memory is lazy, typed, and selective

Primary files:

- `src/memdir/memdir.ts`
- `src/memdir/findRelevantMemories.ts`
- `src/memdir/memoryScan.ts`

Observed patterns:

- Memory directories are prepared up front so the agent does not waste turns checking for them.
- `MEMORY.md` acts as an entrypoint/index, with topic files holding deeper detail.
- Relevance selection is done over file headers and descriptions first, not full documents.
- Memory guidance explicitly distinguishes plan state, task state, and durable memory.

Translation:

- Persistent memory should be cheap to scan and selective to recall.
- Maintain a compact index plus typed topic files.
- Do not pollute memory with current-turn execution state.

## 8. Compaction is layered, not one mechanism

Primary files:

- `src/services/compact/autoCompact.ts`
- `src/services/compact/compact.ts`
- `src/services/compact/apiMicrocompact.ts`
- `src/services/compact/snipCompact.ts`

Observed patterns:

- Token budgets and thresholds are explicit.
- Compaction includes clearing tool results, summary compaction, snipping, and optional context-collapse integration.
- Recursive/autoretry behavior has circuit-breaker safeguards.
- Post-compaction cleanup preserves only bounded amounts of files/skills needed to continue.

Translation:

- Long-running harnesses need multiple compaction strategies.
- Compaction must preserve active intent, not just reduce tokens.
- Repeated failures need a stop condition.

## 9. MCP-style integrations should expose both tools and resources

Primary files:

- `src/services/mcp/MCPConnectionManager.tsx`
- `src/tools/MCPTool/MCPTool.ts`
- `src/tools/ListMcpResourcesTool/ListMcpResourcesTool.ts`
- `src/tools/ReadMcpResourceTool/ReadMcpResourceTool.ts`

Observed patterns:

- External servers are connection-managed separately from tool execution.
- Resources are listed and read independently from tools.
- Reconnect and caching behavior is explicit.
- Binary resource payloads are persisted safely instead of dumped blindly into context.

Translation:

- External integrations should not be "tool call only".
- Model-facing resource discovery is often as important as execution.
- Separate connection lifecycle from invocation lifecycle.

## 10. Worktree isolation belongs to the harness, not just git helpers

Primary files:

- `src/tools/EnterWorktreeTool/EnterWorktreeTool.ts`
- `src/tools/ExitWorktreeTool/ExitWorktreeTool.ts`

Observed patterns:

- Worktree entry updates session cwd, caches, and persisted worktree state.
- Exit refuses destructive cleanup when it cannot prove cleanliness.
- The workflow supports preserve vs remove actions with explicit confirmation.

Translation:

- For coding agents, workspace isolation should be a deliberate orchestration primitive.
- Never remove an isolated workspace without proving safety or receiving explicit confirmation.

## 11. What must be inferred instead of copied

From `claude-code-source-code`:

- The README gives a useful "12 progressive harness mechanisms" ladder.
- `QUICKSTART.md` makes clear that many internal pieces are missing behind Bun feature flags and dead-code elimination.
- Some advanced modules are internal-only or stripped from the public artifact.

Practical consequence:

- Reuse the public architecture patterns.
- Do not design around hidden Anthropic-only modules.
- Build your own equivalents only when the user actually needs those capabilities.

## Recommended translation into future work

When the user asks for an agent harness, default to this delivery order:

1. explicit loop
2. tool contract and registry
3. permission model
4. planning and tasks
5. subagent isolation
6. persistence and memory
7. compaction
8. external integrations
9. worktree and team orchestration
10. verification pass

That order best matches the source-backed evidence from the restored code and the external analysis repo.
