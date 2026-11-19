---
name: quick
description: Use for a patch release, a small change or a bug fix in existing software, where a full brainstorm, spec and plan files would be more ceremony than the work deserves.
---

# Quick

A small change, done properly: understand it, agree a short plan with the
user in the conversation, build it, and merge a reviewed pull request. No
transcript, no spec, no plan files, no ledger entry. The pull request is
the record.

If the work turns out to need several pull requests or real design
decisions, say so and move to arsenal:brainstorm.

## Steps

### 1. Analyze

Read the code involved until you can explain how it works today. For a bug,
follow arsenal:debugging until you know the cause; do not plan a fix for a
symptom. Look up any outside fact the change depends on.

### 2. Ask what is still open

Ask the user only what the code and their request leave open and that
changes what gets built. One question at a time, recommended option first.
If nothing is open, skip this.

### 3. Present the plan

Write the plan in the conversation, briefly:

- **Now:** how it behaves today (for a bug: the cause).
- **After:** how it will behave, with exact values.
- **Unchanged:** behavior nearby that must stay as it is.
- **Changes:** which parts of the code change and how.
- **Tests:** the failing test that will be written for a bug, by name, or
  `none`.
- **Check:** how it will be verified.

Wait for the user to accept it. Their corrections change the plan before
any code changes.

### 4. Build

Cut a branch from the latest default branch. A quick change has no release
branch. For a bug, first write the test the plan names, see it fail because
of the bug, then fix it. Follow arsenal:testing: write the tests the plan
names and no others without a reason. Build what the plan says and nothing
more.

### 5. Verify

Run the check from the plan, plus the project's tests, type check and lint,
and read the output. Confirm each line under After is true and each line
under Unchanged still holds. Do not report success without output from this
session that shows it.

Nothing written only to check the work is committed. If the change alters a
screen, take a screenshot before and after, if your harness can, and push
them to the evidence branch as arsenal:finish describes.

### 6. Pull request and review

Rebase on the latest default branch, push, and open a real pull request,
not a draft, into the default branch. Its description has the Now / After /
Unchanged lines, the evidence for each After line, and a `Tests` line:
tests added as planned, added unplanned, changed and deleted, with the
reason for all but the first. For a change to a screen it shows the before
and after screenshots, or says what you did and saw if you could not take
them.

Save the accepted plan from step 3 to a temporary file outside the
repository, using your file write tool. Follow arsenal:review for a code
review, giving the reviewer that file as the plan the diff is compared
against; there is no spec or ledger to give. Fix the confirmed findings and
post the review record.

### 7. Ask about a release, then merge

Once the fixes are pushed, ask the user whether to tag a patch version and
publish a release for this change.

- **Yes:** the patch version is the next one after the latest tag. Set it
  wherever the project records its version, in this same pull request.
- **No:** change nothing.

Merge once the checks pass, unless the user's instructions say they merge
themselves. Then clean up as arsenal:finish describes under "Clean up":
delete the branch on the remote and locally, switch the checkout to the
default branch, and pull.

On a yes, create the tag on the merged commit and publish the release, with
notes written from the Now and After lines, as arsenal:finish describes
under "Tag and publish".

## Common slips

| Slip | Instead |
|------|---------|
| Fixing straight away because the cause "is obvious" | Present the plan first. It is five lines. |
| Leaving out Unchanged | That list is what stops a small fix from breaking its neighbors. |
| Tidying nearby code in the same pull request | Out of scope. Mention it to the user instead. |
| Skipping the review because the diff is small | Small diffs get a short review, not none. |
| Adding tests beyond the one the plan named | A fix gets its one failing test. More needs a reason in the pull request. |
| Leaving the branch behind after the merge | Delete it, remotely and locally, and switch back. |
