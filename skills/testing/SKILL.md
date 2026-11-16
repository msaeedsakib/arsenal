---
name: testing
description: Use when writing, changing or deciding whether to add tests, including the test that goes with a bug fix.
---

# Testing

Tests exist to catch real breakage in behavior that matters. A few focused
tests do that. A large pile of shallow ones only costs time to run and to
maintain. The project's own testing conventions and the user's instructions
win over anything here.

## What to test

- **Behavior a requirement or task names.** If the plan says a trial lasts
  14 days, a test shows it ends on day 14.
- **Logic with real branches:** calculations, state changes, permission
  checks, parsing, anything where a wrong edit would silently give a wrong
  answer.
- **Every bug fix.** Write the test first, see it fail because of the bug,
  then fix the bug and see it pass. A test that never failed proves
  nothing about the fix.

## What not to write

- Tests that a thing exists, renders, or "does not throw", with nothing
  checked about what it does.
- Tests that mostly check a mock was called. Test what the code does with
  the real collaborator where that is practical.
- Tests for something that was removed, to prove it is gone.
- Tests for inputs the requirements do not mention and normal use will not
  produce.
- The same behavior tested again at another level without a reason.
- Tests of the framework, the library or the language.

## Writing one

- One behavior per test, named for the behavior: "rejects an expired
  token", not "test token 3".
- Check the outcome a user or caller would see, not internal details that
  can change freely.
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
| Editing a failing test until it passes | Decide first whether the test or the code is wrong. |
| Deleting or skipping a test that got in the way | Tell the user and let them decide. |
| Ten near-identical tests for one branch | One test per distinct behavior. |
| Writing the fix, then a test that passes | For a bug, the failing test comes first. |
