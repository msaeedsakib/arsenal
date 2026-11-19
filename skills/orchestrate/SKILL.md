---
name: orchestrate
description: Use when implementing a phase whose plans index says parallel mode, or when the user asks for several plans to be implemented at once by subagents.
---

# Orchestrate

Run the plans of one phase at the same time, one subagent per plan in its
own worktree, and deliver the phase as one clean pull request.

You are the orchestrator. You keep the parts that need judgment: the shared
files, the merges, and whatever breaks where the plans meet. The subagents
do the well-specified building.

Without a subagent tool, use arsenal:implement and do the phase's plans one
after another instead.

## Before starting

Read the ledger, the index and every plan in the phase. Check that:

- no plan in the phase depends on another plan in the same phase
- no two plans edit the same file, apart from the shared files in the index
- every plan the phase depends on has a merged entry in the ledger

If a check fails, tell the user and run the conflicting plans in order
rather than in parallel.

If the plans share nothing at all and each could ship alone, skip the
integration branch and the steps below. Say so before starting, then
dispatch one subagent per plan in its own worktree, each following
arsenal:implement unchanged: it branches from the release branch, writes
its own ledger entry, opens its pull request into the release branch, runs
the review, merges and cleans up. The ledger is the one file they all
append to; a subagent whose rebase conflicts there keeps both entries. Tell
each subagent not to continue with arsenal:finish; when they are all done,
do step 6 below yourself.

Everything here is based on the release branch, `release/<release>`. For a
release written under arsenal 0.1.0, which has no `**Tests:**` block in any
plan file and no release branch, read "the default branch" wherever this
skill says "the release branch", and skip step 6.

## Steps

### 1. Cut the integration branch

Cut `feat/<release>-phase-NN` from the latest release branch and push it.
If the release branch does not exist, cut it first from the latest default
branch, commit the release folder to it and push.

### 2. Prepare the shared files

Make the changes the phase's plans need in the shared files now, on the
integration branch: new dependencies, registered routes or modules, shared
types, the order of migrations. Commit and push, so every subagent starts
from them.

### 3. Dispatch one subagent per plan

Dispatch them together so they run in parallel, each in its own git
worktree branched from the integration branch. Each prompt contains:

- the plan file path, the release folder path, and the instruction to
  follow arsenal:implement for that plan
- the base branch: the integration branch, not the release branch or the
  default branch
- these changes to the implement steps:
  - do not edit the shared files or `ledger.md`; if a shared file needs a
    change, stop and report what is needed
  - put the ledger entry in the pull request description instead of the
    ledger file
  - open the pull request into the integration branch, run the review and
    fix step, and do not merge, clean up or continue with arsenal:finish
  - run the full checks in the worktree before opening the pull request
- what to send back: the pull request link, the ledger entry, and any
  shared-file change it needs

Give each subagent what it needs to work alone. It knows nothing of your
session.

### 4. Merge the sub-pull-requests

As each subagent reports back:

1. Make any shared-file change it asked for, on the integration branch.
2. Read its diff against its plan. A subagent's report is a claim; the diff
   is the fact.
3. Merge its pull request into the integration branch, then delete its
   branch on the remote and locally and remove its worktree.
4. Append its ledger entry to `ledger.md` on the integration branch.
5. Run the full checks on the integration branch. Fix whatever broke where
   the plans meet, following arsenal:debugging, and record the fix under
   Deviations in the entry of the plan it concerns.

### 5. Deliver the phase

When every plan is merged into the integration branch and the checks pass:

1. Rebase the integration branch on the latest release branch and run the
   checks again.
2. Open one pull request into the release branch. Its description lists the
   plans, links their sub-pull-requests, and lists all deviations.
3. Follow arsenal:review for one code review of the whole phase, with the
   phase's plans as what the diff is compared against. Fix the confirmed
   findings and post the record.
4. Merge, unless the user's instructions say they merge themselves.
5. Clean up as arsenal:finish describes under "Clean up": delete the
   integration branch on the remote and locally, remove every worktree you
   created, switch the checkout to the release branch, and pull.

### 6. Finish the release if this was its last phase

If every plan in the index now has a ledger entry whose status is merged,
continue with arsenal:finish in this session.

## Common slips

| Slip | Instead |
|------|---------|
| Letting subagents edit the package manifest or the ledger | Those are yours. Every worktree editing them means every merge conflicts. |
| Subagents branching from the default branch | They branch from the integration branch, after the shared files are prepared. |
| Opening the phase's pull request into the default branch | It goes into the release branch. Only arsenal:finish opens a pull request into the default branch. |
| Leaving sub-pull-request branches and worktrees behind | Delete each as its pull request merges, and the integration branch after the phase. |
| Merging a sub-pull-request on the subagent's word | Read the diff, then run the checks after the merge. |
| Running a plan in parallel that depends on a sibling | Run that one after its dependency has merged into the integration branch. |
| Leaving the integration checks until every plan is in | Run them after each merge, so a break points at one plan. |
