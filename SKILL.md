---
name: git-workflow-and-versioning
description: Keep main at the latest work that the user approved. Let the agent iterate fast in the project. Use this skill for every project change. Use it for experiments, parameter changes, pipeline runs, comparisons, kept results, and review decisions. Use it for Git branches, commits, merges, PRs, worktrees, and cleanup. Use it for releases, versions, tags, and changelogs. This skill controls the full run-and-review workflow. Do not use a separate experiments skill.
---

# Git Workflow and Versioning

This skill uses ASD-STE100 Simplified Technical English (STE).

## Operating Contract

Use this skill for every project change. This includes documentation and configuration changes.

- `create-readme` controls the stable project context and the project bootstrap.
- This skill controls these tasks: change code, run candidates, record evidence, get approval, and integrate accepted work.
- Development, pipeline runs, and experiments use one change-and-review workflow.

`main` is the name of the integration branch that the project uses. For a new repository, use `main`. If the team uses a different name or protected-branch rules, obey them.

- **Main is the latest state that the user approved explicitly. Main is not the latest state that you implemented or tried.** These are not permission to merge: a request to implement, a completed feature, a trial commit, or a successful run.
- **By default, use one checkout in the current project.** When it is safe, change task branches in that checkout. By default, do not make sibling worktrees, copies of the repository, or output directories outside the project.
- **If you must keep runs, keep one run log and one output root.** If the project has an equivalent log or root, use it. If not, use a tracked `RUNS.md` file and ignored `runs\<run-id>\` directories for outputs. Do not make a mandatory queue, a README for each experiment, a registry, or a manifest for each run.
- **Review is a correct place to stop.** Keep the candidate and its results so that the user can examine them. Do not "complete" a trial when you adopt it. Do not remove its evidence to "complete" it.

## Choose the Approval Boundary

| User intent | Where to do the work | When to stop |
|---|---|---|
| Implement a fix, feature, refactor, or documentation change that the user requested | Use one normal task branch in the current checkout. Use main only when the user explicitly permits main for that scope. | Validate the work. Then stop and say **awaiting review**. Do not merge without explicit approval. |
| Try an idea, do an experiment, compare methods, tune a value, or run a change so that the user can examine it | Use one `trial/<short-slug>` branch in the current checkout. Use the same branch again for related attempts. This includes trials that change only parameters. | Save the results. Then stop and say **awaiting review**. Do not change main or the selected outputs. |
| Make one more deliverable with a recipe that the user approved, when the user did not ask for a review trial | Use the safe checkout of the approved code. Do not make a branch for each run. | Keep and log the new result. Do not replace selected results without permission. |
| Run usual tests, builds, smoke checks, or debug commands | Use the current task branch. | This is usual development feedback. Do not make a run log or an output archive unless the purpose is to keep evidence. |

A request to implement gives permission for limited development on a task branch. It does not give permission to integrate into main. If the user does not tell you which branch to use, do not ask. The safe default is a task branch.

Merge only in these conditions:

- The user explicitly tells you to merge to main.
- Before the work started, the user gave an explicit acceptance rule. Record that rule and its scope.

"Try this", "tweak and run", and "let me inspect" give permission for a candidate. They do not give permission to adopt it. These items do not give approval: passed checks, a completed implementation, positive feedback, no answer, or your own recommendation.

If the user asks for a change to a CLI parameter only, so that they can examine it, that change is a trial. If the tracked files do not change, it is still not an approved rerun.

Read `references/iteration-and-runs.md` (relative to this skill) for these tasks: kept runs, comparisons, continuing a trial, review decisions, or result selection. That file gives the one logging and storage protocol for experiments and for usual reruns. Do not read it for development checks that are not related. Do not copy installed skill files into project documentation.

## Start Once, Then Iterate

1. Find these items: the intended project root, the repository root, the integration branch, the upstream, the current branch, staged and unstaged changes, and the active worktrees. Read the README. Read only the source files and records that are related to the task. In a monorepo, the repository root is not always the output root of the current project.
2. Keep all work and staging that are not related to your task. If an upstream is configured, and you have permission, fetch it. Bring accepted main up to date with a fast-forward when possible. Do not guess a remote. Do not hide divergence. Do not reset a checkout that is in use.
3. Start new independent work from the current accepted main. Make a task branch, unless the user explicitly permitted work on main for that scope. To continue feature or trial work, use the existing branch again. Do not make a new branch and do not start again from main. If the user explicitly asks for a historical or dependent baseline, record it. Do not silently include changes from a different candidate.
4. Choose the approval boundary from the table above. Do this one time for each task, not for each edit. Get the context again if one of these items changes: the checkout, the related inputs, the work of other persons, or the integration target.

If there is no applicable repository, use the preflight in `create-readme` to bootstrap a real project. Do not silently initialize Git. Do not initialize a parent directory that is not related. Do not bootstrap an installed skills directory only because you edit its files. If the user declined Git, continue with correct records that have no version control, and keep the outputs. Do not say that rollback with commits is possible. Do not ask about Git again.

## Workspace Rules

For sequential work, use the existing checkout. A branch is a name for a line of code history. Commits are save points. A branch is not a directory. A branch is not a copy of results. The checkout can stay on a trial branch while the main ref continues to point to approved code. This is normal.

- Do not change branches if processes are running. Do not change branches if this can cause edits to be lost, to go into the wrong candidate, or to look like the work of a different owner. Never automatically stash, commit, reset, or discard the work of a different person.
- Use worktrees **only when you have a reason**: real parallel work or a shown need for isolation. Do not make a worktree as a precaution for each trial. If you cannot safely work in the current checkout, tell the user about the conflict. Get approval before you isolate the work. Do not silently make a different checkout.
- After you get permission, use one documented container in the project. Usually this is `<project>\.worktrees\<task>`. Add it to the ignore rules before you make it. Do not make items outside the confirmed project root unless the user explicitly approves that location. You can use external storage again if the user approved it before. Do not make a new sibling directory or a new storage service.
- Put kept outputs in the stable output root of the project. Do not put them in a worktree that you will remove. Find that root before you change the checkout. Give that root explicitly to each execution in a worktree.
- Each checkout must have only one writer. Parallel runs must write to independent locations. Branches and worktrees do not isolate shared files, environments, databases, or external services.
- Make only the paths that the current task needs. Do not make empty experiment structures, code copies with names such as `v2` or `final`, mandatory task queues, or hidden archive directory trees.

If the project has branch name conventions, use them. If not, use `feature/<slug>`, `fix/<slug>`, `refactor/<slug>`, `chore/<slug>`, and `trial/<slug>`. Use the same trial branch until the user answers its review question. Do not give numbers to experiments. Do not make a branch for each attempt. Do not keep permanent code branches only to keep alternative outputs. Put accepted variants that the project will use again into supported configurations on main.

## Save Points and Proportional Validation

Work in increments that have one clear purpose. Edit the code. Run the smallest related check that exists. Commit a useful save point. Keep behavior changes, regression tests, and directly related README changes together. Do not make a commit for each file, metric, or keystroke. Do not make a PR or a release for each attempt.

- Before a measurement that you will keep, commit the execution code and the non-sensitive configuration. Record the actual launch commit with the run reference. If the execution code did not change, do not make an empty snapshot commit. You can commit a candidate that you know will fail on its trial branch. Record the limitation.
- After each attempt, complete the evidence. This includes failures and aborts. Commit the log when an attempt is complete or at a handoff, when this is useful. Before you start a local trial, you do not have to publish a queue, merge metadata to main, or push to a remote.
- If the trial code and the evidence need different adoption or rollback, put them in separate commits. This is important when the user rejects a trial. If one commit is sufficient, do not make more bookkeeping commits.
- Use the existing test runner, and run related test selectors together. Add more test coverage only for a related risk or for failures. Do not run an expensive pipeline again if it did not change. A change to a log, a merged branch, or a save point is not a reason to run it again.
- For changes to documentation only, check the content and the paths. Do not build the application unless documentation tests exist. Use the existing tools and hooks. Do not install a test or tracking framework only to obey a general workflow.
- Stage only the paths and hunks that you own. Keep staging that is not related to your work. Examine the full staged diff for secrets, outputs, and the work of other persons. Do not stage all files in a checkout that has mixed changes.

If the repository has commit conventions, obey them. If not, use `<type>(<optional scope>): <intent>`. The types are `feat`, `fix`, `refactor`, `perf`, `test`, `docs`, and `chore`. Trial code can use the same types. Use `chore(runs): ...` for evidence. Branches, recorded commands, and decisions give the context for a trial. Do not use a separate number system.

## Handoff for User Review

When a requested implementation, trial, or comparison gets to a review point, do these steps:

1. On the task or trial branch, save the candidate. When applicable, also save its exact run recipe, its outcome, and the paths that the user can examine. Report failures and limitations correctly.
2. Do not change main. Do not change a selected-result pointer. When it is safe, keep the candidate branch checked out. Then the user can examine it and run it again in the same directory.
3. Say **awaiting review**. Give the branch, the exact path to the result or report, and the important observation. Each implementation handoff must include this explicit reminder: **This work has not been merged into main.** Do not make the user decide immediately. Do not start more work if the user did not ask for it.
4. Keep the branch and the outputs while the review is pending. If the user asks for a subsequent change, continue on that branch. When applicable, add a new run ID. If the user changes to independent work, keep the candidate safe before you change branches.

The README and the run log on the current branch describe that candidate. The README on main continues to describe the accepted behavior. If you continue from main, use Git to examine the existing task and trial refs and their logs before you make a branch. This prevents duplicate branches. You do not have to merge pending records into main so that persons can find them. Their committed branch is the permanent handoff.

## Integrate Only What Was Approved

Do these steps only for a candidate that the user explicitly told you to merge to main, or for a candidate that obeys an acceptance rule that the user gave before:

1. Make sure that you know the scope of the approval: the implementation, the selected results, or the two. "Merge this" approves the code that the user examined. It does not approve the promotion of a different result. If you make a new change that has an effect on execution after the review, you must get approval again. The only exception is when the user's rule includes that change.
2. Bring main up to date. Examine the full integration diff. Merge concurrent changes without a rewrite of shared history. Validate the behavior that the change affects. If the merge makes the examined result incorrect, keep that result as history. Then give the changed candidate back to the user for review.
3. Integrate the approved implementation, its related documentation, and its evidence. Use the path that the repository usually uses. Use a fast-forward when possible. If not, use an applicable merge, a cherry-pick, or a PR if the repository requires one. Adopt only the part that the user approved. Before a squash, a rewrite, or a branch deletion, keep the original run commits reachable.
4. Do not break the compatibility of selected results. A code merge does not select outputs. A selection of outputs does not merge code. If a schema change will break existing consumers, coordinate a promotion only when the user explicitly permits it.
5. Make sure that the integration actually got to main. If you have permission to use a shared upstream, publish with the configured path. Then make sure that the work is available there. For a repository that is only local, the work is complete at local main. Do not invent a remote. Do not say that a local copy is a backup.
6. When applicable, record the decision and the map of adopted commits in the run log. Then apply the retention gate. Remove only the temporary branches and worktrees that are closed and that your task owns. When it is safe, put the checkout of your task back on main.

Sometimes approval exists, but review policy, permissions, conflicts, or publication stop the integration. In that condition, say **integration pending**. Give the blocker and the exact ref. Do not mix up this blocker with a trial that is **awaiting review** on purpose. Do not go around branch protection. Do not say that a pushed branch or an open PR is merged.

If the user closes a trial as rejected or abandoned, do these steps. Commit the factual outcome. Archive the candidate with the retention gate. Integrate only its useful evidence and the necessary log routing and storage conventions. Do not merge the rejected implementation. Do not merge README changes that apply only to the candidate. A rejection closes the code proposal. A rejection does not give permission to remove results. If the user rejects one attempt but continues the trial, add the decision to the trial log only.

## Retention and Cleanup Gate

Do these checks before you delete a trial branch, a task branch, or a worktree. Also do them before you rewrite history that contains recorded executions.

- Make sure that each recorded baseline commit, each recorded run commit, and the evidence that you keep are reachable from main or from an immutable annotated archive tag. These are not permanent retention: a SHA in Markdown, an object that only exists, or a reflog entry. For example, use `git merge-base --is-ancestor <run-commit> <retained-ref>`.
- Usually one tag at the final candidate keeps all of its ancestor runs. An example name is `archive/<trial-slug>-<unique-suffix>`. Add `run/<run-id>` tags only for recorded commits that no kept ref includes. Never move existing archive tags. Never use the SHA of an adopted squash or cherry-pick in place of the original launch SHA.
- Record the archive refs and the adoption maps in the kept log. If shared publication is part of the approved workflow, make sure that the upstream has the specified tags and evidence. Do this before you remove their last other refs. Do not push all tags without selection.
- Examine ignored and untracked outputs and the processes that the task owns. A clean Git status does not mean that you can discard the outputs. Keep the artifacts in their recorded project storage or in storage that the user approved before. If you must move them, copy them first and make sure that the copy is correct. Then change the references, and then remove the old copy.
- Keep branches that are pending review. Keep all state that you cannot safely preserve yet. Delete only temporary state that is closed and that the task owns. A merge or a rejection of code never gives permission to delete kept outputs. To delete them, you must have explicit user permission or an established retention policy.

For usual merged work that has no additional run refs, do only the applicable parts of this gate. Do not make archive tags for each usual change. To roll back committed shared changes, use `git revert` when possible. Do not use broad resets, forced worktree removal, cleanup of all items, or force-push to make the workspace look clean.

## Generated Files and Releases

Commit generated files only when the project expects them. Examples are lockfiles and migrations. Do not commit raw outputs, secrets, environments, build products, or private IDE state. Keep the ignore baseline from `create-readme` for Python, environments, and local data. Keep existing allowlists. Add ignore rules for specified outputs and for permitted worktrees only when necessary. Include the ignore rules for the related stack, for example `node_modules/` and private keys.

Ignore protection must continue after you change branches. Sometimes a new ignore rule for outputs or worktrees exists only on the trial branch. In that condition, add the same narrow path to the local exclude file of the repository before you change to a branch that does not have that rule. Use Git to find `info\exclude`. Do not think that `.git` is always a directory. Keep existing rules. Keep the planned change to the tracked `.gitignore` file for integration. Do not move main forward only to ignore local artifacts.

A new run is not a software release. A new run does not need a version bump. Archive tags keep execution history. They are not versions for consumers. Read `references/releases.md` only for these tasks: software versions, compatibility or changelogs for consumers, or a release that the user requested. Do not publish a release only because a task got to main.

## Completion

Finish in the state that the user requested:

- **awaiting review** for work that you implemented but did not merge.
- **integrated** only after explicit approval to merge.
- An explicit blocker, for example **integration pending**.

Each implementation handoff that is not integrated must say: **This work has not been merged into main.** Each kept run must have a correct recorded outcome, the exact known recipe or ref, and outputs that the user can get to. Main never moves forward only because an implementation or a trial is complete. Work that is not related to your task stays safe. You did not make external directories without approval. Cleanup never removes the state that the user needs for review or to reproduce results.
