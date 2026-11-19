---
name: testing
description: Use when writing, changing or deciding whether to add tests, including the test that goes with a bug fix.
---

# Testing

> Tests are good! Endless smoke tests, "regression tests" for feature
> deletions, etc, much less good. Tests should be focused, not slop.

That is the bar. The plan decides which tests exist; you write those. The
project's own testing conventions and the user's instructions win over
anything here.

## Which tests to write

- **The tests the plan names.** Each task has a `**Tests:**` block. Write
  the tests it lists, in the project's own test tools and layout, and no
  others except the two cases below. `**Tests:** none` means none.
- **A test for a bug.** A bug found during the work, or confirmed from a
  review finding, gets one test that fails because of the bug before the
  fix. It needs no further reason. A test that never failed proves nothing
  about the fix.
- **A test the plan missed.** You may add a test the plan did not name when
  you find real behavior the plan missed, where a wrong edit would silently
  break a requirement. Record it under Deviations in the ledger entry: the
  situation, and what would go wrong.

When a plan has no `**Tests:**` blocks at all, decide by the plan skill's
rule: tests only for logic where a wrong edit would silently give a wrong
answer (calculations, state changes, permission checks, parsing, and
boundaries a requirement states). Wiring, layout and configuration are
shown by running.

Checking that your change works is not a reason for a test. Run it, read
the output, and write that down as evidence. Anything you write only to
check the work stays outside the repository and is never committed.

## Tests that already exist

An existing test changes for three reasons only:

- **The behavior changed on purpose.** A requirement in the plan changes
  it, and the plan names the change as `changes <test name> — R-xxx`.
- **The shape changed, not the behavior.** What the test calls was renamed
  or reshaped. Update the call; what it checks stays the same.
- **The behavior was removed on purpose.** Delete its tests with it. Do not
  write a test to prove it is gone.

In every other case a failing test means the code is wrong. Do not loosen,
skip or delete the test.

If you believe an existing test is itself wrong, and no requirement in your
plan covers it, you may correct it and record the reason under Deviations
in the ledger entry. You may not delete or skip a test for this reason.

## What not to write

- Tests that a thing exists, renders, or "does not throw", with nothing
  checked about what it does.
- Tests that mostly check a mock was called. Test what the code does with
  the real collaborator where that is practical.
- Tests for inputs the requirements do not mention and normal use will not
  produce.
- The same behavior tested again at another level without a reason.
- Tests of the framework, the library or the language.

## Writing one

- One behavior per test, named for the behavior: "rejects an expired
  token", not "test token 3".
- Check the outcome a user or caller would see, not internal details that
  can change freely.
- When the requirement is a rule over all inputs and the project already
  has a property-testing library, write the test as a property test. Never
  add such a library to a project yourself.
- Use the project's existing test tools, helpers and layout. Read a nearby
  test first.
- No fixed sleeps. Wait for the condition the test depends on.
- A test must fail when the behavior breaks. If you are unsure, break the
  behavior for a moment and watch the test fail.

## Running them

Run the tests you wrote and the project's suite, and read the output,
before saying they pass. If an unrelated test was already failing, say so
plainly rather than fixing or deleting it.

## Common slips

| Slip | Instead |
|------|---------|
| Adding a test to show your change works | Run it and record the output as evidence. Tests come from the plan. |
| Editing a failing test until it passes | The code is wrong unless one of the three reasons applies. |
| Deleting or skipping a test that got in the way | Never. Correct it with a recorded reason, or fix the code. |
| Ten near-identical tests for one branch | One test per distinct behavior. |
| Writing the fix, then a test that passes | For a bug, the failing test comes first. |
| An unplanned test with no line in the ledger | Record the situation and what would go wrong, or do not add it. |
