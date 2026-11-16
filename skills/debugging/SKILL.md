---
name: debugging
description: Use when there is a bug, a failing test, a failing build or any behavior that is not what was expected, before changing code to fix it.
---

# Debugging

Find the cause before changing anything. A fix aimed at a symptom either
fails or hides the bug somewhere harder to find.

## Steps

### 1. Read what it says

Read the whole error: message, stack trace, file and line. Read the failing
test and what it expects. Most of the time the answer is in there.

### 2. Reproduce it

Find the smallest reliable way to make it happen, and write down the exact
steps or command. If it does not happen every time, gather more
information (logs, inputs, timing) rather than guessing.

### 3. Find where it goes wrong

- Check what changed recently: the diff, recent commits, new dependencies,
  configuration.
- Follow the bad value backward from where the failure shows to where the
  value first becomes wrong. That place, not the place it crashed, is where
  the cause is.
- Where several parts are involved, check what goes in and comes out of
  each one, to find which part is at fault before reading its code.
- Find something similar in the codebase that works and compare.

### 4. State the cause, then test it

Write one sentence: "It fails because X." Then make the smallest change
that would prove or disprove that, and run the reproduction. One change at
a time. If the result does not match, undo the change and return to step 3
with what you learned.

### 5. Fix it

Follow arsenal:testing: write a test that fails because of the bug, fix the
cause, and see the test pass. Then run the reproduction from step 2 and the
project's tests, and read the output.

## When fixes keep failing

After three attempts that did not work, stop making attempts. Either the
cause is still not understood, or the design does not support what is being
asked of it. Go back to step 3, or tell the user what you found and what
you think is wrong underneath.

## Common slips

| Slip | Instead |
|------|---------|
| Changing code to see if the error goes away | Know the cause first, then change one thing. |
| Fixing where it crashed | Fix where the value went wrong. |
| Several changes at once | One. Otherwise you cannot tell which mattered. |
| Raising a timeout or adding a retry | That hides timing bugs. Wait for the actual condition. |
| Catching the error to silence it | The cause is still there. |
| Leaving failed attempts in the code | Undo each change that did not fix it. |
