# Full graph execution protocol (normative reference)

This is the **self-contained normative specification** for the project Skill `/graph-task`. This file is the only detailed source of execution rules. Documents of a specific run go into `.agent-runs/<task-id>/`; a native Dynamic Workflow is chosen only where it is better than direct execution. If the short `SKILL.md` and this detailed specification conflict, follow the stricter requirements for authorization, reliability and checks, and report the conflict to the user.

## 0. Purpose and stopping criterion

Solve the task as a graph of bounded nodes, not as one uncontrolled long session. Define `done` before work starts. Deliver a working artifact, a verification report and a graph state that another session can reproduce. Finish when all required nodes are accepted, or on a blocker/limit with an honest status.

## 1. Roles and context management

- Root agent: DAG design, execution, integration, decisions to change the graph; while a Dynamic Workflow is active it does not compete with it to execute the same nodes. First read the project instructions and the existing work.
- Delegate a subtask only when a separate context or independent parallel work is useful. A subagent for independent judgment starts in a **fresh, bounded context** with the specification, inputs and checks.
- The root context is compact text. Hand large logs, broad source reads, screenshots, images and video to a separate bounded agent or to code tools. Save evidence and the index immediately.
- A media agent receives **exact paths and questions** and returns *text, coverage, uncertainty, links to evidence files*; it does not put frames/encoded media into the root context. The root reads only targeted excerpts.

## 2. Experimental principles

From autoresearch: measure a baseline; give every node a scope and a fixed evaluator; change one thing at a time, measure, keep improvements and failed attempts; prefer fewer nodes as long as comparability is preserved.

## 3. Input contract (must be resolved before substantial work)

- `TASK`: what has to be done.
- `OUTPUT`: the final artifact and its path.
- `CHECKS`: explicit acceptance criteria.
- `SOURCES`: input data/materials.
- `SCOPE`: allowed changes/paths/operations.
- `LIMIT`: execution limit — cycles, time/nodes, where applicable.
- `BUDGET`: units, total for the **whole tree** (root, workers, reviewer, judge, retries, external tools), measurement source, hard cap or estimate only, confirmation threshold and reserve for unfinished work/closure.
- `REVIEW_POLICY`: risky nodes, specific review roles, judge conditions, privately supplied commands, limits.

Permission already given is reused without asking again. If the acceptance criterion or the allowed spend is materially unknown, ask **one concrete** question before expanding the work. Do not present API dollars as a percentage of a subscription quota, and do not mix estimates with observed counters.

By default a simple node is: producer + deterministic checks; additional independent review proportional to risk. No mandatory four-role circle.

## 4. Layer 1 — decomposition and inventory

- Split by required context, not by folders. A one-node graph is acceptable for a bounded task, but it does not cancel the mandatory input contract, verification and reporting when the Skill is invoked explicitly. A DAG edge exists only if one node's input depends on another node's output. Independent nodes may run in parallel.
- Before proposing a **new interface contract/format**, a code node inventories all data available in scope: counts, sizes, schemas, producer/application versions, parse failures, unreadable exceptions. Distinguish the version that **created** the data from the installed CLI version.
- Semantically check every observed version/format and material edge cases on bounded samples, without sending the whole corpus to an LLM. Mark an incomplete inventory as coverage/uncertainty. Label synthetic fixtures and the absence of real data.
- In a fixed contract given by the user, **report** contradictions instead of fixing them silently.
- Before execution, save nodes, edges, owners, checks, review and budget in `graph.md` and obtain the designated approval.
- After approval and **before fan-out**, when changes are allowed, create/update a short `AGENTS.md` that preserves existing rules: hard rules only, a file map and links to graph/checks/evidence. Never overwrite an existing `CLAUDE.md`; a variant with a single `@AGENTS.md` reference is acceptable only without conflict and with permission.
- Detailed history lives in graph/progress. After results are accepted, update only confirmed rules. Do not assume a running agent automatically reread new instructions; record actual delivery when possible.

### 4.1. Lightweight path for a single simple node

Applies when the graph consists of **one simple node with no external side effects**: no publishing, external messages, writes to external systems/databases, migrations or paid fan-out. Then it is enough to have:

1. a minimal baseline (for example, one run of the mandatory checks before the change);
2. a bounded change within `SCOPE`;
3. the mandatory gate on the **saved** result (every `CHECKS` item, hash of the verified version);
4. a short record of the result in `checks.md` and of the state in `progress.md`.

Not required: additional subagents, independent review, multi-node decomposition, a separate trial run (§8 concerns parallel launch) and an extended report, unless `REVIEW_POLICY` or the risk requires them. **Never relaxed:** acceptance criteria, permissions and `SCOPE`, verification of the actual saved result, the ban on inventing evidence, the correction-return limit. If a second node or an external effect appears during the work, switch to the full protocol and record this in `progress.md`.

## 5. Layer 2 — choosing nodes and roles

- Choose `code`/`direct`/`bounded subagent`. A single command needs only a tool; a model agent is not required.
- A judgment worker sees only the specification, inputs and criteria; it creates the artifact, verifies the **saved version**, and returns paths, verdict, brief evidence and limitations.
- Tests are bounded by the contract, agreed cases and justified regression risks. Hypothetical cases do not silently expand acceptance. Exploration is labeled exploratory separately.
- If two reviewers read one artifact, record their roles: overlapping or complementary. Complementary does not automatically mean a judge. A judge only on a blocking disagreement/contradicting evidence/prescribed risk review.
- Each reviewer reports at most **5 actionable findings** by priority, with explicit coverage and deferred issues; **a clean review does not automatically trigger another model call**; a mandatory unverified item does not become PASS because of the limit. Review/judge cost is accounted for. Specifications are reusable with other inputs.

## 6. Layer 3 — deterministic operations

- Do merge/sort/dedupe/compare/count and other unambiguous operations in code. Show code nodes in `graph.md`. Run external commands directly through tools.
- If a model is really needed for the surrounding judgment, take the cheapest **sufficient** one among those allowed; raise the model tier and effort only after a recorded measurable failure. A `fixed-model` experimental condition takes precedence over optimization.
- A missing tool is a blocker; keep the useful work; never simulate running a tool, an independent reviewer, a judge or a test.

## 7. Layer 4 — permissions, writes and reproducibility

- Check effective permissions **before launching the graph**. All changes only within `SCOPE`. Another session does not provide isolation by itself; use sandbox/tool rules when needed.
- Reuse permission already given. Ask for permission before publishing, external messages or exceeding the budget.
- Prevent several workers from writing the same file in parallel.
- When **retrying a node that changes an external system** (API, database, publishing, sending), first check the actual outcome of the previous operation. If the API supports an idempotency key, retry with the **same** key. If a safe retry is not proven or the external state is unknown, stop that node with `UNRESOLVED` without blindly writing again. These rules do not apply to ordinary reads and local tests.
- After every output/correction and before a risky integration, keep a recoverable artifact version, input/output hashes, the attempt number and the diff. Git commits/tags are optional and **require permission**; they are not the default exchange mechanism.
- A prompt does not enforce a hard limit. A hard cap needs an external counter/watchdog; otherwise acknowledge the limitation and agree on a limit that can actually be enforced before an expensive fan-out.

## 8. Layer 4b — real pipeline trial

Before the **first parallel launch of a phase**, run **one representative real node** through that phase's actual chain (tools, gate parsers, reviewer/judge interfaces, schemas, labels). Test every **new or changed gate type this phase needs**, including a known-invalid sample it must reject. Test a later-stage gate whose inputs do not exist yet **before its consumer's first launch**, not in advance of all independent nodes.

Keep the real inputs/results. An untested required gate stays `unverified` and blocks dependent execution; **fix harness/contract errors first**. A successfully tested unchanged gate does not need another preflight; test it again when the checker changes. Harness/contract failures go back to the harness/contract owner and do not count as a defect of a correct producer result. **Reuse the accepted useful trial result** where applicable. This spends the agreed budget.

## 9. Layer 5 — gates and defect severity

- Put a gate on every edge with risky work. Deterministic evidence outweighs model confidence and its own report.
- Bind every `PASS`/admission in `checks.md` to the **versions of significant inputs**, the **hash of the verified result**, the **version/hash of the contract and the mandatory `CHECKS`**, the **evaluator version/configuration** and the consumer node version. Acceptable version evidence: a SHA hash, ETag, revision ID or an equivalent that can be rechecked. **If the version of any significant input, the result, the contract/`CHECKS` or the evaluator configuration is missing or unverifiable, the result is `unverified`, not `PASS`**, and the consumer does not start. The rule is the same for the root agent and for the Workflow aggregator (§14.3). On resume, recheck the whole binding, not only the output: a change in any significant part revokes the admission and the admissions of dependent nodes until rechecked. A final retrospective checkmark is not enough.
- **A revoked admission is recorded in `checks.md`**, not only reported in chat: an `admission-revoked` event with the node, the previously admitted version (hash/revision), the reason (what changed: input, result, contract or evaluator) and the list of revoked dependent admissions. After revocation the old `PASS` is void until rechecked. No separate log is created. The writer is the same single owner of `checks.md` (the root agent outside a Workflow run, the aggregator during a run).
- `high`: seriously wrong result, crashes in the normal scenario, critical safety/security.
- `medium`: wrong behavior in a realistic edge case.
- `low`: style, wording, minor impact. A low probability of a serious incident does **not** make it low.
- Supported `high/medium` = gate FAIL. `low` only in the report, without automatic correction. Any **failed, missing or unverified** mandatory `CHECKS` item blocks acceptance, regardless of severity.
- An actionable finding needs: the requirement, the location, a reproducible bad input **or** an explicitly violated requirement plus verifiable static/artifact evidence. Check structure with code before judgment. Keep unconfirmed claims and exclusion reasons separate; **never invent evidence**, links, check results or proof of execution.
- `checks.md`: node | significant input versions | artifact version | contract/CHECKS version | evaluator version/config | requirement | pass/fail/unresolved | evidence | admission/consumer; current saved outputs, invisible evidence marked explicitly. The same file holds the cumulative `correction-return` (stable node ID, return number), `admission-revoked` and `spec-gap` (§10) events; no separate log is needed.

## 10. Layer 6 — controlled feedback

- Send the producer **only** supported blocking findings with evidence and the restriction "fix only this node".
- One correction return is a return for high/medium or failed explicit acceptance. The initial implementation does **not** count as an attempt. At most **3 correction returns per node across all phases and restarts**; when exhausted, stop with `unresolved`. **Before launching each correction**, the single owner of `checks.md` saves a `correction-return` event (node ID, number, reason, verified version). The next call is allowed only after the write is confirmed. On resume the counter is restored from these events, not from the `progress.md` summary; if reading/writing does not work, do not launch the correction. A number reserved before a failure is not reset automatically.
- After the first full review, check the fix diff, the specific findings and dependent nodes instead of searching for all problems again. Run the mandatory deterministic regressions and integration tests on the saved result. If the contract/risks really changed, document the new scope review and permission; do not expand silently.
- A spec/contract/harness/judge error goes back to the orchestrator and to the owner of that node and **does not count as a producer attempt**. Keep the previous version, document the authorized change and revoke downstream admissions. A frozen contract is changed only by its owner/an authorized person.
- **Spec gap.** If a check reveals a requirement that is absent from the agreed contract (`TASK`, `CHECKS`, `SOURCES`, node specification) and does not follow from it, this is a spec gap, not a producer defect: record a `spec-gap` event in `checks.md` (node, requirement, check, classification rationale), **do not count a correction return** and do not return the requirement to the producer as a finding. Pass the question to the contract owner; after the decision, issue a new contract/`CHECKS` revision and recheck the affected results, revoking their admissions per §9. Do not automatically declare every failed test a spec gap: if the requirement is stated explicitly or follows unambiguously from the contract, it is an ordinary finding against the producer.
- Turn into instructions only accepted rules confirmed by evidence. Untested hypotheses stay in progress notes.

## 11. Layer 7 — closure

Before execution, build a closure checklist from `CHECKS`: all outputs exist/open; gates verified exactly the **saved, handed-over** versions; all failures/deferred/missing evidence are fixed or explicitly unresolved; AGENTS.md is current; a separate report of actual resources, unknowns, estimates and remaining budget.

When interrupted by a limit/blocker, give a partial status with exact next actions, keep the evidence and close owned processes, taking the reserve into account. Missing mandatory evidence forbids a full PASS.

## 12. Execution cycle

1. Read `graph.md`, choose ready nodes by dependencies and remaining budget.
2. Check the chain and gates of the current phase before parallel launch; test later-stage checks before their first dependent use. Then run independent nodes, respecting concurrency and write ownership.
3. Accept saved artifact versions through gates **before** downstream consumption.
4. Send only failing nodes back for bounded correction; low goes to the report.
5. Integrate accepted outputs and run the full mandatory checks.
6. Repeat until done, a block, `LIMIT` or a budget stop.
7. After each stage measure **every node and the whole tree** from real transcripts/counters, deduplicate model responses; do **not** add cumulative resume snapshots and nested cached/reasoning sub-counts a second time. Mark unknowns as unknown. At the approval threshold, save the work and stop expansion/new spending until confirmed. Account for in-flight work and the closure reserve.

Do not weaken valid gates and do not "hunt for cosmetic silence" to fake completion.

## 13. Handoff and final artifacts

- At the end of each **externally controlled phase**, when control is back with the root agent, it updates `progress.md`; internal steps of a still-running Workflow are recorded in `checks.md` and evidence instead. In the `progress.md` header record the **actual absolute path** to the normative `references/protocol.md`, taken from `${CLAUDE_SKILL_DIR}` as expanded when the Skill was invoked, and the requirement to **reread the normative protocol in full after context compaction or when continuing in a new session**. Do not write `${CLAUDE_SKILL_DIR}` literally and do not assume the Skill is installed inside the project: it may be in `~/.claude/skills/graph-task/`. If the saved path does not exist after relocation or reinstallation, invoke `/graph-task` again, determine the valid path and update the `progress.md` header before resuming. Record: ready/done/blocked, artifact versions and evidence paths, decisions and reasons, blocking findings and the attempt summary (its **source of truth is `checks.md`**), measured budget + unknowns, next step. Build the evidence index **at creation time**; do not copy large logs and media into the root context.
- **Rereading the protocol.** First Skill invocation in a session, context compaction, a new session — read this file **in full**. Continuing in the same session with context intact — **do not reread**. Recovering an interrupted Workflow in the same session with context intact — reread at least §§13–14 and the sections recovery depends on (§7 for external writes, §9 for admissions, §10 for returns); if context was lost — the whole protocol.
- After a restart, first reread the protocol per the rule above, then graph.md/progress.md, confirm budget and permissions, recheck already accepted versions against checks.md and continue from the last verified version without loading the whole history.
- Return `OUTPUT`, `graph.md`, `checks.md`, `progress.md`, the evidence/resource index. List pass/fail/unresolved. Keep the graph template for reuse. Separate observed resources from estimates; do not attribute savings to the prompt without measurement.

## 14. Dynamic Workflow mode — normative rules (Mode B)

This section is **mandatory** whenever Claude Code uses a native Dynamic Workflow. It supplements but does not weaken §§0–13. In mode A the root agent executes the same graph itself and records state; in mode B **a JavaScript script controls node launch order, branching and parallelism**, and an isolated runtime executes the script. The files `graph.md`, `checks.md`, `progress.md` keep the specification, verified results and handoff, and **do not become a second live dispatcher**.

### 14.1. Launch pre-contract and explicit approval

- One Workflow run is bound to **one approved `graph.md` revision and a specific bounded subset of nodes/phase**. In `graph.md` specify in advance: mode B, node IDs, dependencies, write permissions, real gate procedures, reviewer/judge role, phase plan, **maximum number of agents and of concurrently running agents**, total budget estimate and closure reserve.
- **After showing the graph, obtain separate explicit user permission to launch the specific Workflow phase with the stated agents/concurrency and spending bounds.** Calling `/graph-task`, the Skill text, approval of the general plan and Claude Code's automatic tool permission **do not replace** this consent. Do not enable Ultracode, auto-permissions or global settings to bypass approval. Without confirmation, keep the prepared plan and wait for permission; do not launch mode B.
- If user confirmation is needed *between* stages, split the work into several separate Workflows or choose mode A: a running script cannot ask for arbitrary approval mid-run. If a hard budget cannot actually be measured and stopped within one run, do not promise the limit in text: reduce/split the phase or work without a mass autonomous launch.

### 14.2. Recording state before and after launch

- **The script is saved before launch.** The root agent writes the JavaScript to `.agent-runs/<task-id>/workflow.js` (if large, with several Write/Edit calls), then launches the Workflow through Claude Code's standard mechanism **via `scriptPath` pointing to this file**, instead of passing the whole script in one response. The saved file pins the executed version and makes it checkable; it does **not guarantee** the absence of generation or launch failures.
- **Before calling the Workflow**, record in `progress.md`: task ID, revision/hash of the **actual** `graph.md`, **path and sha256 of `workflow.js`** and their binding to this graph revision, the chosen phase and nodes, agreed limits, assigned files and responsible writers, status `PREPARED`, and a separate authorization note. The single writer of `progress.md` is the **root agent**: after the Workflow completes, pauses or stops, **when control has returned to the root**, it adds the actually available run ID and the phase outcome. While execution continues, do not promise real-time `progress.md` updates. If the run ID is unavailable before control returns or is never provided, leave `unknown/pending`; **do not invent** it. If interrupted before control returns, `progress.md` may stay in `PREPARED`: recovery relies on the actually existing `checks.md`, artifact versions and evidence and, when available, the standard state of the original session.
- During execution, worker and gate agents, through permitted tools, save **only assigned artifacts and unique evidence fragments**; one designated aggregator, sequentially, writes admitted results into `checks.md`. **No Workflow agent modifies `progress.md`**. After control returns, the root agent reconciles `checks.md`, evidence, versions and budget, then saves the checkpoint in `progress.md` itself. The runtime script **has no direct filesystem/command access**; reading and writing are done only by the agents it calls, with their set permissions.
- While the Workflow executes the assigned nodes, the root agent **does not edit `graph.md`, run artifacts, `checks.md` or `progress.md`** and does not start competing execution of the same nodes. `progress.md` still belongs to the root, but its update is deferred **until control returns**, so that two inconsistent state loops do not appear. State can be monitored with native tools (`/workflows`), without substituting `progress.md` for them.

### 14.3. Gates, versions and safe writes in a Workflow

- The trial run from §8 is mandatory **before the first parallel fan-out of a phase**: one representative real node through the phase chain and all new/changed gates it needs, with a negative sample. Test a later-stage gate before its first dependent launch; an untested gate blocks its consumer. Fix harness errors before use; an accepted real trial and tested unchanged gates can be reused.
- On every risky edge the script **waits for an independent gate**: a separate checking agent/code check in the node reads the actually saved artifact, runs the check, writes evidence and returns a structured `PASS/FAIL/UNRESOLVED` together with the versions of significant inputs, output, contract/`CHECKS` and evaluator. The script parses the returned structure and **does not launch the consumer on FAIL, missing or unverified mandatory `CHECKS`**. No admission based on model confidence or producer text.
- **Input versions are passed along the chain.** The script obtains in advance (from an agent that computed them from the files) and passes to the gate and to the aggregator the expected versions of each node's significant inputs, the contract/`CHECKS` and the evaluator configuration. The gate recomputes them from the actual files and writes them into evidence. The aggregator checks the **validity** of the evidence, not just the presence of a field in the model's answer: the evidence file exists, the stated hashes match those recomputed from the files and the expected versions. If a version is missing, unverifiable or mismatched, the aggregator records `unverified`, not `PASS` (§9), and the dependent node does not start.
- Each output and evidence item is written by **one owner**. `progress.md` is written only by the root agent **between runs/phases**; Workflow agents never modify it. In parallel checks each worker/gate writes its own unique evidence fragment, and **one sequentially designated aggregator — the single writer of `checks.md` in this phase —** consolidates the results and marks admission **before** dependent nodes start. The consumer checks that the hash of the received file matches the verified and admitted version; a mismatch revokes the admission and requires a new check. Fragment names and derived paths are formed from stable node/version IDs; do not overwrite accepted artifacts without a documented version/revision.
- Implement the limit of **three correction returns per node across all phases and restarts** in the script code. Before each correction the **single `checks.md` aggregator** reads previous `correction-return` events, saves the next number **before** calling the correcting agent and returns the confirmed cumulative counter to the script. If saving fails, do not launch the correction; `progress.md` remains a summary. Initial production is not a correction return. Independent reviewer/judge by risk, not a mandatory repeat of every successful review. Route tool, specification or gate errors to their owners without charging the producer. A running script cannot ask the contract owner, so on a spec gap (§10) the gate returns `spec-gap`, the aggregator records the event, the node stops as `UNRESOLVED` without a correction return, and the root agent asks the question after control returns. Record global runtime retries (stalls/relaunch) separately when observable: they must not turn into hidden endless corrections.

### 14.4. Phases, budget and transitions

- Choose **one Workflow** only when the whole chain of mandatory gates, decisions and saving of **verifiable artifacts, evidence and admissions in `checks.md`** can be implemented **inside** the script through agents; the root updates the final `progress.md` after control returns. For risky integration, a need for approval, an ambiguous budget or long execution, prefer **several small Workflows by phase**, between which the root agent reconciles `checks.md`, artifacts and spending and prepares the next explicitly authorized launch.
- Record observed spending **per node and in total** when counters are available. A large fan-out does not provide a verifiable hard stop threshold by itself: `/workflows` warnings, agent-count limits and token estimates are **not** a financial watchdog. Reserve resources for verification/closure and stop new cost-bearing phases at the agreed threshold.
- `graph.md` is the approved specification; graph/contract changes require a documented new revision and approval for the affected phases. A running Workflow cannot be "re-tuned" mid-run without re-evaluating already accepted versions. `progress.md` describes checkpoints and handoff and does not invent an in-session status for every agent.

### 14.5. Stopping and resuming

- **Original session:** first use the standard `/workflows` or `claude --resume` to return to the same session and, if needed, relaunch the approved script. With context intact, reread at least §§13–14 and the related requirements (§13); if context was lost or compacted, the whole protocol. **Before resuming**, compare the sha256 of `workflow.js` and `graph.md` with those recorded in `progress.md`: resuming the same run (`resumeFromRunId`) is allowed only if both match. If the script or the graph changed, this is not a resume but a new run under a new revision and new consent (§14.1). When the prompt/call order changes or on an error, some **already completed** agents may run again. Native caching of a completed agent does not guarantee its files survived without a check.
- **New session:** do **not consider** the old executable Workflow state available for a new run. Read this protocol, then `graph.md`, `progress.md`, `checks.md`; check the whole admission binding in `checks.md` (significant inputs, output, contract/`CHECKS`, evaluator) and the cumulative returns; on a change or an unconfirmed version, revoke the admissions of the affected nodes and their dependents and **record the revocation in `checks.md`** (§9). Before repeating an external action, check the actual outcome in the target system per §7. Create a **new** Workflow **only for the remaining/rechecked nodes** under the approved revision and new explicit launch consent.
- **After any resume or relaunch**, recheck **significant inputs, result, contract/`CHECKS` and evaluator** before further use; a changed part revokes the previous PASS and the affected dependent admissions; the revocation is recorded in `checks.md` (§9). Before repeating an external write, apply §7. Do not allow parallel relaunches that change the same files. Keep old versions/diffs and the reason for the retry.
- If the state, tool availability, permissions, budget or reliability of a mandatory gate cannot be proven, stop with `UNRESOLVED` and give the exact next step instead of a fake acceptance.

## 15. Boundary between instructions and human documentation

The package contains `docs/guide.ru.html` (Russian) and `docs/guide.en.html` (English) — illustrated guides **for people**, which are not sources of normative rules. The agent **does not need to read them** to apply the Skill. Reading the HTML is allowed only on a separate request to explain/edit the documentation itself. `SKILL.md` and this `references/protocol.md` are normative. Advisor work, if separately enabled and available, does not change this boundary: a consultation is not a gate and does not replace verification.
