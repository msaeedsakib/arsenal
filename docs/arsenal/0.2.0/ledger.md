# Arsenal 0.2.0 — Ledger

## Plan 01: arsenal-0.2.0
- **Status:** merged
- **Requirements:**
  - R-001, R-002, R-003, R-004, R-005, R-006, R-007 — `skills/plan/SKILL.md`
    has the "Naming the tests" section, the `**Tests:**` block in the plan
    format between `**Exact values:**` and `**Done when:**`, the
    `changes <test name> — R-xxx` form, and the `Tests` column in the index
    format.
  - R-008 — test "a task without a Tests block fails the check and is
    named" passes in `bash tests/run`.
  - R-009 — test "a folder where no plan has a Tests block is not failed"
    passes in `bash tests/run`.
  - R-010, R-011, R-040, R-041, R-042 — `skills/review/reviewer.md` bar item
    5 states the two plan-review findings and the two code-review findings;
    the "do not report" line now reads "missing tests, extra tests or test
    quality beyond what item 5 names".
  - R-012 — `skills/quick/SKILL.md` step 3 has the `Tests:` line.
  - R-013, R-014, R-015, R-016, R-017, R-018, R-019, R-020, R-021, R-022,
    R-023 — `skills/testing/SKILL.md`, rewritten: opens with the quoted
    bar; "Which tests to write", "Tests that already exist" and "Writing
    one" state each rule.
  - R-024 — a search of `skills/` and `references/` for language and
    testing-library names returned nothing.
  - R-025, R-026, R-027, R-028, R-029, R-030, R-031, R-032, R-033, R-034,
    R-035 — `skills/implement/SKILL.md` steps 3 to 6 and
    `skills/quick/SKILL.md` steps 4 to 6; the no-`Tests`-block rule is in
    `skills/testing/SKILL.md`.
  - R-036, R-037, R-038, R-039 — `skills/finish/SKILL.md`, "The evidence
    branch".
  - R-043, R-044, R-045, R-054, R-055 — `hooks/file-tools` is registered
    under `PreToolUse` / `Bash` in `hooks/hooks.json`; it reads no switch;
    a refusal printed
    `{"hookSpecificOutput":{"hookEventName":"PreToolUse","permissionDecision":"deny","permissionDecisionReason":"arsenal: this command writes or edits a file through the shell. Use your file write or edit tool instead."}}`.
    In a Claude Code session loaded from this checkout, `sed -i` came back
    as "PreToolUse:Bash hook error: arsenal: this command writes or edits a
    file through the shell. Use your file write or edit tool instead." and
    the file was unchanged.
  - R-046, R-047, R-048, R-049, R-050, R-051, R-052, R-053, R-056 — the ten
    tests named in Task 6 pass in `bash tests/run` ("All 52 cases
    passed."). With the `sed` rule removed for a moment the run reported
    four R-046 failures and exited 1.
  - R-057, R-059, R-060, R-069, R-082 — `skills/implement/SKILL.md` steps 2,
    8 and 9.
  - R-058 — `skills/plan-review/SKILL.md` step 5.
  - R-061 — `skills/orchestrate/SKILL.md` steps 1 and 5, and the path
    without an integration branch under "Before starting".
  - R-062, R-083, R-084 — `skills/quick/SKILL.md` steps 4, 6 and 7.
  - R-063, R-064, R-065, R-066, R-067 — `skills/finish/SKILL.md`, "Clean
    up"; `implement`, `orchestrate` and `quick` point to it.
  - R-068, R-070, R-071, R-072, R-073, R-074, R-075, R-076, R-077, R-078,
    R-079, R-080, R-081, R-107 — `skills/finish/SKILL.md`.
  - R-085, R-086 — `.codex-plugin/plugin.json` (no `hooks` field; Codex
    registered both hooks from `hooks/hooks.json`) and
    `.agents/plugins/marketplace.json`; `codex plugin marketplace add` and
    `codex plugin add arsenal@arsenal` installed version 0.2.0.
  - R-087 — a `codex exec` session listed `arsenal:brainstorm`,
    `arsenal:debugging`, `arsenal:finish`, `arsenal:implement`,
    `arsenal:orchestrate`, `arsenal:plan`, `arsenal:plan-review`,
    `arsenal:quick`, `arsenal:review`, `arsenal:spec`, `arsenal:testing`.
  - R-088, R-092 — a `codex exec` session quoted the last line of the
    bootstrap text: "On Codex, read
    /home/msaeedsakib/.codex/plugins/cache/arsenal/arsenal/0.2.0/references/codex.md
    before using an arsenal skill." The same line, with this checkout's
    path, appeared in the Claude Code session.
  - R-089, R-090 — in a `codex exec` session `sed -i 's/hello/bye/'
    note.txt` returned "Command blocked by PreToolUse hook: arsenal: this
    command writes or edits a file through the shell. Use your file write
    or edit tool instead." and `cat note.txt` printed `hello`. The same two
    scripts ran in Claude Code.
  - R-091, R-093, R-094 — `references/codex.md`; a search of `skills/` for
    Codex tool names returned nothing.
  - R-095, R-098 — `README.md`: "Tests come from the plan", "The hooks",
    and "Install" with a Codex CLI section.
  - R-096 — after the test, `codex plugin remove` and
    `codex plugin marketplace remove` ran, `~/.codex/config.toml` was
    identical to its copy from before the install, and
    `codex plugin list` no longer showed arsenal.
  - R-097 — both manifests say `0.2.0`; `claude plugin validate .` printed
    "Validation passed".
  - R-099 — `hooks/bootstrap.md` names `arsenal:finish`.
  - R-100, R-101, R-102, R-105 — `tests/run` is one bash script; it
    printed "All 52 cases passed." and exited 0.
  - R-103 — `release/0.2.0` was cut from `main` and pushed, and this plan's
    pull request goes into it. The final pull request, the tag `v0.2.0` and
    the GitHub release follow this entry, by the steps of
    `skills/finish/SKILL.md`.
  - R-104 — `docs/arsenal/0.2.0/` was committed to `release/0.2.0` in
    commit 9921839.
  - R-106 — the Codex sessions ran on codex-cli 0.170.0.
  - R-108 — this is the only plan.
- **Tests:** planned: the two coverage tests of Task 1 and the ten hook
  tests of Task 6, as cases in `tests/run`; unplanned: "a one-line script
  that only reads is allowed" (two cases under R-049) and "an interpreter
  fed a heredoc that only reads is allowed" (one case under R-050), "text
  inside a heredoc body or a quoted string is not read as a redirect" (two
  cases under R-056), and "a quoted string that spans lines is one string"
  (one refused case under R-047, one allowed case under R-056) — see
  Deviations; changed: none; deleted: none
- **Deviations:**
  - Unplanned tests. A commit message passed through a heredoc, and a
    quoted `"a > b"`, are the commonest places a redirect pattern matches
    text that is not a command; without the two guard cases a later edit
    to the patterns could start refusing ordinary commits.
  - Bug from the code review, with its failing tests first. The hook split
    the command into lines before stripping quotes, so
    `echo "line one⏎line two" > notes.md` was allowed and a commit message
    with `sed -i` on its second line was refused. Quotes are now stripped
    over the whole command.
  - Unplanned tests. While writing the hook, a write pattern that matched
    any `.write(` refused `python -c "import sys; sys.stdout.write(...)"`.
    A pattern that broad would refuse every one-line script that prints, so
    three allowed cases guard it.
  - Codex does not run a plugin's hooks until the user has approved them in
    its interface, and `codex exec` cannot ask. The Codex checks of R-088
    and R-089 were run with `--dangerously-bypass-hook-trust`, which lets
    hooks run for that one invocation and saves nothing. On Codex the
    always-on hook of R-044 therefore starts working only after that
    approval. The README says so.
  - R-098: the README gives `codex plugin marketplace add
    msaeedsakib/arsenal`. The test used the path of this checkout instead,
    because 0.2.0 is not on `main` yet. Codex's own help lists both forms.
  - Codex installs a plugin from the repository's latest commit, not from
    the working tree. The README notes it under "Working on arsenal
    itself".
  - `references/codex.md` does not name the setting that gives a spawned
    agent a clean context. Subagents are switched off in the Codex used for
    testing, so the setting could not be checked.
  - `skills/review/SKILL.md` was edited too, though the spec does not list
    it: its one-line statement of the bar and its dispatch list now match
    `reviewer.md`.
  - R-034 to R-039 have no screenshots here: this release changes no
    screen.
  - R-108 was added to the spec after its approval, from D-23.
- **For later plans:** none
