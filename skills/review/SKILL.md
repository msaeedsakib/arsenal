---
name: review
description: Use when a spec, a set of plans, or a pull request needs reviewing, when dispatching a reviewer subagent, and when review findings come back and you must decide what to fix.
---

# Review

Every arsenal review uses one bar: real bugs, contradictions with or
omissions of what was decided, security flaws, and, for plans, things
realistically likely to go wrong during implementation. Nothing below that
bar is reported, and nothing below it is fixed.

The bar and the output format live in `reviewer.md` in this skill's
directory. Reviewers read that file; you do not restate it.

## Dispatching a reviewer

Use a fresh subagent that has seen none of your session.

Its prompt contains only:

1. The kind of review: spec, plan or code.
2. "Read `<absolute path to reviewer.md>` first and follow it."
3. Absolute paths to what it compares:
   - spec review: the transcript and the spec
   - plan review: the transcript, the spec, the plans index and the plan
     files in its batch
   - code review: the plan, the spec, the ledger, and the diff as a file
     (`git diff <base>...HEAD > <file>`), plus the repository path. For an
     arsenal:quick change, the plan is the file holding the accepted
     Now / After / Unchanged plan, and there is no spec or ledger.
4. For large inputs, the slice it owns ("topics 1–4 of the transcript",
   "plans 01–06").

Give it paths, never pasted content or a summary of what you did. Do not
tell it what to ignore or what you think is fine. If the harness lets you
restrict a subagent's tools, make the reviewer read-only.

## When findings come back

For each finding:

1. **Check it has a concrete failure.** A finding without a specific
   situation and outcome is discarded.
2. **Verify it against the documents or the code.** Reviewers are sometimes
   wrong. Read the cited place yourself.
3. **Confirmed:** fix it. In code, a bug fix gets a test that fails before
   the fix when the project has tests for that area.
4. **Not confirmed:** leave the work as it is and note in one line why.

Fix only what the findings name. A finding is not a reason to improve
nearby code or documents.

If a finding shows the spec and the transcript disagree, or the plan and
the spec disagree, the earlier document wins: transcript over spec, spec
over plan. If the earlier document is itself the problem, that is the
user's decision; ask them.

## Recording a code review

Post the findings and what you did about each as one comment on the pull
request, so the record stays with the PR:

```
## Review

1. <title> — fixed in <commit>
2. <title> — not confirmed: <one line>
```

or `## Review` followed by `No findings.`

## Without subagents

Do the review yourself as a separate pass, reading `reviewer.md` first and
working only from the files, not from memory of writing them. Tell the user
the review was not independent.

## Common slips

| Slip | Instead |
|------|---------|
| Pasting the diff or your summary into the reviewer's prompt | Paths only. The reviewer reads the files. |
| Telling the reviewer "X is intentional, don't flag it" | Let it flag X. You judge the finding when it comes back. |
| Fixing a finding without reading the cited place | Verify first. A wrong fix for a wrong finding is two mistakes. |
| Fixing extra things "while here" | Only confirmed findings get fixed. |
| Asking for a second full review after fixes | Verify the fixes by running the checks. Only the spec review has a second round. |
