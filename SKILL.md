---
name: orca-supervised-orchestration
description: Orca workers (Antigravity) under Hermes supervision.
version: 2.0.0
author: AndrewDevos1, Hermes Agent
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [orca, antigravity, orchestration, supervised]
    related_skills: [hermes-kanban-goal-queue]
---

# Orca Supervised Orchestration

Hermes coordinates; Orca workers (agent `antigravity`) execute. Uses only native Orca CLI (`orca orchestration ...`). No server, daemon, cron, own Kanban. Existing Kanban card stays source of truth; never duplicate it.

## When to Use

- User asks for Orca/Antigravity worker on an authorized card.
- Don't use for: CFT profile cleanup, canonical DB, push, merge, deploy, cutover (need separate explicit authorization).

## Prerequisites

- `terminal(command="orca status")` shows runtimeReachable: true.
- Fetch guide once: `terminal(command="orca skills get orchestration")`; references via `--reference references/<file>.md`.
- Repo registered in Orca: `orca repo list --json`.

## Rules

1. WIP=1 global: one executor per card. Never start second worker on a card with an open Dispatch.
2. Spec self-contained: target, change, allowed files, PROTECTED paths, evidence required, forbidden actions (no push/merge/deploy/DB/credentials).
3. Bind card id inside the self-contained spec and Run objective; link `worker_done` to the card by task_id/dispatch_id only. Use optional CLI flags only when the installed guide confirms them.
4. Never poll; end turn after `worker-start`; Orca wakes you.
5. No bypass of permissions/security. Failure: stop, keep evidence, report cause; no workaround chain.
6. Worker edits only in its own worktree (`new-top-level` for real work, never productive worktrees for lab).

## Procedure

### Read-only diagnosis before a pilot

1. Before a read-only diagnosis, create an Orca-managed isolated worktree directly from the approved full candidate SHA: `orca worktree create --repo id:<repo> --base-branch <40-char-sha> --no-parent --name <purpose> --json`. Verify the actual HEAD and empty `git status --porcelain`. Never reuse a productive worktree or copy a database.
2. Inventory the exact runner, SQL, logs, and candidate sources needed by the question before dispatch. If the reported runner or SQL is absent at the approved SHA, report the exact missing artifact; do not infer its contents from another revision.
3. Scan only the proposed source allowlist for credential signatures before dispatch. A clean scan does not authorize `.env`, credential, runtime, database, or network access.
4. When dispatch is blocked by workspace trust or Windows/WSL bridging, do not retry `worker-start` or change trust/configuration. Collect `orca status`, `orca repo list --json`, `orca orchestration run-list`, `orca orchestration worker-list`, `orca worktree ps`, plus `orca orchestration worker-show --dispatch <dispatch_id> --json`; record its `stage` and `lastError` verbatim.
5. Classify independently: a workspace-trust rejection occurs before worker execution and requires a future interactive confirmation for that exact Windows-visible workspace path; a Windows/WSL transport error is a separate infrastructure finding. Never infer that accepting trust repairs transport, or vice versa.
6. A failed dispatch with `liveness=exited` and `terminalState=released` is settled history, not active WIP. Preserve it; release only the exact residual terminal when the native result directs it. Do not retry, reset, stop, abandon, or reuse it during diagnosis.
7. For a future pilot, propose the smallest separately authorized scope: one interactive trust confirmation for that exact path, then one supervised no-bypass dispatch. Keep the original worktrees untouched until the pilot is authorized and succeeds.

1. Reconcile authority before dispatch: read the native Kanban card (`show --json`, `log`), linked issue and current gate issue/comments; inspect target worktree HEAD/status, `orca orchestration worker-list`, and `orca worktree ps`. Latest owner decision overrides stale card summaries; a card marked `blocked` or a gate requiring an exact separate authorization is not dispatchable.
2. Preflight: `orca status`; `orca orchestration run-list`; `orca worktree ps`. For a Windows-host workspace called from WSL, also run one bounded harmless Win32 probe and compare bounded `orca status --json` from WSL and native PowerShell. Done when runtime ready, both paths identify the same reachable runtime, no active executor exists for the card, the card is eligible, and every operation in the worker spec is explicitly authorized. If the bridge probe or either status fails, classify `TEST_INFRA_BLOCKED` before `worker-start`; preserve the error and do not infer workspace-trust status.
3. Run: `orca orchestration run-create --objective "<card id + goal>"`. Do not reuse a laboratory Run for product work; bind a new product Run.
4. Single task: use the syntax returned by the installed guide, currently `orca orchestration worker-start --spec "<self-contained task>" --worktree <selector> --agent antigravity --json`; do not invent `--spec-file`, `--task-title`, or undocumented task flags. For diagnosis, whitelist paths and forbid writes, tests, DB/container/network/configuration commands, credentials, and commands outside that worktree. Require before/after clean `git status --porcelain`; end turn after the receipt.
5. Dependent tasks: `task-create --spec ... --deps <json_array>`; `task-list --ready --brief`; `worker-start --task <id> ...`. Validate task N output (diff, tests) before starting N+1.
6. For a wake-delivery test, record the dispatch receipt, then end the coordinator turn immediately. PASS only when Orca delivers an inbound wake without a human prompt or manual state lookup; after that wake, run `orca orchestration check --run <run_id>` exactly once. If work completed but recognition followed human prompting, inbox inspection, or manual reconciliation, record `PARTIAL_WAKE_NOT_PROVEN`.
7. On a legitimate wake or explicit mailbox notice: match each `worker_done` to task_id/dispatch_id; inspect the persisted task result plus exact worktree bytes, `git status --short`, tracked diff and untracked paths. An untracked deliverable has an empty `git diff`; verify it separately before accepting. Any change to a protected path not authorized = REJECT; do not approve; report.
8. `escalation`/`question`: answer via `orchestration reply` only if spec covers it; else ask user.
8. Report per task: outcome, evidence, blockers. When a staged runner fails before a requested downstream assertion, label that assertion `NOT_RUN`; do not report it as a product failure. Preserve the first failing stage, sanitized stderr hash, teardown result, and the smallest read-only state probe needed to distinguish fixture/bootstrap from product behavior. Release: `worker-release` when settled.

## Local acceptance retests

1. Freeze the effective runner, authorization artifact, exact command, and SHA256 manifest before execution; validate all hashes and syntax first.
2. Evaluate the native execution policy against that exact command. Execute only on `ALLOW`; present the exact command on `ASK-APPROVAL`; stop on `DENY`.
3. Keep disposable DB/container resources owner-labelled and verify teardown after every attempt. Before setup DDL, probe the exact DB client transport and authentication with `psql -h 127.0.0.1 -v ON_ERROR_STOP=1 -U postgres -d <synthetic_db> -c 'SELECT 1;'`; `pg_isready` alone does not prove the later `psql` invocation can connect. A passing fixture substage does not prove downstream RBAC or cross-tenant assertions.
4. On the first stage failure, preserve its structured result and stop. Before changing a fixture, use one separately authorized read-only, sanitized database-state probe to establish whether bootstrap state meets the runner's precondition. If readiness passes but the exact client probe fails, classify it as harness/connection transport and inspect container state before SQL or RBAC.

## Pitfalls

- Do not collapse a fixture/bootstrap failure into an RBAC or tenant-isolation result; downstream assertions never ran, so report them `NOT_RUN` and inspect the prerequisite state first.
- Regenerate the approval artifact whenever the effective runner changes; runner and artifact hashes are a single execution contract.

## Verification

- `skills_list` shows the skill; each worker_done maps to one task_id; verify both tracked and untracked changes; protected-path diff empty or REJECTED.
- For a wake test, preserve receipt timestamps/IDs and classify delivery separately from execution and artifact validation.
- For a local retest, preserve manifest validation, policy verdict, sanitized stage result, and owned-resource teardown evidence.

## Pitfalls

- Plain `check` by a script steals the mailbox; invoke it only for an explicit mailbox notice, then handle the returned messages once.
- Silence/timeout never proves exit; don't recover autonomously (`orca skills get orchestration --reference references/recovery-and-cleanup.md`).
- `worker-start` failure: do not rerun unchanged; read the native error. Existing worktrees reject creation-only flags such as `--setup`; reconcile Run/Dispatch first, then issue one minimally corrected command only if no Task/Dispatch was created.
- A visible `worker_done` in `inbox` is a legitimate indication to run `check --run <run_id>` once; use `inbox` for recovery discovery, never polling. It does not prove automatic wake delivery when a human prompted the lookup.
- Use `run-show --id <run_id>`, not `--run`; command-specific flags are authoritative.
- A laboratory Run and its settled workers prove the mechanism only; never treat them as an active product dispatch or reuse them for product work.
- `agent-trust-workspace` is a pre-execution human gate for the exact Windows-visible path. Preserve the failed Run/Task/Dispatch and present that path for official trust; never bypass, silently grant trust, or redirect the work to another workspace.
- T4 may use a deterministic synthetic protected fixture without a worker. Commit the baseline and evidence separately; preserve the deliberately rejected protected-file diff uncommitted, never promote it, reset it, or call the lab clean until the owner decides.
- An automatic wake proves native delivery only if it reaches Hermes without human prompting. A mailbox reconciliation after VSOCK failure is functional recovery evidence, not proof of autonomous post-failure recovery.
- A general pilot authorization does not authorize a concrete gated operation; preserve `WAITING_HUMAN` when the linked gate requires an exact command or runtime operation.
- Orca runs on the Windows host; repo paths are `C:/...` there. Treat `\\wsl.localhost\...` as a cross-boundary workspace: verify its exact Windows-visible path and required interactive trust before any dispatch; do not mutate trust in diagnosis-only work.

## Verification

- `skills_list` shows the skill; each worker_done maps to one task_id; verify both tracked and untracked changes; protected-path diff empty or REJECTED.
- For a wake test, preserve receipt timestamps/IDs and classify delivery separately from execution and artifact validation.
