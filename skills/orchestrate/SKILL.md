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
integration branch: each subagent opens its pull request straight into the
default branch. Say so before starting.

## Steps

### 1. Cut the integration branch

Cut `feat/<release>-phase-NN` from the latest default branch and push it.

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
- the base branch: the integration branch, not the default branch
- these changes to the implement steps:
  - do not edit the shared files or `ledger.md`; if a shared file needs a
    change, stop and report what is needed
  - put the ledger entry in the pull request description instead of the
    ledger file
  - open the pull request into the integration branch, run the review and
    fix step, and do not merge
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
3. Merge its pull request into the integration branch.
4. Append its ledger entry to `ledger.md` on the integration branch.
5. Run the full checks on the integration branch. Fix whatever broke where
   the plans meet, following arsenal:debugging, and record the fix under
   Deviations in the entry of the plan it concerns.

### 5. Deliver the phase

When every plan is merged into the integration branch and the checks pass:

1. Rebase the integration branch on the latest default branch and run the
   checks again.
2. Open one pull request into the default branch. Its description lists the
   plans, links their sub-pull-requests, and lists all deviations.
3. Follow arsenal:review for one code review of the whole phase, with the
   phase's plans as what the diff is compared against. Fix the confirmed
   findings and post the record.
4. Merge, unless the user's instructions say they merge themselves. Remove
   the worktrees you created.

## Common slips

| Slip | Instead |
|------|---------|
| Letting subagents edit the package manifest or the ledger | Those are yours. Every worktree editing them means every merge conflicts. |
| Subagents branching from the default branch | They branch from the integration branch, after the shared files are prepared. |
| Merging a sub-pull-request on the subagent's word | Read the diff, then run the checks after the merge. |
| Running a plan in parallel that depends on a sibling | Run that one after its dependency has merged into the integration branch. |
| Leaving the integration checks until every plan is in | Run them after each merge, so a break points at one plan. |
