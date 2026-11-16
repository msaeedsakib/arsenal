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
- **Check:** how it will be verified.

Wait for the user to accept it. Their corrections change the plan before
any code changes.

### 4. Build

Cut a branch from the latest default branch. For a bug, first write a test
that fails because of the bug, then fix it. Follow arsenal:testing. Build
what the plan says and nothing more.

### 5. Verify

Run the check from the plan, plus the project's tests, type check and lint,
and read the output. Confirm each line under After is true and each line
under Unchanged still holds. Do not report success without output from this
session that shows it.

### 6. Pull request, review, merge

Rebase on the latest default branch, push, and open a real pull request,
not a draft, with the Now / After / Unchanged lines in its description.

Save the accepted plan from step 3 to a temporary file outside the
repository. Follow arsenal:review for a code review, giving the reviewer
that file as the plan the diff is compared against; there is no spec or
ledger to give. Fix the confirmed findings, post the review record, and
merge unless the user's instructions say they merge themselves.

## Common slips

| Slip | Instead |
|------|---------|
| Fixing straight away because the cause "is obvious" | Present the plan first. It is five lines. |
| Leaving out Unchanged | That list is what stops a small fix from breaking its neighbors. |
| Tidying nearby code in the same pull request | Out of scope. Mention it to the user instead. |
| Skipping the review because the diff is small | Small diffs get a short review, not none. |
