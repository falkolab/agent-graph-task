---
name: graph-task
description: Execute complex project work as a bounded, evidence-gated, budget-aware and resumable graph. Use only when explicitly invoked.
argument-hint: "[task; output; acceptance checks; scope; limit; budget; review policy]"
disable-model-invocation: true
---

# Graph Task — project-scoped execution

Task request: $ARGUMENTS

**Mandatory policy loading:** read the complete normative file at `${CLAUDE_SKILL_DIR}/references/protocol.md` before further decisions, work, or accepting results:

- **first invocation** in a session — read it in full;
- **after context compaction** — read it in full again; do not assume earlier file contents survived;
- **continuing in a new session** — read it in full;
- **continuing in the same session with context intact** — do not reread it;
- **recovering an interrupted Workflow in the same session with context intact** — reread at least protocol §13–§14 and any section the recovery depends on; if context was lost, read the whole file.

The short SKILL.md is not a substitute. See the Dynamic Workflow execution policy in §14.

**Human documentation only:** `${CLAUDE_SKILL_DIR}/docs/guide.ru.html` and `${CLAUDE_SKILL_DIR}/docs/guide.en.html` explain the design to people. You do **not need to read them** to apply this skill. They are not normative and must not supply missing execution rules; open only when asked to discuss or edit documentation.

Never overwrite existing project instructions, change `~/.claude/`, or enable Advisor/Ultracode as an incidental side effect. Keep task changes within the authorized project, regardless of whether this skill is installed globally or in the project.

## 1. Establish the task contract

Bind `TASK`, `OUTPUT`, `CHECKS`, `SOURCES`, `SCOPE`, `LIMIT`, `BUDGET`, and `REVIEW_POLICY` from the request and existing permissions. Define “done” before implementation, establish a baseline when relevant, and do not invent spending authorization, metrics or unavailable tools. Ask one concrete question only if a missing constraint materially prevents acceptance or authorized work. A call with no task does not authorize work: obtain the task.

## 2. Specify and approve the graph

Decompose by required context, not folders; one node may suffice. Inventory the real input formats/versions before proposing new interface contracts. Specify inputs/outputs, dependencies, owners, acceptance checks, reviewers, limits and budget in `.agent-runs/<task-id>/graph.md`. Include the selected execution mode: **A (direct) or B (Dynamic Workflow)**. For mode B also specify bounded node subset/phase, agent count, maximum concurrent agents, estimated full-tree usage, checkpoints and the responsible writers/gates. Obtain the required plan approval before fan-out.

**Lightweight path (protocol §4.1):** a single simple node with no external side effects may use a minimal baseline, a bounded change, the mandatory gate on the saved result, and a short record in `checks.md`/`progress.md`. Extra subagents, independent review, multi-node decomposition and an extended report are not required. Acceptance criteria, `SCOPE`/permissions and verification of the actual saved result are never relaxed.

**Mode B requires separate explicit approval AFTER the graph is shown**, covering this Workflow's phase/subset, agent count, concurrency, expected cost/budget and permitted operations. Calling `/graph-task`, approving `graph.md` in principle, a saved plan, or an automatic tool-permission bypass does **not** grant that separate approval. Do not rely on Claude Code's per-run UI prompt to enforce this policy; it may be skipped in some permission modes. If approval is absent, stop at the proposed graph or use only already authorized direct work.

## 3. Choose execution, not competing orchestration

Mode A: Claude executes nodes directly, calls deterministic commands/code and delegates to fresh bounded subagents only when useful. Mode B: an approved JavaScript Dynamic Workflow script orchestrates the selected subset, with its worker/gate agents writing evidence; the root agent must not concurrently edit files owned by the running Workflow. Do not start a second controller for the same nodes. In **both** modes apply the same normative acceptance requirements, including §8 trial, §9 gates, §10 correction limits, §12 budget accounting, and §14 Workflow-specific procedures.

Keep root context concise: put raw media, broad source reads and large logs in bounded workers/tools; retrieve evidence paths, coverage and summaries instead of large payloads. Do not turn `ultracode` on automatically.

## 4. Evidence, preflight, and correction

Before a phase's first parallel fan-out, test a representative **real** node through that phase's actual chain and the required new/changed gates, including a known-invalid sample. Test later-stage gates before their first dependent use, not prematurely. Reuse previously verified unchanged gates and useful accepted trial output; fix harness/contract failures before dependents run.

Every PASS must be bound to verifiable versions (SHA, ETag, revision ID or equivalent) of **significant inputs, saved output, contract/`CHECKS`, and evaluator version/config**; if any is missing or unverifiable, the result is `unverified`, not PASS — for the Workflow aggregator as well as the root. Before reusing any PASS, recheck that binding; on any change record an `admission-revoked` event in `checks.md` (node, old version, reason, revoked dependents), not only in chat. A requirement absent from and not implied by the agreed contract is a **spec gap**: record it in `checks.md`, ask the contract owner, and do not charge it as a correction return. Supported high/medium findings **and failed, missing OR unverified mandatory `CHECKS`** block acceptance; do not invent evidence. Low-only findings are reported. Enforce one writer per file and a maximum of **three correction returns per node across restarts**, excluding initial production; persist each return to `checks.md` **before** dispatch. On contract/harness/judge failures, correct the responsible check node, not the producer; never weaken valid requirements to obtain PASS.

## 5. Budget, recovery, and closure

Before retrying any **external write**, verify its actual remote outcome and reuse the same idempotency key when supported; stop rather than blindly duplicate uncertain operations. Measure the full tree **per node** and overall: root, subagents, review, judge, retries, tools and in-flight work. Distinguish observed counters, estimates and unknown usage. A prompt cannot impose a hard spending cap; use an actual counter/watchdog or avoid a long autonomous Workflow when a hard cap is required. Stop before executing unapproved cost-bearing phases.

Preserve `.agent-runs/<task-id>/graph.md`, `checks.md`, `progress.md`, versioned artifacts and an evidence/resource index. The root agent alone writes `progress.md` between Workflow runs/phases, after control returns; Workflow workers save assigned outputs and unique evidence, and one aggregator owns `checks.md` during the phase. Record real run IDs/script paths when returned, otherwise mark unknown/pending. In the persisted `progress.md` header record the **resolved absolute path** to the normative protocol (`${CLAUDE_SKILL_DIR}/references/protocol.md` as expanded when this skill is invoked), never a literal variable or an assumed project-relative path. If the stored path no longer exists after relocation, reinvoke `/graph-task` to resolve the current location before resuming. Prefer native resume in the **original** session; for a fresh session validate saved hashes and admissions, revoke invalid ones and execute only the remaining work under renewed authorization. Reload the normative protocol according to the policy-loading rules above before further decisions.

Return the requested deliverable plus PASS/FAIL/UNRESOLVED, check evidence, consumed versions, measured/unknown resources and exact next steps for unresolved work.

## Advisor boundary

Advisor is optional, separately configured, and never installed/changed by this skill. Consult it only if already enabled and genuinely useful for a consequential architectural question, recurring unexplained error or high-risk decision. It is neither an independent reviewer nor an evidence gate. Do not invoke `/advisor` or modify `advisorModel` without separate user instruction; avoid inherited Advisor cost fan-out.