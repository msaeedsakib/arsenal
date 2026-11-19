---
name: implement
description: Use when the user says "Implement Plan NN" or "Implement Phase NN", or asks to build from an arsenal plan file.
---

# Implement

Build one plan as one reviewed, merged pull request, and record in the
ledger what was actually built.

You have no memory of the brainstorm. What you need is in the plan, the
spec sections it cites, and the ledger. Read them; do not guess.

## A phase

"Implement Phase NN" means every plan in that phase.

- Index says **inline**: run the steps below for each plan in order, one
  pull request per plan. Start the next plan only after the previous one
  has merged.
- Index says **parallel**: use arsenal:orchestrate.

## Steps for one plan

### 1. Read

In `docs/arsenal/<release>/`:

1. `ledger.md`, all of it. It says what earlier plans built and where they
   departed from what was written. Where the ledger and a plan disagree
   about something already built, the ledger is right.
2. `plans/index.md`, then your plan file.
3. In `spec.md`, every requirement your plan covers, with the section
   around each.

Check that every plan yours depends on has a merged entry in the ledger. If
one does not, stop and tell the user.

### 2. Branch

The base is the release branch, `release/<release>`, unless you were told
another. If it does not exist, cut it from the latest default branch,
commit the release folder to it and push.

Update the base and cut a branch for this plan from it. Follow the
repository's branch naming, or use `plan/<release>-NN-<name>`.

**A release written under arsenal 0.1.0** has no `**Tests:**` block in any
of its plan files. It has no release branch and you do not cut one: its
base is the default branch.

### 3. Build

Work through the tasks in order. For each task:

- Use the exact values written in the task. If a value in the plan differs
  from the spec, the spec is right; note it as a deviation.
- Use the shared names exactly as the plan and the ledger give them.
- Write the tests the task's `**Tests:**` block names, as arsenal:testing
  says, and no others without a recorded reason. If the plan has no such
  blocks, arsenal:testing says how to decide.
- Follow arsenal:debugging when something fails.
- Build what the task says and nothing more.

**When the plan cannot be followed as written,** decide the smallest change
that still satisfies the requirement, carry on, and record it as a
deviation in the ledger entry. Stop and ask the user only when the change
would alter something they decided, when it is destructive or touches
anything outside this repository, or when every way forward is a guess.

**When a requirement itself is wrong,** amend the spec as arsenal:spec
describes.

### 4. Verify

Before saying anything is done:

1. Run every command in the plan's Verification section, plus the project's
   tests, type check and lint. Read the output.
2. For anything with a screen, run the app and do what the plan says to do.
3. Go through the requirements your plan covers one at a time. For each,
   find the evidence that it is met. A requirement with a planned test is
   shown by that test passing, by name. Every other requirement is shown by
   a command and its output, or by what you observed in the running app.
4. When a requirement states a target as a number, the evidence is the
   measurement: the figure before, the figure after, and how they were
   taken.
5. When the plan changes a screen, take a screenshot of it before your
   change and one after, if your harness can. They go to the evidence
   branch, as arsenal:finish describes.

Checking the work does not add to the repository. Nothing written only to
check it is committed, and a script or temporary test you need for that is
created outside the repository, with your file write tool.

A requirement with no evidence is not done; go back and finish it. Do not
write "should work" or "tests pass" without output from this session that
shows it.

### 5. Write the ledger entry

Append your entry to `ledger.md` and commit it on the plan's branch, so it
merges with the code.

```markdown
## Plan NN: <name>
- **Status:** merged
- **Requirements:**
  - R-012 — <evidence>
  - R-013 — <evidence>
- **Tests:** planned: <names>; unplanned: <name> — <reason>; changed:
  <name> — <reason>; deleted: <name> — <reason>
- **Deviations:** none
- **For later plans:** none
```

- **Tests:** every test this plan touched. Tests added as the plan named
  them are listed by name. Tests added unplanned, changed or deleted each
  carry the reason. Leave out the parts that are empty, or write `none`.
- **Deviations:** anything built differently from the plan or the spec,
  with the reason, and any spec amendment made. An unplanned test is a
  deviation: give the situation and what would go wrong.
- **For later plans:** anything a later session must know that its plan
  will not tell it: a name that changed, a shape that differs, something
  left for a later plan.

### 6. Open the pull request

Rebase on the latest base branch, run the checks again if anything changed,
push, and open a real pull request, not a draft. Follow the repository's
title conventions. The description says what the plan was for and how it
was built, lists each requirement covered with its evidence, repeats the
ledger entry's `Tests` line, and lists the deviations.

For a plan that changes a screen, the description shows the before and
after screenshots, linked from the evidence branch. If your harness cannot
take a screenshot, describe what you did and what you saw.

### 7. Review and fix

Follow arsenal:review: dispatch a fresh code reviewer, verify its findings,
fix the confirmed ones, and post the review record as a comment on the pull
request. After fixing, run the verification again and update the ledger
entry if the fixes changed any evidence or added a deviation.

### 8. Merge

Merge the pull request once the fixes are pushed and the checks pass,
unless the user's instructions say they merge themselves.

Then clean up as arsenal:finish describes under "Clean up": delete the
plan's branch on the remote and locally, remove any worktree you made,
switch the checkout to the branch you merged into, and pull.

### 9. Finish the release if this was its last plan

If every plan in the index now has a ledger entry whose status is merged,
continue with arsenal:finish in this session. Do not do this for a release
written under arsenal 0.1.0; the user calls arsenal:finish for those.

Otherwise report in a few lines: the pull request, any deviations, and
anything the user must do.

## Common slips

| Slip | Instead |
|------|---------|
| Starting from the plan without reading the ledger | Earlier plans changed things. The ledger is where that is written. |
| "I know roughly what R-020 says" | Open the spec and read it. |
| Writing the ledger entry from memory of what you built | Each line of evidence comes from output you saw in this session. |
| Adding a helpful extra that no task asked for | Out of scope. It is not reviewed against anything and no requirement covers it. |
| Quietly doing something different from the plan | Do it if it is right, and write the deviation. |
| Marking a requirement done because its test passes, when the test checks something narrower | The evidence must show the requirement, not something near it. |
| Opening the pull request and stopping | The plan is finished when the reviewed pull request is merged. |
| Opening the pull request into the default branch | A plan goes into `release/<release>`. Only arsenal:finish opens a pull request into the default branch. |
| Committing a script or test written to check the work | Evidence is output you saw. It goes in the ledger and the pull request, not the repository. |
| Leaving the plan's branch behind after the merge | Delete it, remotely and locally, and switch back. |
