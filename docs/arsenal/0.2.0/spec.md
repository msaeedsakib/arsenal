# Arsenal 0.2.0 — Spec

## Purpose

Arsenal 0.1.0 let the implementing agent decide which tests to write, left
branches behind after merging, and ran only on Claude Code. This release is
for people who build with arsenal and want four things:

1. Agents stop piling up tests. The plan names the tests, the implementer
   writes those, and a fresh reviewer flags the rest.
2. Agents stop writing and editing files through the shell.
3. A release ends in `main` through one reviewed pull request, with its
   branches removed, a tag and a published release.
4. Arsenal runs in the Codex CLI as it does in Claude Code.

Arsenal 0.1.0 has no spec and no requirement IDs, so changes below name the
file they change instead of an earlier requirement.

## Not in this release

- Laws as a separate concept: no `laws` folder, no law mark on
  requirements, no hook protecting law files (D-02)
- Proof tools such as Kani, and languages such as Bend (A-01)
- An E2E test layer (D-03)
- CODEOWNERS or branch protection as a gate (D-04)
- A fixed maximum number of tests per plan (D-06)
- A line in pull requests naming the model and harness that did the work
  (D-11)
- Blocking the reading of files through the shell (D-12)
- A switch that turns the file-tools hook off (D-13)
- Video evidence for changes to a screen (D-10)
- The Codex app and Codex cloud (D-18)

## Changed: tests are named in the plan

Files: `skills/plan/SKILL.md`, `skills/plan-review/SKILL.md`,
`skills/quick/SKILL.md`, `skills/spec/scripts/coverage`,
`skills/review/reviewer.md`.

The plan decides which tests exist, before any code is written, so that the
user can see and cut the list.

- **R-001** In the plan format, every task carries a `**Tests:**` block,
  placed after `**Exact values:**` and before `**Done when:**`. (D-05)
- **R-002** Each line of a `**Tests:**` block names one test for its
  behavior and cites its requirement ID, in the form
  `- R-012: a trial started on any date ends exactly 14 days later`. The
  block contains no test code. (D-05)
- **R-003** A task that gets no test writes
  `**Tests:** none — <reason>`. (D-06)
- **R-004** `skills/plan/SKILL.md` tells the plan author to name tests only
  for logic where a wrong edit would silently give a wrong answer:
  calculations, state changes, permission checks, parsing, and boundaries a
  requirement states. (D-06)
- **R-005** `skills/plan/SKILL.md` tells the plan author that wiring,
  layout and configuration get `none` with a reason and are shown by
  running, and that there is no fixed number of tests per plan. (D-06)
- **R-006** An existing test that must change because a requirement in the
  plan changes the behavior is named in the `**Tests:**` block as
  `changes <test name> — R-xxx`. (A-07)
- **R-007** The table in `plans/index.md` has a `Tests` column giving the
  number of tests named in each plan. (A-02)
- **R-008** The coverage script exits 1 and prints the plan file and the
  task heading of every task that has no `**Tests:**` block. (A-03)
- **R-009** The coverage script makes the check of R-008 only when at
  least one plan file in the release folder contains a `**Tests:**`
  block. (A-32, D-21)
- **R-010** In a plan review, the reviewer reports a boundary that a
  requirement states and that no test in the plan is named for. (A-04)
- **R-011** In a plan review, the reviewer reports a test that is named for
  something no requirement states. (A-04)
- **R-012** The plan presented in `arsenal:quick` has a `Tests:` line that
  names the failing test for a bug, or says `none`. (A-06)

## Changed: the testing skill

File: `skills/testing/SKILL.md`.

- **R-013** The skill opens with this bar, quoted exactly: "Tests are good!
  Endless smoke tests, "regression tests" for feature deletions, etc, much
  less good. Tests should be focused, not slop." (D-07)
- **R-014** The skill tells the implementer to write the tests the plan
  names, in the project's own test tools and layout, and no others except
  as R-015 and R-016 allow. (D-05, D-02)
- **R-015** The implementer may add a test the plan did not name when it
  finds real behavior the plan missed, where a wrong edit would silently
  break a requirement, and it records that test under Deviations in the
  ledger entry with the situation and what would go wrong. (D-08)
- **R-016** A bug found during implementation, or confirmed from a review
  finding, gets one test that fails before the fix, and needs no further
  reason. (A-08)
- **R-017** An existing test may change when a requirement in the plan
  changes the behavior and the plan names the change (R-006). (A-07)
- **R-018** An existing test may change when the shape of what it calls
  changed and what it checks stays the same. (A-07)
- **R-019** When behavior is removed on purpose, its tests are deleted with
  it and no test is written to prove it is gone. (A-07)
- **R-020** In every case outside R-017, R-018, R-019 and R-021, the skill
  says a failing test means the code is wrong, and the test is not
  loosened, skipped or deleted. (A-07)
- **R-021** When the implementer believes an existing test is itself wrong
  and no requirement in its plan covers it, it may correct the test and
  record the reason under Deviations in the ledger entry. It may not delete
  or skip a test for this reason. (D-09)
- **R-022** A named test is written as a property test when the requirement
  is a rule over all inputs and the project already has a property-testing
  library. (A-05)
- **R-023** No arsenal skill adds a property-testing library to a
  project. (A-05)
- **R-024** No arsenal skill names a programming language or a testing
  library except as an example. (D-01)

## Changed: verification, the ledger and the pull request

Files: `skills/implement/SKILL.md`, `skills/quick/SKILL.md`,
`skills/orchestrate/SKILL.md`.

Checking that a change works is separate from guarding against future
breakage. Only the second produces committed tests.

- **R-025** A requirement with a planned test is shown by that test
  passing; every other requirement is shown by a command and its output, or
  by what was seen in the running app. (A-11)
- **R-026** Nothing written only to check the work is committed to the
  repository. (A-11)
- **R-027** A script or temporary test written to check something is
  created outside the repository. (A-12)
- **R-028** Where a skill tells the agent to create a scratch file or to
  save the accepted plan of a quick change, it tells the agent to use the
  harness's write tool. (A-42)
- **R-029** When a requirement states a target as a number, its evidence is
  the measurement, with the before and after figures and how they were
  taken. (A-13)
- **R-030** Evidence is written in the ledger entry, one line per
  requirement, and repeated in the pull request description. (A-14)
- **R-031** The ledger entry format gains a `- **Tests:**` line that lists
  tests added as planned, tests added unplanned, tests changed and tests
  deleted, with the reason for all but the first. (A-09)
- **R-032** The pull request description repeats the ledger entry's
  `Tests` line. For a quick change, which has no ledger, the line appears
  in the pull request description only. (A-09)
- **R-033** When a plan has no `**Tests:**` block, the implementer decides
  which tests each task needs by the rule of R-004 and R-005 and lists them
  on the ledger entry's `Tests` line. (D-21)
- **R-034** For a plan or quick change that changes a screen, the pull
  request carries a screenshot from before the change and one from after,
  whenever the harness can take them. (D-10)
- **R-035** Where the harness cannot take a screenshot, the agent describes
  in the pull request what it did and what it saw. (D-10)
- **R-036** Screenshots are pushed to the branch `arsenal/evidence`, which
  never merges into `main`, and the pull request links to them
  there. (D-22)
- **R-037** `arsenal/evidence` has no shared history with `main`.
  Screenshots are stored as `<release>/plan-NN/<name>-before.png` and
  `<release>/plan-NN/<name>-after.png`, or under `quick/<branch>/` for a
  quick change. (A-47)
- **R-038** Screenshots are committed and pushed from a temporary worktree,
  so the working checkout is not switched. (A-47)
- **R-039** A screenshot is linked in the pull request as
  `https://github.com/<owner>/<repo>/blob/arsenal/evidence/<path>?raw=true`.
  (A-47)

## Changed: review

File: `skills/review/reviewer.md`.

- **R-040** In a code review, the reviewer reports a test in the diff that
  neither the plan nor the ledger entry accounts for. (A-10)
- **R-041** In a code review, the reviewer reports an existing test that
  was loosened, skipped or deleted outside R-017, R-018, R-019 and
  R-021. (A-10)
- **R-042** The line in `reviewer.md` that tells reviewers not to report
  missing tests, extra tests or test quality is reworded so that it does
  not contradict R-010, R-011, R-040 and R-041. (A-04, A-10)

## Added: the file-tools hook

Files: `hooks/file-tools` (new), `hooks/hooks.json`.

A hook refuses a shell command that writes or edits a file, so the agent
uses the harness's write and edit tools. It matches patterns in the command
text and is not expected to catch every way of writing a file.

- **R-043** The hook is the script `hooks/file-tools`, registered in
  `hooks/hooks.json` under `PreToolUse` with the matcher `Bash`. (D-13,
  A-43)
- **R-044** The hook is always on: it reads no setting, file or
  environment variable that turns it off. (D-13)
- **R-045** The hook is a bash script that needs no other runtime and no
  tool beyond a standard shell environment. (A-18)
- **R-046** The hook refuses in-place edits: `sed -i`, `perl -i` and
  `awk -i inplace`. (D-12, A-15)
- **R-047** The hook refuses a heredoc, `echo` or `printf` whose output is
  redirected to a file with `>` or `>>`. (A-15)
- **R-048** The hook refuses `tee` writing to a file when it is fed by a
  heredoc, `echo` or `printf`, and allows `tee` capturing another command's
  output. (A-15, A-44)
- **R-049** The hook refuses `python -c`, `node -e`, `bun -e`, `ruby -e`
  and `perl -e` when the script contains a file write. (A-15)
- **R-050** The hook treats an interpreter fed its script through a heredoc
  (`python3 - <<'EOF'`) as it treats that interpreter's `-c` or `-e`
  form. (A-44)
- **R-051** The hook allows reading files through the shell, including
  `cat`, `head`, `tail`, `less`, `sed -n`, `grep`, `rg` and `find`. (D-12)
- **R-052** The hook allows a command's own output redirected to a file,
  such as `git diff main...HEAD > review.diff`, and a build writing its
  output. (A-16)
- **R-053** The hook allows a redirect to `/dev/null` and a redirect to a
  file descriptor (`>&2`, `2>&1`). (A-16, A-44)
- **R-054** The hook refuses a command by printing JSON whose
  `hookSpecificOutput.permissionDecision` is `"deny"`. (A-43)
- **R-055** The reason given with a refusal is exactly: "arsenal: this
  command writes or edits a file through the shell. Use your file write or
  edit tool instead." (A-17, A-45)
- **R-056** The hook allows any command it does not recognise and any
  input it cannot read. (A-43)

## Added: the release branch and finishing

Files: `skills/finish/SKILL.md` (new), `skills/implement/SKILL.md`,
`skills/orchestrate/SKILL.md`, `skills/plan-review/SKILL.md`,
`skills/quick/SKILL.md`.

A release is built on one branch and reaches `main` once, complete.

- **R-057** The pull request for each plan of a release goes into the
  branch `release/<release>`, after its review. (D-14)
- **R-058** `arsenal:plan-review` cuts `release/<release>` from the latest
  default branch as its last step, commits the release folder to it and
  pushes. (A-46)
- **R-059** `arsenal:implement` cuts `release/<release>` the same way when
  it does not exist, except for a release written under arsenal 0.1.0
  (R-082). (A-46, A-52)
- **R-060** `arsenal:implement` cuts each plan's branch from
  `release/<release>` and opens its pull request into it, except for a
  release written under arsenal 0.1.0 (R-082). (D-14, A-52)
- **R-061** A parallel phase cuts its integration branch from
  `release/<release>` and merges back into it, not into `main`. (A-22)
- **R-062** A change made through `arsenal:quick` goes straight into
  `main` through its own reviewed pull request, with no release
  branch. (A-23)
- **R-063** Whenever arsenal merges a pull request, in `implement`,
  `orchestrate`, `quick` or `finish`, it then deletes that branch on the
  remote and locally, removes any worktree it made, switches the checkout
  to the branch it merged into, and pulls. (A-19)
- **R-064** Arsenal deletes a branch only when its pull request is merged
  or it holds no commit its target branch lacks. (A-20)
- **R-065** Arsenal never deletes a branch holding work that is not merged,
  and never touches a branch it did not cut. (A-20)
- **R-066** Arsenal knows a branch as its own in three ways: by name
  (`release/<release>`, `plan/<release>-NN-<name>`,
  `feat/<release>-phase-NN`); as the head branch of a pull request whose
  base is `release/<release>` or a phase's integration branch; or as a
  branch it cut in the same session. (A-54)
- **R-067** No clean-up step deletes `arsenal/evidence`. (A-38)
- **R-068** A new skill, `arsenal:finish`, lives at
  `skills/finish/SKILL.md` and can be called by the user at any
  time. (D-15)
- **R-069** After a merge, `arsenal:implement` and `arsenal:orchestrate`
  follow `arsenal:finish` in the same session when every plan in the index
  has a ledger entry whose status is merged, except for a release written
  under arsenal 0.1.0 (R-082). (D-15, A-51, A-52)
- **R-070** Before the final pull request, `arsenal:finish` sets the
  release's version wherever the project records one, on the release
  branch. (A-26)
- **R-071** `arsenal:finish` rebases the release branch on the latest
  default branch, runs the project's checks, and opens one pull request
  from `release/<release>` into `main`. (D-14, A-51)
- **R-072** The final pull request is reviewed through `arsenal:review` as
  a code review against the release's spec, plans and ledger; the confirmed
  findings are fixed; then it is merged. (D-14, A-24)
- **R-073** For a large release, several reviewers each own a range of
  plans in the review of the final pull request. (A-24)
- **R-074** When a release has a single plan, so that the final pull
  request's diff is the same as that plan's already reviewed pull request,
  the final pull request is not reviewed a second time. (A-40)
- **R-075** At the end of a release, `arsenal:finish` lists every branch
  and worktree the release cut. One whose work is in `main` is deleted. One
  whose work is not gets a pull request, a review, fixes and a merge, and
  is then deleted. (A-21)
- **R-076** Anything `arsenal:finish` cannot resolve is reported to the
  user with its name and what it holds. (A-21)
- **R-077** After the final pull request is merged and the branches and
  worktrees are cleaned up, `arsenal:finish` creates a git tag and a GitHub
  release for the version that was being worked on. (D-16)
- **R-078** The tag follows the repository's existing tag style, or is
  `v<release>` when the repository has no tags. (A-25)
- **R-079** The release notes are written from the spec and the ledger in
  plain language for the software's users, grouped as added, changed,
  removed and fixed. (D-16, A-25)
- **R-080** Where the repository has no GitHub remote, `arsenal:finish`
  creates the tag and writes the release notes into the conversation,
  without a GitHub release. (A-41)
- **R-081** Called by hand on a release whose plans are not all merged,
  `arsenal:finish` cleans up as R-063 to R-067 allow and reports, and opens
  no final pull request and creates no tag. (A-51)
- **R-082** A release counts as written under arsenal 0.1.0 when no plan
  file in its folder has a `**Tests:**` block. For such a release
  `arsenal:implement` and `arsenal:orchestrate` cut no release branch and
  use the default branch as the base and the target of pull
  requests. (D-21, A-52)
- **R-107** Called on a release written under arsenal 0.1.0, which has no
  release branch, `arsenal:finish` skips the final pull request, cleans up
  and tags. (D-21, A-51)
- **R-083** In `arsenal:quick`, the agent asks the user whether to tag a
  patch version and publish a release for the change. (D-17)
- **R-084** The question of R-083 is asked once the review fixes are pushed
  and before the merge. On a yes, the version change goes into the same
  pull request, the patch version is the next one after the latest tag, and
  the tag and the release are created after the merge. (A-48)

## Added: Codex CLI support

Files: `.codex-plugin/plugin.json` (new),
`.agents/plugins/marketplace.json` (new), `references/codex.md` (new),
`hooks/bootstrap.md`, `hooks/session-start`, `README.md`.

- **R-085** Arsenal has a `.codex-plugin/plugin.json` naming the plugin
  `arsenal`, with its skills at `./skills/`, written so that Codex
  registers the hooks in `hooks/hooks.json`. Which form does that (no
  `hooks` field, or a path) is settled by the test of R-096. (A-27)
- **R-086** Arsenal has a `.agents/plugins/marketplace.json` that lists the
  `arsenal` plugin with this repository as its source. (A-27)
- **R-087** In the Codex CLI, every arsenal skill is listed under the name
  `arsenal:<name>`. (D-18, A-31)
- **R-088** In the Codex CLI, the `SessionStart` bootstrap text of
  `hooks/bootstrap.md` reaches a new session, as it does on Claude
  Code. (D-19, A-31)
- **R-089** In the Codex CLI, the file-tools hook refuses a shell edit and
  allows a shell read. (A-31)
- **R-090** Each hook is one script that serves both harnesses. (A-28)
- **R-091** `references/codex.md` at the plugin root maps what differs on
  Codex: questions are asked in plain text with numbered options; a
  subagent is started with a clean context and needs the `multi_agent_v2`
  feature; file edits go through `apply_patch`. (A-53, A-49)
- **R-092** The bootstrap text ends with one line giving the absolute path
  of `references/codex.md` and telling a Codex session to read it before
  using an arsenal skill. The line is present on both harnesses. (A-49)
- **R-093** The skills name no Codex tool and gain no new mention of any
  harness's tools; the one mention of Claude Code's workflow tool already
  in `skills/plan-review/SKILL.md` stays. What differs on Codex is in
  `references/codex.md` only. (A-53)
- **R-094** On a Codex where subagents are not switched on, a review is
  done as a separate pass and reported as not independent, and a parallel
  phase runs its plans in order. (A-30)
- **R-095** The README says how to switch on `multi_agent_v2`. Arsenal does
  not change a user's Codex configuration. (A-30)
- **R-096** R-087, R-088 and R-089 are tested by installing arsenal
  temporarily into the Codex on Saeed's machine and running non-interactive
  sessions there. The plugin and its marketplace entry are removed from
  that Codex after testing. (D-20, A-31)

## Changed: release housekeeping

Files: `.claude-plugin/plugin.json`, `.codex-plugin/plugin.json`,
`README.md`, `hooks/bootstrap.md`, `tests/run` (new).

- **R-097** The plugin manifests for Claude Code and for Codex both say
  version `0.2.0`. (A-33)
- **R-098** The README describes the release branch and `arsenal:finish`,
  tests named in plans, the file-tools hook, and installing on Codex with
  the commands that worked in the test of R-096. (A-34)
- **R-099** The bootstrap text names `arsenal:finish`. (A-34)
- **R-100** Arsenal's tests are one bash script, `tests/run`, that needs no
  runtime beyond bash and exits non-zero when a case fails. (A-35, A-50)
- **R-101** `tests/run` covers the file-tools hook: at least one refused
  command for each of R-046, R-047, R-048, R-049 and R-050, and at least
  one allowed command for each of R-048, R-051, R-052, R-053 and
  R-056. (A-35)
- **R-102** `tests/run` covers the coverage script's check of R-008 and
  R-009. (A-35)
- **R-103** Arsenal 0.2.0 is itself built on `release/0.2.0`: its plans
  merge there, one reviewed pull request goes into `main`, and then the tag
  `v0.2.0` and a GitHub release with notes are created. (A-36)
- **R-104** `docs/arsenal/0.2.0/` is committed to the arsenal
  repository. (A-37)

## Stack and constraints

- **R-105** Everything arsenal runs (the hooks, the coverage script and
  `tests/run`) is bash and adds no runtime dependency. (A-18, A-35)
- **R-106** Codex support is verified against codex-cli 0.170.0, the
  version installed on Saeed's machine. (D-20)
- **R-108** Arsenal 0.2.0 is implemented as one single plan. (D-23)
