---
name: plan
description: Use when a spec has been approved and must be split into implementation plans, or when the user asks to write, split or add plans for a release.
---

# Plan

Split the approved spec into small plans. Each plan is one pull request
that a fresh agent session can finish, with no memory of the brainstorm.
That session knows only what the plan, the spec and the ledger tell it, so
every detail it must honor has to be written where it will read it.

Plans say what to build, where, and how it should be done. They carry no
code.

## What this produces

In `docs/arsenal/<release>/`:

- `plans/index.md`: the plans, their phases and their order
- `plans/NN-<name>.md`: one file per plan
- `ledger.md`: created empty, filled in during implementation

## Steps

### 1. Ask how it will be implemented

Ask the user, with your recommendation and the reason for it:

- **Inline:** one plan per session, in order. Fits work where plans build
  on each other.
- **Parallel:** an orchestrator runs several plans at once with subagents
  in separate worktrees. Fits phases whose plans touch separate parts of
  the code and share only names that are fixed in advance.

The answer shapes the split: parallel work needs plans that do not edit the
same files.

### 2. Split the work

- **One plan, one pull request.** Something that works and can be checked
  when it merges. Prefer a slice a user could see or exercise over a layer
  that does nothing alone.
- **Keep plans small.** A plan covers about a dozen requirements at most.
  More than that, split it. Details get dropped when a session carries too
  many.
- **Order by dependency.** A plan may depend only on plans before it.
- **Group into phases.** A phase is a set of plans that ends in a state
  worth checking. In parallel mode, the plans inside a phase must be able
  to run at the same time: none depends on another in the same phase, and
  no two edit the same file.
- **Shared files** (package manifests, lockfiles, route or module
  registration, shared type files, migration order) are listed in the
  index. In parallel mode the orchestrator alone edits them.

### 3. Write the index, then the plans

Write `index.md` first, then each plan file using the format below.

For every task:

- Cite the requirements it covers.
- Copy in, word for word from the spec, every exact value the task must
  honor: limits, field names, status values, routes, text shown to users.
  Do not paraphrase them and do not leave them for the implementer to look
  up.
- Say what to build, in which part of the code, and any approach the spec
  or the existing code requires. Leave ordinary coding choices to the
  implementer.
- State how to tell it is done.

Where a plan uses or provides something another plan touches (a function,
a type, a route, a table), write its exact name and shape under "Shared
names" in both plans, identically.

### 4. Run the coverage script

Run `../spec/scripts/coverage <release-dir>` relative to this skill's
directory, by its absolute path. Every live requirement must be cited by at
least one plan, and every cited requirement must exist. Fix what it
reports.

### 5. Create the ledger

Create `ledger.md` with only its title line:
`# <Project> <release> — Ledger`.

### 6. Review

Continue with arsenal:plan-review. The user gets the plans after that
review, not before.

## Index format

```markdown
# <Project> <release> — Plans

**Spec:** ../spec.md
**Mode:** inline | parallel

| Plan | Name | Phase | Depends on | Covers |
|------|------|-------|------------|--------|
| 01 | <name> | 1 | none | R-001, R-002, R-040 |
| 02 | <name> | 1 | 01 | R-003, R-004 |

## Shared files
- <path> — <what it is>
```

Progress is not tracked here. The ledger is the only record of what has
been implemented.

## Plan format

```markdown
# Plan NN: <name>

**Release:** <release>
**Phase:** <n>
**Depends on:** Plan 02, Plan 05 | none
**Covers:** R-012, R-013, R-020
**Goal:** <one sentence: what works when this is merged>

## Shared names
- **Uses:** <exact name and shape> — from Plan 02
- **Provides:** <exact name and shape> — used by Plan 09

## Tasks

### Task 1: <name> (R-012, R-013)
<what to build, where, and how it should be done>

**Exact values:**
- R-012: "<copied from the spec>"
- R-013: "<copied from the spec>"

**Done when:** <what can be observed>

### Task 2: <name> (R-020)
...

## Verification
<the commands to run and what passing looks like; for anything with a
screen, what to do in the running app and what should be seen>
```

## Common slips

| Slip | Instead |
|------|---------|
| "Implement validation as per the spec" | Copy the rules and limits into the task. |
| A shared function named one way in Plan 03 and another in Plan 07 | Write it once, paste it into both. |
| A plan that is all database tables, with nothing to check | Slice so the plan ends in something observable. |
| A twenty-requirement plan because the area is "one module" | Split it. The size limit is about details carried, not module boundaries. |
| Code blocks with function bodies | Plans carry names, shapes and values. The implementer writes the code. |
| Adding tasks the spec does not ask for | Every task cites a requirement. No citation, no task. |
