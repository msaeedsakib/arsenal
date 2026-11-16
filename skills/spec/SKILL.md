---
name: spec
description: Use when a brainstorm transcript is complete and the spec must be written from it, when the user asks to write or revise the spec for a release, or when implementation shows a requirement in an approved spec is wrong.
---

# Spec

Turn the transcript's decisions into a detailed description of what will be
built, where every requirement can be traced back to the decision behind
it. Then have it checked against the transcript before the user reads it.

## What this produces

`docs/arsenal/<release>/spec.md`, next to the transcript.

## Steps

### 1. Write the spec

Read the whole transcript. Write the spec so that someone who never saw the
brainstorm could plan the build from it.

- Every requirement has an ID and cites the decisions it comes from.
- Every live decision in the transcript, `D-` and `A-`, is cited by at
  least one requirement or by a line under "Not in this release".
- Exact values from the decisions are copied as written: numbers, names,
  status values, wording the user gave.
- Where the transcript leaves something open that the build needs, decide
  it, add it to the transcript as a new `A-` call with your reason, and
  cite it. Never put an uncited requirement in the spec.

For a release of existing software, the spec describes the change only.
Group requirements under Added, Changed and Removed, and name the earlier
requirement each change touches (`replaces 0.1.0/R-014`).

### 2. Run the coverage script

Run `scripts/coverage <release-dir>` from this skill's directory, by its
absolute path. Fix everything it reports under the decision checks: a
decision missing from the spec, or a cited decision that does not exist.

### 3. Review against the transcript

Follow arsenal:review to dispatch a spec review. Use one reviewer for a
small spec. For a large one, dispatch several in parallel, each owning a
range of transcript topics, so that every topic has an owner.

Handle the findings as arsenal:review says, then run the coverage script
again.

### 4. Second review

Dispatch one more spec review on the corrected spec, with fresh reviewers.
Fix what is confirmed. This is the last round; do not start a third.

### 5. Hand to the user

Give the user the spec path and a short list of the `A-` calls you made
while writing, so they can check those while reading. Wait for their
approval.

Changes they ask for go into the transcript first, as new decisions, and
then into the spec. After they approve, continue with arsenal:plan.

## Requirement IDs

- Requirements are `R-001`, `R-002`, … in each release folder.
- One testable statement per ID. "Users can sign up and reset their
  password" is two requirements.
- IDs are never reused or renumbered.

## Spec format

Each requirement is a bullet that starts with its bold ID and ends with the
decisions it cites. The coverage script depends on that shape.

```markdown
# <Project> <release> — Spec

## Purpose
<what this release is for, and for whom>

## Not in this release
- <thing decided against or deferred> (D-09)

## <Area>
<a few lines of context for this area>

- **R-001** <one testable statement with exact values> (D-01)
- **R-002** <...> (D-02, A-01)

## Stack and constraints
- **R-040** <runtime, framework, limits the build must respect> (D-15)
```

## Amending an approved spec

When implementation shows a requirement is wrong or impossible:

1. Add a new requirement with a new ID that says `amends R-014`, citing the
   decision behind it. If no decision covers it, add an `A-` call to the
   transcript, or ask the user when it changes something they decided.
2. Append `— replaced by R-052` to the old requirement's line. Do not edit
   or delete the old text.
3. Note the amendment in the ledger entry of the plan that found it.

## Common slips

| Slip | Instead |
|------|---------|
| Rounding or rewording a value ("about two weeks") | Copy it: "14 days". |
| A requirement that sounds sensible but cites nothing | Record the call in the transcript as `A-`, then cite it. |
| Leaving out a decision because it was a "no" | It goes under "Not in this release" with its ID. |
| Handing over the spec before the reviews | The user reads it after both rounds, not before. |
| Editing a requirement in place after approval | Amend with a new ID and mark the old one replaced. |
