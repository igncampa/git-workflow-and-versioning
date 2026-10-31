---
name: git-workflow-and-versioning
description: Keep main current with user-approved work while enabling fast, project-local iteration. Use for every project change; for experiments, parameter tweaks, pipeline runs, comparisons, retained results, and review decisions; for Git branches, commits, merges, PRs, worktrees, and cleanup; and for releases, versioning, tags, and changelogs. Owns the unified run-and-review workflow without a separate experiments skill.
---

# Git Workflow and Versioning

## Operating Contract

Use for every project change, including documentation and configuration. `create-readme` owns stable project context and bootstrap; this skill owns changing code, running candidates, recording evidence, obtaining approval, and integrating accepted work. Development, pipeline runs, and experiments use one change-and-review workflow.

`main` means the established integration branch. Use `main` for a new repository; respect existing team naming and protected-branch rules.

- **Main is the latest explicitly approved state, not the latest implemented or attempted state.** A request to implement, a completed feature, a trial commit, or a successful run is not permission to merge.
- **Default to one checkout in the current project.** Switch task branches in place when safe. Do not create sibling worktrees, repository copies, or external output directories by default.
- **Keep one run log and one output root when retained runs are needed.** Reuse existing equivalents; otherwise use tracked `RUNS.md` and ignored `runs\<run-id>\` for outputs. No mandatory queue, per-experiment README, registry, or per-run manifest.
- **Review is a legitimate stopping point.** Preserve the candidate and its results for the user to inspect. Do not "finish" a trial by adopting it or cleaning away its evidence.

## Choose the Approval Boundary

| User intent | Work in | Stop condition |
|---|---|---|
| Implement a requested fix, feature, refactor, or documentation change | One normal task branch in place; use main only when the user explicitly authorizes it for that scope | Validate and stop **awaiting review**; do not merge without explicit approval |
| Try an idea, experiment, compare approaches, tune a value, or run a change for inspection | One `trial/<short-slug>` branch in place, reused across related attempts, including parameter-only trials | Save results and stop **awaiting review**; main and selected outputs stay unchanged |
| Generate another deliverable with an already-approved recipe, without requesting a review trial | The safe checkout of that approved code; no branch per run | Preserve and log the new result; do not replace selected results without authorization |
| Run routine tests, builds, smoke checks, or debugging commands | The current task branch | Normal development feedback; no run log or output archive unless retained evidence is the purpose |

A request to implement authorizes bounded development on a task branch, not integration into main. Do not ask whether to use a branch when the user does not specify: the safe default is a task branch. Merge only after the user explicitly says to merge to main, or supplies an explicit acceptance rule before execution; record that rule and its scope. "Try this", "tweak and run", and "let me inspect" authorize a candidate, not adoption. Passing checks, a completed implementation, positive feedback, silence, or the agent's own recommendation do not grant approval.

A CLI-only parameter change requested for inspection is still a trial. Unchanged tracked files do not make it an approved rerun.

Read `references\iteration-and-runs.md`, relative to this skill, for retained runs, comparisons, resuming a trial, review decisions, or result selection. It supplies the single logging/storage protocol for both experiments and ordinary reruns. Do not load it for unrelated development checks, or copy installed skill files into project documentation.

## Start Once, Then Iterate

1. Establish the intended project root, repository root, integration branch, upstream, current branch, staged/unstaged changes, and active worktrees. Read the README and only the task-relevant source and records. A monorepo's repository root is not automatically the current project's output root.
2. Preserve unrelated work and staging. Fetch the configured upstream when available and authorized; reconcile accepted main by fast-forward where possible. Do not guess a remote, hide divergence, or reset an occupied checkout.
3. Base new independent work on current accepted main and create a task branch unless the user explicitly authorized working on main for that scope. Resume an existing branch for continued feature or trial work instead of creating another branch or restarting from main. Record an explicitly requested historical or dependent baseline rather than silently including a different candidate's changes.
4. Choose the approval boundary above. Establish it once per task, not per edit. Refresh context when the checkout, relevant inputs, collaborators' work, or integration target changes.

If no suitable repository exists, use `create-readme`'s preflight for an actual project bootstrap. Do not silently initialize Git, initialize an unrelated parent, or bootstrap an installed skills directory merely because its files are being edited. If Git was declined, continue with honest unversioned records and preserved outputs; do not claim commit-based rollback or keep prompting.

## Workspace Rules

Sequential work uses the existing checkout. A branch names a line of code history; commits are save points. Neither is a directory or a copy of results. It is normal for the checkout to remain on a trial while the main ref still identifies approved code.

- Do not switch branches with running processes or edits that would be lost, carried into the wrong candidate, or attributed to another owner. Never auto-stash, commit, reset, or discard someone else's work.
- Worktrees are **opt-in** for real parallelism or a demonstrated isolation need, not a precaution for every trial. If safe in-place work is blocked, explain the conflict and obtain approval for isolation rather than silently creating another checkout.
- Once authorized, use one documented project-local container, normally `<project>\.worktrees\<task>`, ignored before creation. Create nothing outside the confirmed project root unless the user explicitly approves that destination. Existing approved external storage may be reused; do not invent a sibling directory or new storage service.
- Keep retained outputs in the stable project output root, not inside a disposable worktree. Resolve that root before changing checkout and pass it explicitly to executions in a worktree.
- Give each checkout a single writer. Parallel runs require independent write destinations; branches/worktrees do not isolate shared files, environments, databases, or external services.
- Create only paths needed for the current task. No empty experiment scaffolding, code copies named `v2` or `final`, mandatory task queues, or hidden archival directory trees.

Use existing branch conventions; otherwise use `feature/<slug>`, `fix/<slug>`, `refactor/<slug>`, `chore/<slug>`, and `trial/<slug>`. Reuse a trial branch until its review question is resolved. Do not number experiments, create a branch per attempt, or maintain permanent code branches just to keep alternate outputs. Accepted reusable variants belong in supported configurations on main.

## Save Points and Proportional Validation

Work in coherent increments: edit, run the smallest relevant existing check, and commit a useful save point. Keep behavior, regression coverage, and directly related README changes together. Do not create a commit per file, metric, or keystroke, or create a PR/release for each attempt.

- Before a retained measurement, commit the execution code and non-sensitive configuration and record the actual launch commit using the run reference. Unchanged execution code needs no empty snapshot commit. A deliberately failing candidate may be committed on its trial branch, with the limitation recorded.
- Finalize evidence after each attempt, including failures and aborts. Commit the log at useful completed-attempt or handoff boundaries. No queue publication, metadata merge to main, or remote push is required before starting a local trial.
- Separate trial implementation and evidence commits when they need different adoption or rollback treatment, especially when rejecting a trial. Do not require multiple bookkeeping commits when one serves the purpose.
- Batch related test selectors using the existing runner. Expand coverage only for relevant risk or failures. Do not rerun an expensive unchanged pipeline because a log changed, a branch was merged, or a save point was made.
- Documentation-only changes need content/path checks, not application builds, unless documentation tests exist. Use existing tools and hooks; do not install a testing/tracking framework to satisfy a generic workflow.
- Stage only owned paths/hunks, preserve unrelated staging, and inspect the complete staged diff for secrets, outputs, and other people's work. Do not blanket-stage a mixed checkout.

Follow repository commit conventions; otherwise use `<type>(<optional scope>): <intent>`, with `feat`, `fix`, `refactor`, `perf`, `test`, `docs`, or `chore`. Trial code can use the same types; `chore(runs): ...` suits evidence. Branches, recorded commands, and decisions provide trial context without a separate numbering system.

## Handoff for User Review

When a requested implementation, trial, or comparison reaches a review point:

1. Save the candidate and, when applicable, its exact run recipe, outcome, and inspection paths on the task or trial branch. Keep failures and limitations honest.
2. Leave main and any selected-result pointer unchanged. Leave the candidate branch checked out when safe so the user can inspect and rerun it in the same directory.
3. State **awaiting review**, the branch, and the exact result/report path, with the meaningful observation. Every implementation handoff must include the explicit reminder: **This work has not been merged into main.** Do not force an immediate decision or start further work unasked.
4. Keep the branch and outputs while review is pending. On a follow-up change, continue there and add a new run ID when applicable. If the user switches to independent work, preserve the candidate before safely changing branches.

The current branch's README and run log describe that candidate; main's README still describes accepted behavior. If resuming from main, inspect existing task/trial refs and their log via Git before creating a duplicate branch. Pending records need not be merged into main merely to be discoverable; their committed branch is the durable handoff.

## Integrate Only What Was Approved

Only for a candidate the user explicitly directed to merge to main, or a candidate satisfying a previously delegated acceptance rule:

1. Confirm the approval scope: implementation, selected results, or both. "Merge this" approves the reviewed code, not an unrelated result promotion. A new execution-relevant change after review needs renewed approval unless already covered by the user's rule.
2. Refresh main and review the full intended integration diff. Reconcile concurrent changes without rewriting shared history. Validate affected behavior; if reconciliation invalidates the inspected result, preserve it as historical and return the changed candidate for review.
3. Integrate approved implementation with its relevant documentation and evidence through the repository's established path: fast-forward where possible, otherwise an appropriate merge/cherry-pick or required PR. Adopt only the approved subset. Preserve original run commits before a squash, rewrite, or branch deletion could make them unreachable.
4. Keep selected-result compatibility intact. Merging code does not select outputs; selecting outputs does not merge code. Coordinate an explicitly authorized promotion if a schema change would otherwise break existing consumers.
5. Confirm integration actually reached main. With an authorized shared upstream, publish through the configured path and confirm availability there. Local-only repositories finish at local main; do not invent a remote or call local retention a backup.
6. Record the decision and any adopted-commit mapping in the run log when applicable. Then apply the retention gate and remove only closed, task-owned temporary branches/worktrees. Return the task-owned checkout to main when safe.

If approval exists but review policy, permissions, conflicts, or publication block integration, say **integration pending** with the blocker and exact ref. Do not confuse that blocker with a trial deliberately **awaiting review**, bypass protection, or claim a pushed branch/open PR is merged.

If the user closes a trial as rejected or abandoned, commit the factual outcome, archive the candidate under the retention gate, and integrate only its useful evidence and necessary log-routing/storage conventions. Do not merge rejected implementation or its candidate-only README changes. Rejection closes the code proposal, not permission to erase results. Rejection of one attempt while continuing the trial simply adds a decision to its log.

## Retention and Cleanup Gate

Before deleting a trial/task branch or worktree, or rewriting history containing recorded executions:

- Verify every recorded baseline/run commit and the evidence being retained is reachable from main or an immutable annotated archival tag. A SHA in Markdown, the object merely existing, or a reflog is not durable retention. For example, use `git merge-base --is-ancestor <run-commit> <retained-ref>`.
- Usually one tag such as `archive/<trial-slug>-<unique-suffix>` at the final candidate protects all its ancestor runs. Add `run/<run-id>` tags only for recorded commits not covered by retained refs. Never move existing archival tags; never substitute an adopted squash/cherry-pick SHA for the original launch SHA.
- Record archival refs and adoption mappings in the retained log. When shared publication is part of the authorized workflow, confirm the specific tags and evidence are available upstream before removing their last other refs. Do not push all tags indiscriminately.
- Inspect ignored/untracked outputs and task-owned processes. A clean Git status does not mean outputs are disposable. Retain artifacts in their recorded project-local or already-approved storage; if relocation is necessary, copy and verify before changing references or removing the old copy.
- Keep pending-review branches and any state whose preservation is blocked. Delete only closed, task-owned temporary state. Merging or rejecting code never authorizes deleting retained outputs; deletion needs explicit user authorization or an established retention policy.

Ordinary merged work with no additional run refs needs only the applicable parts of this gate; do not create archive tags for every routine change. Prefer `git revert` for rollback of committed shared changes. Do not use broad resets, forced worktree removal, blanket cleanup, or force-push to manufacture a tidy workspace.

## Generated Files and Releases

Commit generated files only when the project expects them, such as lockfiles or migrations. Exclude raw outputs, secrets, environments, build products, and private IDE state. Preserve `create-readme`'s Python/environment/local-data ignore baseline and existing allowlists; add scoped output and authorized worktree exclusions only when needed. Include relevant stack exclusions such as `node_modules/` and private keys.

Ignore protection must survive branch switches. If a new output/worktree ignore rule exists only on the trial branch, protect the same narrow path in the repository-local exclude file before switching to a branch without that rule. Use Git to locate `info\exclude` rather than assuming `.git` is a directory, preserve existing rules, and keep the intended tracked `.gitignore` change for integration. Do not advance main merely to ignore local artifacts.

A new run is not a software release and does not require a version bump. Archival tags preserve execution history, not consumer versions. Read `references\releases.md` only for software versioning, consumer-facing compatibility/changelogs, or a requested release. Do not publish a release because a task reached main.

## Completion

Finish in the state the user requested: **awaiting review** for implemented but unmerged work, **integrated** only after explicit merge approval, or an explicit blocker such as **integration pending**. Every implementation handoff that is not integrated states: **This work has not been merged into main.** Retained runs have an honest recorded outcome, exact known recipe/ref, and accessible outputs. Main never advances merely because implementation or a trial finished. Unrelated work is preserved, no unapproved external directories were created, and cleanup never destroys review or reproducibility state.
