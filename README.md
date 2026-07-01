# Orca Skill

Codex plugin for a conservative Orca workflow stack:

```text
Orca = live orchestration
GitNexus = code intelligence and impact, when installed in the repo
agentmemory = durable memory and handoff
Graphify = out of scope
```

The plugin ships one skill: `orca-agentmemory-workflow`.

## Install

```bash
git clone https://github.com/bamburra/orca-skill.git
cd orca-skill
codex plugin marketplace add .
codex plugin add orca-agentmemory-stack@orca-skill
```

Start a new Codex thread after installation so the skill metadata is loaded.

## Use

Invoke explicitly:

```text
$orca-agentmemory-workflow
```

Or trigger it naturally with tasks that mention Orca, multi-agent work, long tasks,
context compaction, handoff, durable memory, high token usage, agentmemory,
GitNexus-aware repo work, or "modo economico".

## Conservative agentmemory setup

The skill does not install or enable agentmemory automatically. When requested, it
uses this conservative setup:

```bash
npm install -g @agentmemory/agentmemory
agentmemory
agentmemory connect codex --with-hooks
npx skills add rohitg00/agentmemory -y
```

Recommended environment:

```env
AGENTMEMORY_TOOLS=core
AGENTMEMORY_INJECT_CONTEXT=false
AGENTMEMORY_AUTO_COMPRESS=false
CONSOLIDATION_ENABLED=false
AGENTMEMORY_REFLECT=false
AGENTMEMORY_AGENT_SCOPE=shared
```

## GitNexus

This stack does not install GitNexus. In repositories that already have GitNexus
instructions, follow them exactly. For example, in `notification-service`, run
impact analysis before editing symbols and `detect_changes()` before committing.

