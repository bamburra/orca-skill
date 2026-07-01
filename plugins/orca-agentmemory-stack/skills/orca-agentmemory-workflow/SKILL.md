---
name: orca-agentmemory-workflow
description: Use when a task mentions Orca, multi-agent work, long-running implementation, context compaction, handoff, durable memory, high token usage, agentmemory, GitNexus-aware repo work, or "modo economico".
---

# Orca agentmemory Workflow

## Core Rule

Use the smallest coordination layer that fits:

```text
Orca = live orchestration
GitNexus = code intelligence and impact, when installed in the repo
agentmemory = durable memory and handoff
Graphify = out of scope for this workflow
```

Do not create a global `TASK_STATE.md`. Do not make agentmemory the task scheduler. Do not use agentmemory leases/signals unless the user explicitly asks to replace Orca coordination.

## First Checks

If the task may use Orca, inspect live state before choosing a flow:

```bash
orca status --json
orca terminal list --json
orca orchestration task-list --json
```

If `orca` is unavailable or not running, continue with a single-agent flow and report that Orca orchestration was skipped.

If the repo instructions mention GitNexus, follow them exactly. In `notification-service`, this means:

- run `impact({target: "...", direction: "upstream"})` before editing any symbol;
- warn on HIGH or CRITICAL impact before editing;
- run `detect_changes()` before commit;
- use GitNexus `query`, `context`, or `explain` for code flow questions before broad grep.

## Flow Choice

### One agent

Use plain Orca worktree/terminal status only:

```bash
orca worktree set --worktree active --comment "short current state" --json
```

Use agentmemory only for durable facts:

- decisions;
- root cause;
- important verification result;
- handoff or next step.

### Two or more agents, or long task

Use Orca orchestration:

- create or reuse a `taskId`;
- dispatch workers with `dispatchId`;
- workers send exactly one `worker_done`;
- `worker_done` includes a short summary, `filesModified`, and `reportPath` when a report exists;
- the coordinator reads summaries, not full logs.

Prefer setting a stable per-worker identity:

```bash
env AGENT_ID=<repo>-<task-id>-<role> AGENTMEMORY_AGENT_SCOPE=shared codex
```

If Orca launches the agent and env injection is unavailable, put the expected `AGENT_ID` in the dispatch prompt and require the worker to use it in any memory/handoff it records.

### Compaction risk

Before context grows further:

1. Save a compact durable memory.
2. Include objective, decisions, files/symbols, verification, blockers, next step, Orca task/terminal, and `AGENT_ID`.
3. Store paths to reports instead of copying report bodies.

Memory shape:

```text
Task <taskId>: <objective>.
Decisions: ...
Files/symbols: ...
Verification: ...
Pending/next: ...
Origin: Orca <worktree/terminal>, agent <AGENT_ID>.
```

## agentmemory Mode

Conservative default:

```env
AGENTMEMORY_TOOLS=core
AGENTMEMORY_INJECT_CONTEXT=false
AGENTMEMORY_AUTO_COMPRESS=false
CONSOLIDATION_ENABLED=false
AGENTMEMORY_REFLECT=false
AGENTMEMORY_AGENT_SCOPE=shared
```

Use `remember`, `recall`, `handoff`, or their MCP equivalents when available. If agentmemory tools are unavailable, do not invent memory state; use Orca summaries and tell the user agentmemory is not wired in this session.

Save only durable knowledge:

- architectural decisions;
- validated cause/fix;
- API or FE/BE contracts;
- important test command plus summarized result;
- blockers and resolution;
- handoff summary.

Do not save:

- raw logs;
- full diffs;
- full test output;
- secrets, tokens, personal data, or credentials;
- facts better represented by GitNexus.

## Setup Only When Asked

If the user asks to configure the stack, use the conservative setup:

```bash
npm install -g @agentmemory/agentmemory
agentmemory
agentmemory connect codex --with-hooks
npx skills add rohitg00/agentmemory -y
```

Validate:

```bash
node -v
npm view @agentmemory/agentmemory version
agentmemory --version
curl -fsS http://localhost:3111/agentmemory/health
agentmemory doctor
```

Confirm that continuous context injection remains off. `PreCompact` may provide short compaction context; `SessionStart` and `PreToolUse` should not inject ongoing context when `AGENTMEMORY_INJECT_CONTEXT=false`.

## Completion

For multi-agent work, final output should include:

- Orca task/worktree status;
- agent count and flow used;
- GitNexus checks used, if applicable;
- agentmemory memories saved or recalled, if applicable;
- verification commands run.

