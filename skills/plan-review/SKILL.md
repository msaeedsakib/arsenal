---
name: plan-review
description: Use when the plans for a release have been written and must be checked against the spec and transcript before implementation, or when the user asks to review the plans.
---

# Plan Review

Check the written plans against the spec and the transcript before anyone
implements them. The review looks only for what would realistically go
wrong during implementation. It does not add scope, polish or extra
safeguards.

## Steps

### 1. Ask which model to use

Ask the user which model the reviewers should run on, and use it for every
reviewer below. Without a way to choose models, say so and continue.

### 2. Run the coverage script

Run `../spec/scripts/coverage <release-dir>` relative to this skill's
directory, by its absolute path, and fix anything it reports. Reviewers
should not spend effort on gaps a script can find.

### 3. Dispatch the reviewers

Dispatch these in parallel, each as arsenal:review describes for a plan
review:

- **Plan reviewers.** Split the plans into batches of about five, in
  order. Each reviewer owns one batch and checks every plan in it against
  the spec, and against the transcript where the spec cites it.
- **One cross-plan reviewer.** It reads the index and all plans, looking
  only at how they fit together: shared names that differ between plans, a
  plan using something no earlier plan provides, dependencies in the wrong
  order, and, in parallel mode, two plans in one phase editing the same
  file.

On Claude Code, run this as a workflow with the Workflow tool: one agent
per reviewer on the chosen model, each returning its findings. On other
harnesses, dispatch parallel subagents. Without subagents, do the batches
yourself one at a time, as arsenal:review describes.

### 4. Handle the findings

Handle each finding as arsenal:review says: discard any without a concrete
failure, verify the rest against the documents, and fix the confirmed ones
in the plans.

- A plan that contradicts the spec: fix the plan.
- A finding that shows the spec itself is wrong or silent: that is a spec
  change. Amend the spec as arsenal:spec describes, asking the user when it
  changes something they decided, then fix the plans.

Run the coverage script once more after the fixes.

### 5. Cut the release branch

Cut `release/<release>` from the latest default branch, commit the release
folder to it (transcript, spec, plans and the empty ledger), and push. Every
plan of the release branches from it and merges back into it, and
arsenal:finish takes it into the default branch at the end.

### 6. Hand to the user

Give the user:

- the plans folder path, the number of plans and phases, and the number of
  tests the plans name
- the findings that were confirmed and fixed, one line each
- how to start: a fresh session per plan with "Implement Plan NN", or
  "Implement Phase NN" for a whole phase

## Common slips

| Slip | Instead |
|------|---------|
| One reviewer for thirty plans | Batches of about five, plus the cross-plan reviewer. |
| Fixing a plan because a reviewer "suggested" something | Only findings with a concrete failure, verified by you. |
| Adding tasks or requirements during the fix round | Fixes correct plans against the spec. They do not grow it. |
| Skipping the cross-plan reviewer | Mismatched shared names are the commonest break between plans. |
