# Iteration, Runs, and Review

Read with the Git skill for experiments, parameter tweaks, retained pipeline results, comparisons, and their review decisions. These all use the same small run record; the user's approval boundary determines whether work may integrate. Routine development tests, builds, and debugging need no run tracking.

The fast path is: **branch in place if this is a trial -> save the candidate -> run into a fresh destination -> log the outcome -> let the user inspect -> integrate only what is approved**. No queue registration, pre-run merge to main, or separate experiment directory is required.

## One Log, One Output Root

Enter through the project README, then read only the relevant run entries and source. Reuse an existing log/output mechanism rather than building a competing registry. For a project without one, the complete default layout is:

```text
<project>\
  README.md                 # stable context and commands
  RUNS.md                   # tracked recipes, outcomes, and review decisions
  runs\                     # ignored outputs only; created when needed
    <run-id>\               # this attempt's actual outputs/logs
```

There are no per-experiment READMEs, `RESULTS.md` databases, required per-run manifests, sequential queue IDs, or extra `artifacts\` layers in the default. Keep reusable code/configuration in its normal source locations, versioned on the candidate branch. Put small one-off non-secret overrides in the recorded command or log, not in a new configuration hierarchy.

Create the log for the first retained attempt, not during unrelated onboarding. Create output directories only if the execution writes files; a metric-only attempt can be recorded entirely in the log. One folder per file-producing attempt is the necessary preservation boundary, not a second copy of the project. Use the existing runner's internal output structure without adding another wrapper layer.

When first introducing the log or output root, wire the README handoff and scoped ignore rules on the current task branch before execution. Keep that supporting context with the candidate; a pending trial does not require publishing it on main. Apply the Git skill's cross-branch ignore protection before leaving a branch.

The output root belongs to the confirmed project, not its parent, an arbitrary shell working directory, or a disposable checkout. Resolve it before launching; pass the destination explicitly if the working directory changes. Do not use siblings, a system temporary directory, or session storage for the sole retained copy. Reuse approved external storage only when it is already part of the project's contract or explicitly authorized.

## The Smallest Useful Run Record

`RUNS.md` needs only a short `## Conventions` section with the project's output path, approval rule, and selection authority if one exists, followed by `## Runs`. Do not duplicate the project setup guide. Project records must be usable without knowing where these skills are installed.

Append one entry per attempt. Use a Windows-safe UTC timestamp, readable slug, and collision-resistant suffix, for example `20260908T170012Z-threshold-7c2e`. Reuse a task branch across attempts, never a run ID or writable output destination. Link a prior attempt when comparing, retrying, or deriving results; a comparison link does not imply its files were consumed.

```markdown
### <run-id> - <short intent>
- State: running
- Started (UTC): <timestamp>
- Change / comparison: <what differs; prior run ID or none>
- Branch / baseline: <task branch; accepted or explicit baseline commit>
- Run commit: <actual HEAD at launch, or unavailable with reason>
- Command: <exact invocation, shell, and working directory>
- Inputs / parameters: <input identities; resolved non-secret overrides; consumed run IDs>
- Environment: <runtime; lockfile/environment reference; relevant seed/model/hardware>
- Outputs / inspect: <output root and key report/files, or inline measurements>
- Outcome: pending
- Review: awaiting review | no adoption requested
```

Write starting facts before execution. On exit, fill State (`succeeded | failed | aborted`), append the end time, exit status, actual measurements/observations, key artifact inventory or inventory reference, and known reproducibility limits under Outcome. Update Review only after a decision and append a dated decision note with who authorized it, its scope (code/results/both), reason, and actual integration/archive refs or selection change. Further corrections and decisions are dated additions; do not rewrite the original recipe or past measurements. Summary fields may reflect the latest factual state without erasing these events.

Keep detail proportional but reproducible: an exact command plus a committed config/lockfile reference can replace pages of repeated settings. Record input versions/checksums and immutable retrieval references when available; a checksum detects change but does not retain the input. Note mutable/unavailable inputs or environments honestly. Record relevant output checksums or an existing inventory, especially for comparisons and preservation. Do not hash unrelated datasets or install a tracking service for a one-value trial.

Never record credentials, secret values, private input contents, or sensitive output excerpts in Git. Use environment-variable placeholders for secrets and approved non-sensitive identifiers for inputs. Never report an unmeasured value as zero or an estimate as measured. Imported prior results use State `imported`, the import time and known locations, with unknown execution facts explicitly marked; they are not newly verified successful executions.

## Before Launch

1. Identify the requested result or review question and the smallest useful run/comparison. Reuse a suitable recorded baseline when its relevant inputs and recipe match. A one-value trial does not require an automatic baseline rerun, parameter sweep, or new experiment plan. Observe the user's run/time/compute limits; note follow-up ideas in the current log or an existing tracker rather than creating a queue.
2. Follow the Git skill's branch and approval rules and prepare the candidate. If a trial requires no code changes, its branch can initially point at main; no empty commit is needed.
3. Inspect all writes, not just final outputs: intermediates, logs, mutable caches, databases, fixed exports, and external sinks. Point run-owned mutable state at the new destination using existing options. For destructive fixed paths, make a scoped output-root change on the task branch when within the request, or explain the blocker. Proven-safe shared read-only or content-addressed resources may be reused; shared/external writes still need authorization. A worktree does not isolate hard-coded paths, links, shared environments, or services.
4. Preserve any old results that could be overwritten. Prefer leaving them intact and redirecting the new run. If a legacy fixed location must be replaced, retain a copy/snapshot in the existing output root, verify it, and log its known provenance as an import before the write. Do not move or reorganize unrelated old outputs just to normalize the layout.
5. Reserve an unused run ID and, for file-producing commands, atomically create its previously nonexistent destination before launch. Fail on a collision; never empty, reuse, or point at a selected/prior run's directory. Retries get new IDs. Concurrent runs must reserve independent destinations and have independently safe writers.
6. Commit the execution code, scripts, non-sensitive configuration, and supporting README/ignore changes; capture the actual launch HEAD and persist the starting record as below. Start only when write isolation is real and no execution changes remain uncommitted. Do not silently copy the repository or launch another worktree to hide an isolation problem.

### Exact Launch State Without Extra Checkouts

The run commit is the execution checkout's actual `HEAD` at launch, after all pre-run code/config commits, not an earlier baseline or the later evidence commit. Execution code/configuration must be clean, and no other writer may change it during the run. Never include unrelated dirty code in a candidate snapshot.

Normally capture HEAD, append the starting record to the run log, and leave only those declared record edits uncommitted until the run finishes. This preserves the exact launch SHA without a self-referential commit hash or another worktree. If the application inspects Git dirty state, keep the checkout clean by persisting the starting recipe and HEAD in an ignored log inside that run's output directory, then append it to the tracked log after execution. Record which method was used if it affects behavior.

If Git was explicitly declined, record refs as unavailable and retain the non-secret recipe and outputs. Preserve a necessary source/config snapshot within the same retained storage when possible, not a second project copy; state any code-recovery limits instead of claiming Git reproducibility.

## Run, Inspect, and Continue

Run only the requested scope. For long-running work, persist the task-owned process/runner identifier with the starting record so a later session can reconcile it. Do not switch or mutate the execution checkout while it is active.

On exit, establish success from the actual exit status and expected output integrity, not the mere existence of files. Finalize the log and commit the outcome on the task branch, including partial output and failed/aborted attempts. After an interruption, inspect the actual process and files before declaring abortion or retrying. Never start a duplicate run just because the previous chat ended.

For an inspect-first task, stop **awaiting review** with the candidate branch and the exact report or output location. Keep the branch checked out and leave main and selected results alone. Another tweak continues the same trial with a new attempt; the previous output remains available for comparison. A request for review is not a failure to complete the task.

An ordinary rerun of already-approved code may save its factual evidence on main when the request and repository policy authorize it. That is permission to record the run, not to adopt a new implementation or replace a selected result. Do not turn a routine deliverable into a trial queue or require a branch per output.

Records on an open trial branch are authoritative for that pending work; they do not need pre-publication on main. When resuming from another branch, read the original branch's log through Git rather than copying its record into a second authority. Reconcile independent appended entries when integrating parallel branches; never drop another run to resolve a log conflict.

## Decide What to Adopt

The user may accept code, select results, accept both, request another attempt, or reject/abandon the trial. Record the scope explicitly. Use the Git skill's integration/retention gates; do not treat the best metric or a successful exit as approval.

An approval applies to the reviewed candidate and result. Before integration, compare the current branch with the recorded execution state: evidence-only edits do not invalidate it, but changes to executed code, dependencies, configuration, or relevant inputs may. Do not silently merge an uninspected later tweak under an earlier approval.

On code approval, integrate the approved implementation, stable README changes, and run evidence. Keep the actual launch SHA and any archival/adopted-commit mapping. **Do not rerun merely because code was merged or squashed.** The inspected artifacts can be retained or selected if they are still the approved, compatible result; rerun only when relevant execution state changed or the user requests it.

For a CLI-only trial, approval to make its parameter change permanent must reach the existing canonical configuration or supported invocation, not just the run log. Promote only the approved values and confirm the resulting effective recipe matches the inspected one. A log-only merge is not an applied configuration change.

On closure without code adoption, preserve the candidate with an archival ref, then integrate the factual evidence and the minimal README/log-storage routing needed to find it, not rejected code or candidate-only documentation. Keep old results. If review remains pending, do not close, archive away, or delete the live trial merely to put the checkout back on main.

When integrating evidence selectively, apply only the relevant entries and convention changes. Preserve main's other runs and selected-result authority rather than overwriting them with an entire stale candidate log.

### Selecting a Result Is Optional and Separate

Not every project needs a selected/default result. Do not invent a `latest` directory, series registry, pointer file, or selection database just to track runs. Producing a newer result never selects it automatically.

If selection is needed, use the project's existing consumer pointer/configuration as the sole authority and record the event in the run log. If there is no mechanism and selection is for human/agent use, a small `## Selected` table in the same log is sufficient: `Purpose | Run ID | Location | Reason`. Add it only when selection is actually requested; use one `default` row unless multiple named deliverables are needed. Do not maintain a second current pointer in the README.

For an authorized selection, require a successful run or verified import, complete/readable artifacts, and consumer compatibility; record the old/new reference and decision, and update the actual authority through the approved integration/publication path. Recheck the previous value against current main before applying concurrent decisions. A machine pointer must not move before required metadata integration; use the existing atomic update mechanism and report a partial failure instead of claiming consumers switched. Do not add a deployment/pointer system for manual inspection.

Selection references preserved outputs; it does not rewrite them. A required fixed-path materialized export is an explicitly authorized publication step after preserving the old contents, not the canonical run store. Rollback selects a retained compatible result and records the change; it does not reset Git or delete newer attempts. Result-only acceptance does not implicitly authorize the trial's code.

## Preservation Without Directory Sprawl

Completed artifacts are immutable. Corrections/recomputations create another attempt; a branch switch, merge, rejection, or cleanup must not overwrite them. Git preserves code and the log, not ignored outputs. Keep artifacts and required inputs in persistent project-local or approved storage; local retention is not an off-machine backup.

When relocation is necessary, copy to the intended retained location, verify the relevant inventory and checksums, update the recorded location and consumers, and only then remove a redundant task-owned copy. Never remove the sole copy with a worktree. Deliberate result deletion requires explicit authorization or an established policy; append what became unavailable and why.

Keep active proposals as branches and closed alternatives as archival Git refs when needed, not directory snapshots or permanent code variants. Do not create archive folders, preserve duplicate full checkouts, or delete result history in the name of tidiness.

## Existing Projects and Legacy Records

This is a forward default, not permission to reorganize every existing project.

- Reuse a capable existing run log, registry, and output layout. Use its actual paths and compatible fields; do not create `RUNS.md` alongside an equivalent authority.
- With old split `RUNS.md` and `EXPERIMENTS.md`, use the run log for new work and keep the experiment index as linked history. Finish already-active entries in their original records; do not duplicate their attempts into the new log. If only an experiment log exists, it can receive the compact new run entries without a forced rename.
- Preserve existing IDs, evidence, artifact locations, and referenced commits. Do not renumber, fabricate historical provenance, copy old outputs into a new layout, or remove old directories as an automatic migration.
- When adopting this protocol in the current project, update the README routing and the owning log's active instructions so obsolete mandatory queue/worktree rules do not compete with the new defaults. Label which log receives new work and which records remain historical or active legacy work.
- Existing mixed trees with tracked per-run documents keep scoped artifact ignore rules. The broad `/runs/` ignore applies only to the new outputs-only layout; do not hide records or expose private data through careless migration.
- Previously created external worktrees or artifact stores remain untouched unless cleanup/relocation is requested and preservation gates pass. Never scan other projects or initialize a parent repository to apply this workflow globally.
