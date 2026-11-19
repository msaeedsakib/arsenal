# Plan 01: arsenal-0.2.0

**Release:** 0.2.0
**Phase:** 1
**Depends on:** none
**Covers:** R-001, R-002, R-003, R-004, R-005, R-006, R-007, R-008, R-009,
R-010, R-011, R-012, R-013, R-014, R-015, R-016, R-017, R-018, R-019,
R-020, R-021, R-022, R-023, R-024, R-025, R-026, R-027, R-028, R-029,
R-030, R-031, R-032, R-033, R-034, R-035, R-036, R-037, R-038, R-039,
R-040, R-041, R-042, R-043, R-044, R-045, R-046, R-047, R-048, R-049,
R-050, R-051, R-052, R-053, R-054, R-055, R-056, R-057, R-058, R-059,
R-060, R-061, R-062, R-063, R-064, R-065, R-066, R-067, R-068, R-069,
R-070, R-071, R-072, R-073, R-074, R-075, R-076, R-077, R-078, R-079,
R-080, R-081, R-082, R-083, R-084, R-085, R-086, R-087, R-088, R-089,
R-090, R-091, R-092, R-093, R-094, R-095, R-096, R-097, R-098, R-099,
R-100, R-101, R-102, R-103, R-104, R-105, R-106, R-107, R-108
**Goal:** Arsenal 0.2.0 is merged: plans name the tests, a hook refuses
shell file edits, a release ends in `main` with a tag, and arsenal runs in
the Codex CLI.

This is the only plan of the release (R-108). The implementing session runs
the 0.1.0 skills, so it applies the 0.2.0 branch and finish rules by hand
(R-103): this plan's branch is cut from `release/0.2.0` and its pull request
goes into `release/0.2.0`.

**Before Task 1.** `release/0.2.0` does not exist yet. Cut it from the
latest `main`, commit the whole of `docs/arsenal/0.2.0/` to it (transcript,
spec, plans and the empty ledger), and push it. Then cut
`plan/0.2.0-01-arsenal-0.2.0` from it. This is R-058 done by hand, and it
also satisfies R-104.

Every skill edited or written here keeps the 0.1.0 style: short, plain
language, a "Common slips" table, no programming language or testing
library named except as an example (R-024), no Codex tool named and no new
mention of any harness's tools (R-093).

## Shared names

- **Provides:** `hooks/file-tools` — bash script, reads the hook's JSON
  input on stdin, prints a deny decision or nothing
- **Provides:** `tests/run` — bash script, no arguments, exits non-zero
  when a case fails
- **Provides:** `skills/finish/SKILL.md` — skill name `finish`, invoked as
  `arsenal:finish`
- **Provides:** `references/codex.md`
- **Provides:** branch names `release/<release>`,
  `plan/<release>-NN-<name>`, `feat/<release>-phase-NN`,
  `arsenal/evidence`

## Tasks

### Task 1: Tests in the plan format and the coverage script (R-001, R-002, R-003, R-004, R-005, R-006, R-007, R-008, R-009)

In `skills/plan/SKILL.md`: add the `**Tests:**` block to the per-task
instructions and to the plan format, add the rule for which requirements
get a test, and add the `Tests` column to the index format.

In `skills/spec/scripts/coverage`: add a check that finds each task heading
(`### Task ...`) in each plan file and reports the ones with no
`**Tests:**` line before the next task heading or the next `## ` heading.
Run it only when at least one plan file contains `**Tests:**`. Update the
header comment of the script to describe the check.

**Exact values:**
- R-001: the block is "placed after `**Exact values:**` and before
  `**Done when:**`"
- R-002: line form
  "`- R-012: a trial started on any date ends exactly 14 days later`"; "The
  block contains no test code."
- R-003: "`**Tests:** none — <reason>`"
- R-004: "name tests only for logic where a wrong edit would silently give
  a wrong answer: calculations, state changes, permission checks, parsing,
  and boundaries a requirement states"
- R-005: "wiring, layout and configuration get `none` with a reason and are
  shown by running, and that there is no fixed number of tests per plan"
- R-006: "`changes <test name> — R-xxx`"
- R-007: "a `Tests` column giving the number of tests named in each plan"
- R-008: "exits 1 and prints the plan file and the task heading of every
  task that has no `**Tests:**` block"
- R-009: "only when at least one plan file in the release folder contains a
  `**Tests:**` block"

**Tests:**
- R-008: the coverage script exits 1 and prints the plan file and the task
  heading when one task of a plan has no `**Tests:**` block and another has
- R-009: the coverage script does not fail a release folder in which no
  plan has a `**Tests:**` block

**Done when:** the coverage script passes on `docs/arsenal/0.2.0` and both
tests pass.

### Task 2: Plan review and the reviewer's bar (R-010, R-011, R-040, R-041, R-042, R-058)

In `skills/review/reviewer.md`: under plan review add the two findings
about named tests; under code review add the two findings about tests in
the diff; reword the "do not report" line about tests so it excludes these
four and nothing else.

In `skills/plan-review/SKILL.md`: add a last step that cuts the release
branch.

**Exact values:**
- R-010: "a boundary that a requirement states and that no test in the plan
  is named for"
- R-011: "a test that is named for something no requirement states"
- R-040: "a test in the diff that neither the plan nor the ledger entry
  accounts for"
- R-041: "an existing test that was loosened, skipped or deleted outside"
  the three reasons of R-017, R-018, R-019 and the correction of R-021
- R-042: the line "missing tests, extra tests, or test quality, unless a
  requirement names the test" is reworded so that it does not contradict
  R-010, R-011, R-040 and R-041
- R-058: "cuts `release/<release>` from the latest default branch as its
  last step, commits the release folder to it and pushes"

**Tests:** none — instruction text read by agents, checked by reading it

**Done when:** both files state the six points with the values above.

### Task 3: The testing skill (R-013, R-014, R-015, R-016, R-017, R-018, R-019, R-020, R-021, R-022, R-023, R-024)

Rewrite `skills/testing/SKILL.md` around the plan's `**Tests:**` block.
Keep the sections "Writing one" and "Running them" and the parts of "What
not to write" that still apply.

**Exact values:**
- R-013: the skill opens with, quoted exactly: "Tests are good! Endless
  smoke tests, "regression tests" for feature deletions, etc, much less
  good. Tests should be focused, not slop."
- R-014: "write the tests the plan names, in the project's own test tools
  and layout, and no others except as R-015 and R-016 allow"
- R-015: an unplanned test is allowed "when it finds real behavior the plan
  missed, where a wrong edit would silently break a requirement", recorded
  "under Deviations in the ledger entry with the situation and what would
  go wrong"
- R-016: "gets one test that fails before the fix, and needs no further
  reason"
- R-017: "when a requirement in the plan changes the behavior and the plan
  names the change"
- R-018: "when the shape of what it calls changed and what it checks stays
  the same"
- R-019: "its tests are deleted with it and no test is written to prove it
  is gone"
- R-020: "a failing test means the code is wrong, and the test is not
  loosened, skipped or deleted"
- R-021: "it may correct the test and record the reason under Deviations in
  the ledger entry. It may not delete or skip a test for this reason."
- R-022: "written as a property test when the requirement is a rule over
  all inputs and the project already has a property-testing library"
- R-023: no skill adds a property-testing library to a project
- R-024: no programming language or testing library named except as an
  example, in this and every other skill

**Tests:** none — instruction text read by agents, checked by reading it

**Done when:** the skill states each point; a search of `skills/` finds no
language or testing library named other than as an example.

### Task 4: Implement, quick and orchestrate (R-012, R-025, R-026, R-027, R-028, R-029, R-030, R-031, R-032, R-033, R-034, R-035, R-036, R-037, R-038, R-039, R-057, R-059, R-060, R-061, R-062, R-063, R-064, R-065, R-066, R-067, R-069, R-082, R-083, R-084)

In `skills/implement/SKILL.md`: the branch step uses the release branch;
the verify step separates checking from committed tests; the ledger entry
gains the `Tests` line; the pull request step repeats evidence and the
`Tests` line and carries screenshots; the merge step cleans up and then
starts `arsenal:finish` when the release is complete.

In `skills/quick/SKILL.md`: the plan gains the `Tests:` line; the saved
plan is written with the write tool; the pull request carries the `Tests`
line and screenshots; the tag question; clean-up after the merge.

In `skills/orchestrate/SKILL.md`: the integration branch is cut from and
merged into the release branch; in the path that skips the integration
branch ("Before starting", last paragraph), each subagent branches from
the release branch and opens its pull request into the release branch, not
the default branch; clean-up of sub-pull-request branches, the integration
branch and worktrees; start `arsenal:finish` when the release is complete.

Put the rules for deleting branches and for the evidence branch in
`skills/finish/SKILL.md` (Task 5) and have these three skills point to it,
so they are written once.

**Exact values:**
- R-012: "a `Tests:` line that names the failing test for a bug, or says
  `none`"
- R-025: "A requirement with a planned test is shown by that test passing;
  every other requirement is shown by a command and its output, or by what
  was seen in the running app."
- R-026: "Nothing written only to check the work is committed to the
  repository."
- R-027: "created outside the repository"
- R-028: scratch files and the saved plan of a quick change are created
  with "the harness's write tool"
- R-029: "the measurement, with the before and after figures and how they
  were taken"
- R-030: "in the ledger entry, one line per requirement, and repeated in
  the pull request description"
- R-031: "a `- **Tests:**` line that lists tests added as planned, tests
  added unplanned, tests changed and tests deleted, with the reason for all
  but the first"
- R-032: the pull request description repeats that line; "For a quick
  change, which has no ledger, the line appears in the pull request
  description only."
- R-033: with no `**Tests:**` block in the plan, the implementer decides by
  the rule of R-004 and R-005 "and lists them on the ledger entry's `Tests`
  line"
- R-034: "a screenshot from before the change and one from after, whenever
  the harness can take them"
- R-035: otherwise "the agent describes in the pull request what it did and
  what it saw"
- R-036: "pushed to the branch `arsenal/evidence`, which never merges into
  `main`"
- R-037: "`<release>/plan-NN/<name>-before.png` and
  `<release>/plan-NN/<name>-after.png`, or under `quick/<branch>/`";
  `arsenal/evidence` "has no shared history with `main`"
- R-038: "committed and pushed from a temporary worktree, so the working
  checkout is not switched"
- R-039:
  "`https://github.com/<owner>/<repo>/blob/arsenal/evidence/<path>?raw=true`"
- R-057: "goes into the branch `release/<release>`, after its review"
- R-059: implement cuts `release/<release>` "from the latest default
  branch", commits the release folder to it and pushes, "when it does not
  exist, except for a release written under arsenal 0.1.0"
- R-060: "cuts each plan's branch from `release/<release>` and opens its
  pull request into it, except for a release written under arsenal 0.1.0"
- R-061: "cuts its integration branch from `release/<release>` and merges
  back into it, not into `main`"
- R-062: a quick change "goes straight into `main` through its own reviewed
  pull request, with no release branch"
- R-063: after a merge: "deletes that branch on the remote and locally,
  removes any worktree it made, switches the checkout to the branch it
  merged into, and pulls"
- R-064: "only when its pull request is merged or it holds no commit its
  target branch lacks"
- R-065: "never deletes a branch holding work that is not merged, and never
  touches a branch it did not cut"
- R-066: own branches: "by name (`release/<release>`,
  `plan/<release>-NN-<name>`, `feat/<release>-phase-NN`); as the head
  branch of a pull request whose base is `release/<release>` or a phase's
  integration branch; or as a branch it cut in the same session"
- R-067: "No clean-up step deletes `arsenal/evidence`."
- R-069: follow `arsenal:finish` in the same session "when every plan in
  the index has a ledger entry whose status is merged, except for a release
  written under arsenal 0.1.0"
- R-082: "A release counts as written under arsenal 0.1.0 when no plan file
  in its folder has a `**Tests:**` block." For it, cut no release branch
  and "use the default branch as the base and the target of pull requests"
- R-083: "asks the user whether to tag a patch version and publish a
  release for the change"
- R-084: asked "once the review fixes are pushed and before the merge. On a
  yes, the version change goes into the same pull request, the patch
  version is the next one after the latest tag, and the tag and the release
  are created after the merge."

**Tests:** none — instruction text read by agents, checked by reading it

**Done when:** the three skills state each point with the values above.

### Task 5: The finish skill (R-068, R-070, R-071, R-072, R-073, R-074, R-075, R-076, R-077, R-078, R-079, R-080, R-081, R-107)

Write `skills/finish/SKILL.md`, with a description that triggers on
finishing a release, on "finish release", and on cleaning up branches left
behind. It holds: the steps for a complete release; what a call by hand
does on an incomplete release and on a 0.1.0 release; the branch deletion
rules (R-063 to R-067); the evidence branch (R-036 to R-039); the tag and
the release notes.

**Exact values:**
- R-068: "`arsenal:finish`, lives at `skills/finish/SKILL.md` and can be
  called by the user at any time"
- R-070: "sets the release's version wherever the project records one, on
  the release branch", before the final pull request
- R-071: "rebases the release branch on the latest default branch, runs the
  project's checks, and opens one pull request from `release/<release>`
  into `main`"
- R-072: "reviewed through `arsenal:review` as a code review against the
  release's spec, plans and ledger; the confirmed findings are fixed; then
  it is merged"
- R-073: "several reviewers each own a range of plans"
- R-074: "When a release has a single plan, so that the final pull
  request's diff is the same as that plan's already reviewed pull request,
  the final pull request is not reviewed a second time."
- R-075: "lists every branch and worktree the release cut. One whose work
  is in `main` is deleted. One whose work is not gets a pull request, a
  review, fixes and a merge, and is then deleted."
- R-076: "reported to the user with its name and what it holds"
- R-077: "creates a git tag and a GitHub release for the version that was
  being worked on", after the merge and the clean-up
- R-078: "follows the repository's existing tag style, or is `v<release>`
  when the repository has no tags"
- R-079: "written from the spec and the ledger in plain language for the
  software's users, grouped as added, changed, removed and fixed"
- R-080: with no GitHub remote: "creates the tag and writes the release
  notes into the conversation, without a GitHub release"
- R-081: by hand on a release whose plans are not all merged: "cleans up as
  R-063 to R-067 allow and reports, and opens no final pull request and
  creates no tag"
- R-107: on a release written under arsenal 0.1.0: "skips the final pull
  request, cleans up and tags"

**Tests:** none — instruction text read by agents, checked by reading it

**Done when:** the skill exists and states each point with the values
above.

### Task 6: The file-tools hook (R-043, R-044, R-045, R-046, R-047, R-048, R-049, R-050, R-051, R-052, R-053, R-054, R-055, R-056, R-090)

Write `hooks/file-tools` as an executable bash script and register it in
`hooks/hooks.json`. It reads the hook input from stdin, takes the command
text from `tool_input.command`, and matches patterns in it. It uses no tool
outside a standard shell environment and no JSON parser. Decide refusal on
the command text only; when the command text cannot be found, allow.

**Exact values:**
- R-043: "registered in `hooks/hooks.json` under `PreToolUse` with the
  matcher `Bash`"
- R-044: "reads no setting, file or environment variable that turns it off"
- R-045: "a bash script that needs no other runtime and no tool beyond a
  standard shell environment"
- R-046: refuses "`sed -i`, `perl -i` and `awk -i inplace`"
- R-047: refuses "a heredoc, `echo` or `printf` whose output is redirected
  to a file with `>` or `>>`"
- R-048: refuses "`tee` writing to a file when it is fed by a heredoc,
  `echo` or `printf`, and allows `tee` capturing another command's output"
- R-049: refuses "`python -c`, `node -e`, `bun -e`, `ruby -e` and
  `perl -e` when the script contains a file write"
- R-050: "an interpreter fed its script through a heredoc
  (`python3 - <<'EOF'`)" is treated as its `-c` or `-e` form
- R-051: allows "`cat`, `head`, `tail`, `less`, `sed -n`, `grep`, `rg` and
  `find`"
- R-052: allows "`git diff main...HEAD > review.diff`, and a build writing
  its output"
- R-053: allows "a redirect to `/dev/null` and a redirect to a file
  descriptor (`>&2`, `2>&1`)"
- R-054: "printing JSON whose `hookSpecificOutput.permissionDecision` is
  `"deny"`". The whole output is one line of this shape, which both
  harnesses accept:
  `{"hookSpecificOutput":{"hookEventName":"PreToolUse","permissionDecision":"deny","permissionDecisionReason":"<the reason of R-055>"}}`
- R-055: the reason is exactly "arsenal: this command writes or edits a
  file through the shell. Use your file write or edit tool instead."
- R-056: "allows any command it does not recognise and any input it cannot
  read"
- R-090: "Each hook is one script that serves both harnesses."

**Tests:**
- R-046: `sed -i`, `perl -i` and `awk -i inplace` are each refused
- R-047: a heredoc, an `echo` and a `printf` redirected to a file with `>`
  or `>>` are each refused
- R-048: `tee` fed by `echo` is refused
- R-048: `tee` capturing another command's output is allowed
- R-049: `python -c`, `node -e`, `bun -e`, `ruby -e` and `perl -e`
  containing a file write are each refused
- R-050: an interpreter fed a script with a file write through a heredoc is
  refused
- R-051: `cat`, `head`, `tail`, `less`, `sed -n`, `grep`, `rg` and `find`
  are each allowed
- R-052: `git diff main...HEAD > review.diff` is allowed
- R-053: `echo` redirected to `/dev/null`, to `>&2` and with `2>&1` is
  allowed
- R-056: an unrecognised command and unreadable input are each allowed

**Done when:** the ten tests pass, and a refusal prints the JSON of R-054
with the reason of R-055.

### Task 7: The test script (R-100, R-101, R-102, R-105)

Write `tests/run` as an executable bash script. It feeds hook input to
`hooks/file-tools` for each case of Task 6 and checks refused or allowed,
and it builds temporary release folders outside the repository for the two
coverage cases of Task 1. It prints each failing case and exits non-zero
when any fails.

**Exact values:**
- R-100: "one bash script, `tests/run`, that needs no runtime beyond bash
  and exits non-zero when a case fails"
- R-101: "at least one refused command for each of R-046, R-047, R-048,
  R-049 and R-050, and at least one allowed command for each of R-048,
  R-051, R-052, R-053 and R-056"
- R-102: "covers the coverage script's check of R-008 and R-009"
- R-105: "bash and adds no runtime dependency"

**Tests:** none — this task is the runner for the tests named in Tasks 1
and 6

**Done when:** `bash tests/run` exits 0, and exits non-zero when a refused
pattern is removed from the hook for a moment.

### Task 8: Codex CLI support (R-085, R-086, R-087, R-088, R-089, R-091, R-092, R-093, R-094, R-095, R-096, R-106)

Add `.codex-plugin/plugin.json` and `.agents/plugins/marketplace.json`.
Write `references/codex.md`. Change `hooks/session-start` so the injected
text ends with the line of R-092, with the plugin root made absolute by the
script. Then test in the Codex on this machine: add this checkout as a
marketplace, install the plugin, run non-interactive sessions with
`codex exec`, and remove the plugin and the marketplace entry afterwards.
If the hooks do not register with one manifest form, try the other.

**Exact values:**
- R-085: names the plugin `arsenal`, "with its skills at `./skills/`,
  written so that Codex registers the hooks in `hooks/hooks.json`. Which
  form does that (no `hooks` field, or a path) is settled by the test"
- R-086: "lists the `arsenal` plugin with this repository as its source"
- R-087: "every arsenal skill is listed under the name `arsenal:<name>`"
- R-088: "the `SessionStart` bootstrap text of `hooks/bootstrap.md` reaches
  a new session"
- R-089: "the file-tools hook refuses a shell edit and allows a shell read"
- R-091: "questions are asked in plain text with numbered options; a
  subagent is started with a clean context and needs the `multi_agent_v2`
  feature; file edits go through `apply_patch`"
- R-092: "ends with one line giving the absolute path of
  `references/codex.md` and telling a Codex session to read it before using
  an arsenal skill. The line is present on both harnesses."
- R-093: "The skills name no Codex tool and gain no new mention of any
  harness's tools; the one mention of Claude Code's workflow tool already
  in `skills/plan-review/SKILL.md` stays."
- R-094: "a review is done as a separate pass and reported as not
  independent, and a parallel phase runs its plans in order"
- R-095: "The README says how to switch on `multi_agent_v2`. Arsenal does
  not change a user's Codex configuration."
- R-096: "The plugin and its marketplace entry are removed from that Codex
  after testing."
- R-106: "codex-cli 0.170.0"

**Tests:** none — packaging and harness behavior, shown by running Codex

**Done when:** the three Codex checks of R-087, R-088 and R-089 are seen in
session output, and `codex plugin list` no longer shows arsenal as
installed.

### Task 9: Housekeeping (R-097, R-098, R-099, R-103, R-104, R-108)

Set the version in both manifests. Update `README.md` and
`hooks/bootstrap.md`. The release folder was committed to the release
branch before Task 1.

**Exact values:**
- R-097: both manifests "say version `0.2.0`"
- R-098: the README "describes the release branch and `arsenal:finish`,
  tests named in plans, the file-tools hook, and installing on Codex with
  the commands that worked in the test"
- R-099: "The bootstrap text names `arsenal:finish`."
- R-103: "its plans merge there, one reviewed pull request goes into
  `main`, and then the tag `v0.2.0` and a GitHub release with notes are
  created"
- R-104: "`docs/arsenal/0.2.0/` is committed to the arsenal repository"
- R-108: "implemented as one single plan"

**Tests:** none — documents and version numbers

**Done when:** both manifests say `0.2.0`, the README covers the four
subjects, and the release is tagged `v0.2.0` with a published release.

## Verification

- `bash tests/run` exits 0.
- `skills/spec/scripts/coverage docs/arsenal/0.2.0` from the working tree
  prints `Coverage OK`.
- `bash hooks/session-start` prints one line of valid JSON whose text ends
  with the absolute path of `references/codex.md`.
- In Claude Code, started with this checkout as the plugin directory, a
  shell command using `sed -i` is refused with the reason of R-055.
- The Codex checks of Task 8.
