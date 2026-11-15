# Agent Graph Task

[Русский](README_ru.md) · [Documentation](https://falkolab.github.io/agent-graph-task/guide.en.html) · [Руководство на русском](https://falkolab.github.io/agent-graph-task/guide.ru.html)

A structured, evidence-gated approach to complex work with AI agents: bounded task graphs, explicit acceptance criteria, independent checks, controlled retries, resource tracking, and resumption across sessions.

**Current integration:** Claude Code Skill. The same protocol supports direct execution and, when appropriate, Claude Code Dynamic Workflows.

## Install

**For one project** (run from its root):

```bash
git clone https://github.com/falkolab/agent-graph-task.git /tmp/agent-graph-task
mkdir -p .claude/skills
cp -R /tmp/agent-graph-task/.claude/skills/graph-task .claude/skills/
```

**Globally** (available across Claude Code projects):

```bash
git clone https://github.com/falkolab/agent-graph-task.git /tmp/agent-graph-task
mkdir -p ~/.claude/skills
cp -R /tmp/agent-graph-task/.claude/skills/graph-task ~/.claude/skills/
```

Invoke the skill in Claude Code:

```text
/graph-task Refactor the importer without changing external behavior. Propose a task graph and acceptance checks before modifying code.
```

The skill defines the task, checks, scope and budget before execution. It can run steps directly or use Dynamic Workflows for suitable multi-agent work. Launching a Workflow requires separate approval. Advisor is optional and configured independently.

## How it works

- **Plan:** Define bounded nodes, dependencies, inputs, outputs, owners and acceptance checks in `graph.md`.
- **Execute:** Use code for deterministic steps; delegate isolated work when it adds value. A Dynamic Workflow is an optional execution mechanism, not a second task graph.
- **Verify:** Check saved artifacts and input versions before accepting results; record evidence in `checks.md`.
- **Recover:** Preserve checkpoints in `progress.md`, revalidate relevant evidence, and resume remaining work.
- **Control:** Respect authorized scope, retry limits and measured resource usage.

The files `graph.md`, `checks.md` and `progress.md` are created for each task, not shipped as templates.

## Files

| File | Purpose |
| --- | --- |
| [SKILL.md](.claude/skills/graph-task/SKILL.md) | Skill entry point and execution instructions |
| [references/protocol.md](.claude/skills/graph-task/references/protocol.md) | Full normative protocol |
| [English documentation](https://falkolab.github.io/agent-graph-task/guide.en.html) | Illustrated reference and usage guide |
| [Русская документация](https://falkolab.github.io/agent-graph-task/guide.ru.html) | Same guide in Russian |

The HTML guides are for people; the agent follows `SKILL.md` and `protocol.md`.

## Related project

[AI Agent Task Graph — generic agent prompt](https://github.com/falkolab/agent-task-graph-prompt) is the framework-independent prompt. This repository provides its Claude Code implementation.

## Author

Andrei Tkachenko, Telegram channel "Automate It" (Rus): [@aitomateit](https://t.me/aitomateit)
