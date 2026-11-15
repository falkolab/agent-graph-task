# Agent Graph Task

[Русский](README_ru.md) · [User guide (English)](docs/guide.en.html) · [Руководство (RU)](docs/guide.ru.html)

An evidence-gated task graph protocol for AI agents. **Current implementation: Claude Code Skill**, invoked manually, for complex tasks: bounded task graphs, independent checks, controlled retries, resource budgets, and resumable work. The same protocol supports **direct execution** and **Dynamic Workflows**. No workflow starts merely because the Skill was invoked: execution scope and parallelism require explicit approval.

> **Status:** v6.1. Static review completed; end-to-end behavior in Claude Code, including interrupted Dynamic Workflows, has not yet been validated in a live run.

## Quick start

**Project-local installation** (from your project root):

```bash
git clone https://github.com/falkolab/agent-graph-task.git /tmp/agent-graph-task
mkdir -p .claude/skills
cp -R /tmp/agent-graph-task/.claude/skills/graph-task .claude/skills/
```

**Global installation** (available in your Claude Code projects):

```bash
git clone https://github.com/falkolab/agent-graph-task.git /tmp/agent-graph-task
mkdir -p ~/.claude/skills
cp -R /tmp/agent-graph-task/.claude/skills/graph-task ~/.claude/skills/
```

In Claude Code, invoke the Skill explicitly:

```text
/graph-task Refactor the importer, preserve existing behavior, propose a task graph and acceptance checks before changes.
```

Review the proposed scope, budget, and plan before authorizing execution. The Skill can perform small steps directly, or use native Dynamic Workflows when justified and separately approved. **Advisor is optional and configured independently**; it is not required to run this Skill.

## How it is structured

| File | Role |
| --- | --- |
| [SKILL.md](.claude/skills/graph-task/SKILL.md) | Entry point and mandatory policy loading |
| [references/protocol.md](.claude/skills/graph-task/references/protocol.md) | Normative execution, acceptance, budget, and recovery rules |
| [guide.en.html](docs/guide.en.html) | Illustrated documentation for people (not a normative source) |
| [guide.ru.html](docs/guide.ru.html) | Russian human-readable documentation |

A task's `graph.md` is the **reviewable specification**, whereas Dynamic Workflow JavaScript is an optional **execution mechanism**. `checks.md` captures evidence; `progress.md` enables handoffs and recovery. These task-state files are created **during execution**, not supplied as empty templates in the repository.

## Documentation website

GitHub Pages can serve the bilingual guide from [`/docs`](docs/). Once Pages is enabled for the `main` branch and `/docs` folder, the site will be available at:

`https://falkolab.github.io/agent-graph-task/`

The docs are static HTML, with no required build step. The Skill's own `docs/` copies are shipped for offline use; the top-level `/docs` folder is the GitHub Pages publishing copy.

## Related project

[AI Agent Task Graph — generic agent prompt](https://github.com/falkolab/agent-task-graph-prompt): the framework-agnostic task-graph prompt. This is a separate implementation of the task graph methodology for Claude Code. Other runtimes can use future adapters; compatibility beyond Claude Code is not claimed.

## Credits and caveats

Maintained by [Andrey Tkachenko](https://github.com/falkolab) · [«Автоматизируй это»](https://t.me/aitomateit).

This protocol improves process discipline but is **not a hard spending limit** by itself; actual limits need tool/runtime enforcement. Publication does not imply the Skill has passed a full behavioral test suite. No software license has been selected yet; publishing a repository publicly alone does not grant reuse rights.