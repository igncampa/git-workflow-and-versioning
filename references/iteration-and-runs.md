# Iteration, Runs, and Review

Read this file with the Git skill for these tasks: experiments, parameter changes, kept pipeline results, comparisons, and their review decisions. All of these tasks use the same small run record. The approval boundary of the user tells you if the work can integrate. Usual development tests, builds, and debug work do not need run records.

The fast path is: **if this is a trial, make a branch in the current checkout -> save the candidate -> run into a new destination -> log the outcome -> let the user examine the result -> integrate only what the user approved**. You do not have to register a queue, merge to main before the run, or make a separate experiment directory.

## One Log, One Output Root

Start with the project README. Then read only the related run entries and source files. If the project has a log or output mechanism, use it. Do not make a competing registry. If the project does not have one, this is the full default layout:

```text
<project>\
  README.md                 # stable context and commands
  RUNS.md                   # tracked recipes, outcomes, and review decisions
  runs\                     # ignored outputs only; make it only when necessary
    <run-id>\               # the actual outputs and logs of this attempt
```

The default does not include these items: a README for each experiment, `RESULTS.md` databases, mandatory manifests for each run, sequential queue IDs, or more `artifacts\` layers. Keep code and configuration that you will use again in their usual source locations. Version them on the candidate branch. Put small, one-time, non-secret overrides in the recorded command or in the log. Do not make a new configuration hierarchy for them.

Make the log for the first attempt that you keep. Do not make it during onboarding that is not related. Make output directories only if the execution writes files. You can record an attempt that gives only metrics fully in the log. One folder for each attempt that makes files is the necessary preservation boundary. It is not a second copy of the project. Use the internal output structure of the existing runner. Do not add one more wrapper layer.

When you add the log or output root for the first time, do these steps on the current task branch before the execution: connect the README handoff and add the specified ignore rules. Keep that supporting context with the candidate. For a pending trial, you do not have to publish it on main. Before you leave a branch, apply the cross-branch ignore protection from the Git skill.

The output root belongs to the confirmed project. It does not belong to the parent directory, to a random shell working directory, or to a checkout that you will remove. Find the output root before you start the run. If the working directory changes, give the destination explicitly. Do not use sibling directories, a system temporary directory, or session storage for the only kept copy. Use external storage only when it is already part of the project contract, or when the user explicitly permits it.

## The Smallest Useful Run Record

`RUNS.md` needs only these items:

- A short `## Conventions` section. It gives the output path of the project, the approval rule, and the selection authority if one exists.
- A `## Runs` section after it.

Do not copy the project setup guide into it. Persons must be able to use the project records without information about where these skills are installed.

Add one entry for each attempt. Use a UTC timestamp that is safe for Windows, a slug that persons can read, and a suffix that prevents collisions. For example: `20260908T170012Z-threshold-7c2e`. You can use a task branch again across attempts. Never use a run ID or a writable output destination again. When you compare, try again, or make results from a prior attempt, add a link to that attempt. A comparison link does not mean that the run used its files.

```markdown
### <run-id> - <short intent>
- State: running
- Started (UTC): <timestamp>
- Change / comparison: <what is different; prior run ID or none>
- Branch / baseline: <task branch; accepted or explicit baseline commit>
- Run commit: <actual HEAD at launch, or unavailable with reason>
- Command: <exact command, shell, and working directory>
- Inputs / parameters: <input identities; resolved non-secret overrides; run IDs that the run used>
- Environment: <runtime; lockfile or environment reference; related seed, model, or hardware>
- Outputs / inspect: <output root and important report or files, or inline measurements>
- Outcome: pending
- Review: awaiting review | no adoption requested
```

Write the start facts before the execution. When the run stops, do these steps:

- Set State to `succeeded`, `failed`, or `aborted`.
- Under Outcome, add the end time, the exit status, the actual measurements and observations, the list of important artifacts (or a reference to that list), and the known limits on reproducibility.

Change Review only after a decision. Then add a dated decision note. The note tells who gave the permission, the scope (code, results, or the two), the reason, and the actual integration or archive refs or the change to the selection. Add all subsequent corrections and decisions as dated additions. Do not rewrite the original recipe or past measurements. The summary fields can show the latest factual state, but do not remove these events.

Give a quantity of detail that agrees with the run, but make sure that the run is reproducible. An exact command and a reference to a committed configuration or lockfile can replace many pages of repeated settings. When possible, record the versions or checksums of the inputs and immutable references to get them. A checksum finds a change, but it does not keep the input. If inputs or environments can change or are not available, record this correctly. Record the related output checksums or an existing list of outputs. This is important for comparisons and preservation. Do not calculate hashes of datasets that are not related. Do not install a tracking service for a trial that changes one value.

Never record these items in Git: credentials, secret values, the contents of private inputs, or sensitive parts of outputs. Use environment-variable placeholders for secrets. Use approved, non-sensitive identifiers for inputs. Never report a value that you did not measure as zero. Never report an estimate as a measurement. For results that you import from before, use State `imported`. Record the import time and the known locations. Mark the unknown execution facts explicitly. Imported results are not new, verified, successful executions.

## Before Launch

1. Find the requested result or the review question. Find the smallest useful run or comparison. If a recorded baseline has the same related inputs and recipe, use it again. A trial that changes one value does not automatically need a new baseline run, a parameter sweep, or a new experiment plan. Obey the limits of the user on runs, time, and compute. Write ideas for later work in the current log or in an existing tracker. Do not make a queue.
2. Obey the branch and approval rules of the Git skill, and prepare the candidate. If a trial does not need code changes, its branch can point at main at first. You do not need an empty commit.
3. Examine all writes, not only the final outputs. These include intermediate files, logs, caches that change, databases, fixed exports, and external sinks. Use the existing options to send the changing state of the run to the new destination. If a fixed path will be overwritten, and the change is in the scope of the request, change the output root for that path on the task branch. If not, tell the user about the blocker. You can use shared resources again if you know that they are read-only or content-addressed. Writes to shared or external locations still need permission. A worktree does not isolate hard-coded paths, links, shared environments, or services.
4. Keep all old results that the run can overwrite. It is better to keep them where they are and send the new run to a different location. If you must replace a legacy fixed location, first do these steps: keep a copy or snapshot in the existing output root, make sure that the copy is correct, and log its known origin as an import. Do not move or reorganize old outputs that are not related only to make the layout uniform.
5. Reserve a run ID that is not in use. For commands that make files, make its new destination atomically before the launch. The destination must not exist before. If there is a collision, stop. Never empty, use again, or point at the directory of a selected run or a prior run. Each retry gets a new ID. Concurrent runs must reserve independent destinations, and each writer must be safe independently.
6. Commit the execution code, the scripts, the non-sensitive configuration, and the related README and ignore changes. Get the actual HEAD at launch. Keep the start record as the next section tells you. Start the run only when the write isolation is real and all execution changes are committed. Do not silently copy the repository or start a different worktree to hide an isolation problem.

### Exact Launch State Without Extra Checkouts

The run commit is the actual `HEAD` of the execution checkout at launch. This is after all code and configuration commits before the run. It is not an earlier baseline commit. It is not the subsequent evidence commit. The execution code and configuration must be clean. No other writer can change them during the run. Never include unrelated, uncommitted code in a candidate snapshot.

Usually, do these steps: get HEAD, add the start record to the run log, and keep only those declared record edits uncommitted until the run is complete. This keeps the exact launch SHA. You do not need a commit that refers to its own hash, and you do not need a different worktree. If the application examines the Git dirty state, keep the checkout clean. To do this, write the start recipe and HEAD to an ignored log in the output directory of that run. After the execution, add it to the tracked log. If the method has an effect on behavior, record which method you used.

If the user explicitly declined Git, record the refs as unavailable. Keep the non-secret recipe and the outputs. When possible, keep a necessary snapshot of the source and configuration in the same kept storage. Do not make a second copy of the project. Tell the limits on code recovery. Do not say that Git makes the run reproducible.

## Run, Inspect, and Continue

Run only the scope that the user requested. For work that runs for a long time, record the process or runner identifier that the task owns with the start record. Then a subsequent session can find it. Do not change branches in the execution checkout, and do not change its files, while the run is active.

When the run stops, use the actual exit status and the correct condition of the expected outputs to decide if the run succeeded. It is not sufficient that files exist. Complete the log and commit the outcome on the task branch. Include partial outputs and failed or aborted attempts. After an interruption, examine the actual process and files before you say that the run aborted or before you try again. Never start a duplicate run only because the previous chat stopped.

For a task where the user wants to examine the result first, stop and say **awaiting review**. Give the candidate branch and the exact report or output location. Keep the branch checked out. Do not change main or the selected results. If the user asks for one more change, continue the same trial with a new attempt. The previous output stays available for comparison. A request for review does not mean that you did not complete the task.

A usual rerun of approved code can save its factual evidence on main if the request and the repository policy permit it. This gives permission to record the run. It does not give permission to adopt a new implementation or to replace a selected result. Do not change a usual deliverable into a trial queue. Do not make a branch for each output.

The records on an open trial branch are the authority for that pending work. You do not have to publish them on main first. When you continue from a different branch, use Git to read the log of the original branch. Do not copy its record into a second authority. When you integrate parallel branches, merge the independent entries that were added to each log. Never remove a different run to solve a log conflict.

## Decide What to Adopt

The user can do one of these: accept the code, select the results, accept the two, ask for one more attempt, or reject or abandon the trial. Record the scope explicitly. Use the integration and retention gates of the Git skill. The best metric is not approval. A successful exit is not approval.

An approval applies to the candidate and the result that the user examined. Before the integration, compare the current branch with the recorded execution state. Edits to evidence only do not make the approval incorrect. Changes to the executed code, dependencies, configuration, or related inputs can make it incorrect. Do not silently merge a subsequent change that the user did not examine under an earlier approval.

When the user approves the code, integrate the approved implementation, the stable README changes, and the run evidence. Keep the actual launch SHA and all archive maps or maps of adopted commits. **Do not run again only because the code was merged or squashed.** You can keep or select the examined artifacts if they are still the approved and compatible result. Run again only when the related execution state changed or when the user asks for it.

For a trial that changed only CLI parameters, an approval to make the change permanent must go into the existing canonical configuration or the supported command. It is not sufficient to put it only in the run log. Promote only the approved values. Make sure that the effective recipe is the same as the recipe that the user examined. A merge of the log only is not an applied configuration change.

If the user closes the trial without code adoption, do these steps. Keep the candidate with an archive ref. Then integrate the factual evidence and the minimum README and log-storage routing that is necessary to find it. Do not integrate rejected code or documentation that applies only to the candidate. Keep the old results. If the review is still pending, do not close, archive, or delete the active trial only to put the checkout back on main.

When you integrate only some of the evidence, apply only the related entries and convention changes. Keep the other runs on main and the selected-result authority on main. Do not overwrite them with a full candidate log that is out of date.

### Selecting a Result Is Optional and Separate

Not all projects need a selected or default result. Do not invent a `latest` directory, a series registry, a pointer file, or a selection database only to track runs. A newer result never becomes the selected result automatically.

If selection is necessary, use the existing consumer pointer or configuration of the project as the only authority. Record the event in the run log. If there is no mechanism, and the selection is for persons or agents, a small `## Selected` table in the same log is sufficient: `Purpose | Run ID | Location | Reason`. Add it only when the user actually asks for a selection. Use one `default` row, unless the project needs more named deliverables. Do not keep a second current pointer in the README.

For a permitted selection, these conditions are mandatory: a successful run or a verified import, artifacts that are complete and that persons can read, and consumer compatibility. Record the old reference, the new reference, and the decision. Change the actual authority through the approved integration or publication path. Before you apply concurrent decisions, compare the previous value with the current main again. A machine pointer must not move before the necessary metadata is integrated. Use the existing atomic update mechanism. If the update fails partially, report it. Do not say that the consumers changed. Do not add a deployment or pointer system for manual inspection.

A selection refers to kept outputs. It does not rewrite them. If the project needs an export to a fixed path, this is a publication step that the user must explicitly permit. Do it only after you keep the old contents. The export is not the canonical run storage. A rollback selects a kept compatible result and records the change. A rollback does not reset Git and does not delete newer attempts. If the user accepts only the result, this does not give permission for the trial code.

## Preservation Without Directory Sprawl

Completed artifacts are immutable. A correction or a new calculation makes a new attempt. A branch change, a merge, a rejection, or a cleanup must not overwrite artifacts. Git keeps the code and the log. Git does not keep ignored outputs. Keep artifacts and necessary inputs in permanent storage in the project or in approved storage. A local copy is not a backup on a different machine.

If you must move artifacts, do these steps in this sequence. Copy them to the planned kept location. Make sure that the related list of files and the checksums are correct. Change the recorded location and the consumers. Then, and only then, remove a copy that the task owns and that is not necessary. Never remove the only copy with a worktree. To delete results on purpose, you must have explicit permission or an established policy. Record what became unavailable and why.

Keep active proposals as branches. When necessary, keep closed alternatives as archive Git refs. Do not keep them as directory snapshots or permanent code variants. Do not make archive folders. Do not keep duplicate full checkouts. Do not delete result history to make the workspace look clean.

## Existing Projects and Legacy Records

This protocol is a default for new work. It does not give permission to reorganize each existing project.

- If the project has a run log, a registry, and an output layout that are sufficient, use them. Use their actual paths and their compatible fields. Do not make `RUNS.md` next to an equivalent authority.
- If the project has old, separate `RUNS.md` and `EXPERIMENTS.md` files, use the run log for new work. Keep the experiment index as linked history. Complete the entries that are already active in their original records. Do not copy their attempts into the new log. If the project has only an experiment log, you can add the new short run entries to it. You do not have to rename it.
- Keep the existing IDs, evidence, artifact locations, and referenced commits. Do not give new numbers. Do not invent historical origin. Do not copy old outputs into a new layout. Do not automatically remove old directories as a migration.
- When you start to use this protocol in the current project, change the README routing and the active instructions of the log that owns the records. Then old mandatory queue or worktree rules do not compete with the new defaults. Write which log gets new work. Write which records are history and which records are active legacy work.
- In existing mixed trees that have tracked documents for each run, keep the specified artifact ignore rules. The broad `/runs/` ignore rule applies only to the new layout that has only outputs. Do not hide records. Do not show private data because of a careless migration.
- Do not change external worktrees or artifact storage that were made before, unless the user asks for cleanup or relocation and the preservation gates pass. Never scan other projects. Never initialize a parent repository to apply this workflow to all projects.
