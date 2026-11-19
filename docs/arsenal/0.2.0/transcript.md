# Arsenal 0.2.0 — Brainstorm Transcript

## Brief

From the conversation on 2026-11-19, in Saeed's words:

> we write Unit Tests once & then when we modify our codebase later we run
> those tests two make sure that our codebase will pass those tests but when
> writing code with claude code our any other agent every new release adds
> bloat of tests & this pile never stops it keeps growing

> i find these all dumb tests -> big waste of inference & time & compute....
> next time i give a task to agent 80% chances are that it will write a new
> test just to prove its implementation as correct or even manipulate the
> previous test to make them pass

> the real thing what matters is E2E & a law which can never ever be broken

> let's exclude E2E from our list

On gating law changes with CODEOWNERS and branch protection:

> this is something not possible at all... the commits are done here the
> work is done here commited by my own GH account

> in hooks we can implement the blockage all kind of sed heredocs python
> scripts etc to block usage of this & advise to use proper read write edit
> tools... additionally in this 0.2.0 release i want to add full codex
> support

Added when the agenda was shown:

> in the end when all plans are finished agents tend to leave feat or
> release branches they cut stay... what i want is that in the end all work
> has to pr'd review that pr fix & then merged to main & clean up of stale
> branch

So 0.2.0 has four parts, the fourth being that every piece of work ends
merged into `main` through a reviewed pull request, with the branches it cut
removed:

1. Laws-first testing in place of example unit tests, with laws carried
   through the spec, the plans and the coverage script, and protected by a
   hook. Verification becomes evidence in the pull request and the ledger,
   not committed tests. No E2E layer. No CODEOWNERS or branch protection.
2. A hook that blocks reading and editing files through sed, heredocs,
   Python scripts and the like, and tells the agent to use the Read, Write
   and Edit tools.
3. Full Codex support.

## Research

### Arsenal 0.1.0 as it stands

- `skills/testing/SKILL.md` asks for a test per behavior a requirement
  names, per piece of branching logic and per bug fix. It has no notion of
  a law and does not stop an agent editing an existing test.
- `skills/implement/SKILL.md` step 4 already accepts three kinds of
  evidence for a requirement: a passing test by name, a command and its
  result, or what was observed in the running app.
- `skills/review/reviewer.md` tells reviewers not to report missing tests,
  extra tests or test quality unless a requirement names the test.
- `skills/review/SKILL.md` and `skills/quick/SKILL.md` both require a
  failing test before a bug fix.
- `skills/spec/scripts/coverage` checks decisions against the spec and
  requirements against the plans. It finds IDs by the shape
  `- **R-001** ...` and knows nothing about kinds of requirement.
- The only hook is `SessionStart`, which injects `hooks/bootstrap.md`. Its
  command uses `${CLAUDE_PLUGIN_ROOT}`.
- Harness-specific wording in the skills is thin: an ask-question tool in
  brainstorm, subagents and worktrees in review, plan-review and
  orchestrate. Each already has a "without it" fallback.
- `implement` step 8 merges the pull request and says nothing about
  deleting the branch. `orchestrate` removes the worktrees it created but
  not the integration branch or the sub-pull-request branches. `quick`
  merges and stops.
- There is no `docs/arsenal/0.1.0/` folder: 0.1.0/D-08 built arsenal
  without a spec or plans. Its decisions are in `research/03-decisions.md`,
  which is not in git.

### Bend and laws (looked up 2026-11-19)

- Bend 2 was released on 17 September 2026. Laws are proved only about
  code written in Bend. It compiles to C, Metal, CUDA and JavaScript, with
  no Rust target.
- The equivalent in existing codebases is a property test (`proptest` in
  Rust, `fast-check` in TypeScript), which checks a rule against many
  generated inputs. That is sampling, not proof. Kani gives bounded proof
  for Rust.

### Codex (codex-cli 0.170.0 is installed on this machine)

- Codex has plugins and marketplaces (`codex plugin add`,
  `codex plugin marketplace`). The features `plugins` and `hooks` are
  stable and on.
- The manifest is `.codex-plugin/plugin.json`. Codex falls back to
  `.claude-plugin/plugin.json` at lower precedence, so arsenal may already
  load in some form. Not yet tried.
- Skills are `SKILL.md` files in the plugin's `skills/` directory, the same
  layout arsenal has.
- Codex hooks use the same event names and output shape as Claude Code:
  `SessionStart` can return `additionalContext`, and `PreToolUse` can
  return a `deny` decision with a reason.
- Superpowers ships a `.codex-plugin/plugin.json` with `"hooks": {}`, so it
  runs no hooks in Codex. The reason is not known yet.
- Subagents in Codex go through `spawn_agent`, behind the `multi_agent_v2`
  feature, which is stable but off on this machine.
- Still to find out by trying it: which variable gives a hook the plugin's
  root in Codex, what Codex's shell and file-edit tools are called in a
  hook matcher, how skills are named when invoked, and whether Codex has an
  ask-question tool.

## Agenda

1. What a law is
2. Laws in the chain
3. Other tests and verification
4. Protecting laws
5. The file-tools hook
6. Finishing: merged to main, branches cleaned up
7. Codex support
8. Release housekeeping

**Revised during topic 1**, after laws were dropped as a separate concept.
The topic numbers below replace the ones above:

1. What a law is (closed: dropped)
2. Tests come from the plan
3. Unplanned tests and changed tests
4. Verification as evidence
5. The file-tools hook
6. Finishing: merged to main, branches cleaned up
7. Codex support
8. Release housekeeping

## Topic 1: What a law is

### Explanation

Every later topic leans on this definition. If it is loose, agents will
call any test a law and the pile comes back under a new name.

**The definition I propose.** A law is a statement that must hold for every
input or every state, written in the requirement's own words and not in
terms of how the code is built. "A closed ticket never reopens." "Decoding
an encoded value gives back the original." "For every start date, a trial
ends exactly 14 days later."

**What is not a law.**

- A check of one fixed input against one fixed output. That is an example.
- A single constant, such as "the Pro plan costs $9". That is configuration
  and needs no test.
- A check that a mock was called.
- A statement that only repeats the implementation, so it can never
  disagree with it.

**What form a law takes in code.** Three options:

1. *Property tests only.* The law is a test that runs the rule against many
   generated inputs: `proptest` in Rust, `fast-check` in TypeScript. Simple,
   one form everywhere. It samples inputs; it does not prove.
2. *Property tests, plus types as evidence.* Same as 1, but when a type
   already makes the bad state impossible to write (a Rust enum, a
   TypeScript discriminated union), that counts as the law being met and no
   test is written for it. Fewer tests still, and the compiler enforces it
   for free.
3. *Also real proof tools,* such as Kani for Rust. The strongest check, but
   it needs setup per project, runs slowly on this machine, and has no
   practical TypeScript counterpart.

I lean to option 2, and to leaving proof tools out of 0.2.0.

**Where laws live.** The protecting hook in topic 4 must be able to tell a
law file from any other file without thinking. Three options:

1. *A folder name.* Any file under a directory called `laws` is a law. It
   works in Rust (`tests/laws/main.rs`), in TypeScript
   (`src/billing/laws/*.test.ts`) and in a monorepo with many packages, and
   the hook's rule is one path pattern.
2. *A file name,* such as `*.law.test.ts`. It keeps a law next to its code
   but needs a different pattern per language, and Rust has no natural form
   for it.
3. *A marker comment inside the file.* The hook would have to read file
   contents, and an agent can remove the marker.

I lean to option 1.

**What "never broken" honestly means here.** A property test checks a few
hundred random inputs on each run. It is far stronger than ten hand-picked
examples and it does not change when code is refactored, but it is not a
mathematical proof. The guarantee in 0.2.0 is: the rule is checked widely on
every run, and no agent can quietly weaken it.

### Q&A

**Comment from Saeed on "property tests only ... `proptest` in Rust and
`fast-check` in TypeScript":**

> this is a point to ponder.... true that i built this plugin for myself but
> also true that its opensource & i would love if people will use it... what
> if other people use other stacks... shouldn't we give general statement...
> & idk dude if every language has equivalent of laws

**Reply:** Nearly every mainstream language has a property-testing library
(Hypothesis for Python, jqwik for Java, FsCheck for .NET, StreamData for
Elixir, QuickCheck for Haskell, built-in fuzzing in Go). Where one has none,
a law can be a loop over generated inputs in the ordinary test tool. The
skill will therefore name no library and no language except as examples.

**Q1.** What form may a law take in code?
Options: Property tests + types (recommended) / Property tests only / Also
proof tools
**Answer:** "wait man what am i reading...." with two screenshots of an AI
answer to "these laws isn't this just another name for 'property tests'".
The answer said no: Bend laws are formal proofs checked at compile time and
cover every input, while property tests are sample runs at runtime that can
miss rare cases.

**Reply:** The screenshot is correct. A property test is not a proof. Real
proof needs a language built for it or a verifier for the language in use,
and outside Rust there is none in everyday use. What other languages do
allow is a ladder of strength: a type that makes the bad state impossible
(proof by the compiler), checking every case of a small finite set (proof
for that set), a bounded proof tool where the language has one, and a
property test (sampling) for everything else. Q1 is re-asked with that
ladder.

**Saeed, with a test-versus-code count of his mail server (62,328 lines of
software, 50,963 lines of tests; Rust crates 53.1% tests, almost all inline
`mod tests`):**

> then why change the behavior.... let the agent's write unit tests since
> they are the convention but stop them from writing useless tests idk dude
> i'm so confused... can you do web research & get back to me with results &
> give me a clear way out.... i told you my problem is "agents wasting
> tokens & usage limits on writing useless unit tests which they dont
> respect at all rewrite them change them & keep adding more & more every
> release to a point that its bloat

> now i dont mean that TDD is bad its not obviously not but its bad if done
> the wrong way

**Research (web, 2026-11-19):**

- Tests an agent writes for its own change add little. One experiment over
  111 tasks found agents allowed to write tests succeeded 65.3% of the time
  against 66.2% for those that did not. A 2026 study found such tests often
  accept incorrect implementations. A vendor audit of 1,000 agent-written
  unit tests judged 90.4% of little value (one company's audit).
- Agents editing, skipping or deleting tests to get a green run is
  documented as "test tampering". Pressure makes it worse: one study saw
  test edits rise from 1 of 90 runs to 8 of 90 after a one-sentence
  deadline. Making test files read-only helped but did not end attempts.
- Instructions alone are weak, and can backfire: an ETH Zurich study found
  agents that were told about testing in AGENTS.md ran and wrote more of
  it, often unnecessarily. Writers who studied this recommend a limit
  enforced outside the prompt and a check by something other than the
  author.
- No source gave a healthy ratio of test code to software code.

**Reply:** Proposed a simpler direction that drops laws as a separate
concept: tests stay ordinary tests, the plan names the tests each task gets,
the implementer writes only those, verification goes to the pull request
and ledger, and the fresh reviewer flags any test the plan did not name and
any existing test that was changed without a requirement changing. Noted
that a hook cannot protect tests in Saeed's Rust code, because they are
inline in the source files.

**Saeed:**

> what is your final agenda then & according to this new point of view what
> is your take about the edge cases. since now tests are being defined
> during plan time what happens when during implementation something
> behaves differently i hope the agent writes a test where its really
> needed & doesn't wastes tokens. there is a line in my global CLAUDE.md
> i.e., "Tests are good! Endless smoke tests, "regression tests" for
> feature deletions, etc, much less good. Tests should be focused, not
> slop." ... i think it conveys the message clearly.

### Decisions

- **D-01** Arsenal's skills name no programming language and no testing
  library except as examples, because the plugin is open source and others
  use other stacks.
- **D-02** Laws are not a separate concept in arsenal. There is no `laws`
  folder, no law mark on requirements and no hook protecting law files.
  Agents keep writing ordinary tests in the project's own convention.
- **D-03** Arsenal adds no E2E test layer.
- **D-04** Arsenal does not use CODEOWNERS or branch protection to gate
  anything, because the agent commits and merges as the user's own GitHub
  account.
- **A-01** Proof tools such as Kani and languages such as Bend are not part
  of 0.2.0 — they need per-project setup or a rewrite, and have no
  counterpart in most languages.

## Topic 2: Tests come from the plan

### Explanation

Today the implementing agent decides which tests to write, while it is
writing the code. That is where the pile comes from. This topic moves the
decision to the plan, which is written once, reviewed, and read by you
before any code exists.

**What a plan would carry.** Each task gets a `Tests` block: one line per
test, named for the behavior and citing its requirement. No test code, as
plans carry no code today.

```markdown
**Tests:**
- R-012: a trial started on any date ends exactly 14 days later
- R-013: an expired trial cannot start a new session
```

or, when the task needs none:

```markdown
**Tests:** none — wiring only, verified by running it
```

Writing `none` with a reason makes the absence a decision, not a gap.

**Which requirements get a test.** This is the real choice. Three options:

1. *Every requirement gets one.* Simple to check, and it rebuilds the pile.
2. *Only where a wrong edit would silently give a wrong answer:*
   calculations, state changes, permission checks, parsing, and boundaries
   a requirement states. Everything else (wiring, layout, configuration) is
   shown by running it, with `none` and a reason. No number is fixed.
3. *A fixed cap per plan,* such as at most 10 tests. The research suggested
   budgets, and a number is easy to enforce. But any number is arbitrary: it
   is too many for a settings page and too few for a parser.

I lean to option 2.

**How the test is written.** The project's own convention decides the tool
and the layout (D-02). The `testing` skill opens with the bar from your
CLAUDE.md: tests are good, focused tests, no slop. When a requirement is a
rule over all inputs and the project already has a property-testing
library, the test is written as a property test. Arsenal does not add that
library to a project by itself.

**How you see it and how it is checked.**

- The plans index gets a `Tests` column with the count per plan, so the
  total for a release is visible on one screen before anything is built.
- The coverage script fails when a task has no `Tests` block at all.
- `plan-review` treats two things as findings: a boundary a requirement
  states with no test named for it, and a test named for something no
  requirement states.

**The quick track.** The short plan agreed in the conversation gets one
more line, `Tests:`, naming the failing test for a bug, or `none`.

### Q&A

**Q1.** When a plan is written, which requirements get a test named for
them in the task's Tests block?
Options: Only silent-failure logic (recommended) / Silent-failure logic + a
cap / Every requirement
**Answer:** Only silent-failure logic (Recommended)

### Decisions

- **D-05** Every task in a plan carries a `Tests` block that names each
  test the task gets, one line per test, named for the behavior and citing
  its requirement ID, with no test code. The implementer writes the tests
  the plan names.
- **D-06** A plan names tests only for logic where a wrong edit would
  silently give a wrong answer: calculations, state changes, permission
  checks, parsing, and boundaries a requirement states. Wiring, layout and
  configuration get `**Tests:** none — <reason>` and are shown by running.
  There is no fixed number of tests per plan.
- **D-07** The `testing` skill opens with the bar from Saeed's CLAUDE.md:
  "Tests are good! Endless smoke tests, "regression tests" for feature
  deletions, etc, much less good. Tests should be focused, not slop."
- **A-02** The plans index gets a `Tests` column giving the number of named
  tests per plan — so the total for a release can be seen and cut before
  anything is built.
- **A-03** The coverage script fails when a task in a plan file has no
  `Tests` block — a missing block is the one part of this that can be
  checked mechanically.
- **A-04** `plan-review` reports as findings a boundary a requirement
  states that has no test named for it, and a test named for something no
  requirement states — both have a concrete failure and fit the existing
  bar.
- **A-05** A named test is written as a property test when the requirement
  is a rule over all inputs and the project already has a property-testing
  library. Arsenal never adds such a library to a project — a new
  dependency is the user's decision.
- **A-06** The plan in `arsenal:quick` gets a `Tests:` line naming the
  failing test for a bug, or `none` — the same rule on the short track.

## Topic 3: Unplanned tests and changed tests

### Explanation

The plan now fixes which tests are planned. Two leaks remain: a test the
plan did not name, and a change to a test that already exists.

**Unplanned tests.** The plan cannot foresee everything. Three options:

1. *Allowed with a reason.* The implementer may add a test the plan did not
   name when it finds real behavior the plan missed, where a wrong edit
   would silently break a requirement. It records the test under Deviations
   in the ledger entry in one line: the situation and what would go wrong.
2. *Not allowed.* The implementer stops and asks you. Nothing slips
   through, and every surprise interrupts you.
3. *Free.* As today.

I lean to option 1. A bug found during the work keeps the existing rule,
one failing test first, and counts as its own reason.

**Changing a test that already exists.** There are honest reasons to touch
one, and one dishonest reason:

- *The behavior changed on purpose.* A requirement in this plan changes
  what the code should do, so the test must change. The plan names it in
  the `Tests` block: `changes <test name> — R-031`.
- *The shape changed, not the behavior.* A function was renamed or takes a
  new argument. The test is updated to call it, and what it checks stays
  the same.
- *The behavior was removed on purpose.* Its tests are deleted with it, and
  no test is written to prove it is gone.
- *The test is in the way.* It fails, and loosening, skipping or deleting
  it is a smaller edit than fixing the code. This is the tampering the
  research describes.

The rule I propose: an existing test changes only for the first three
reasons. Otherwise a failing test means the code is wrong.

One case is left: the implementer believes an existing test is itself
wrong, and no requirement covers it. Either it may fix the test and record
why as a deviation, or it must stop and ask you. 0.1.0 says to ask before
deleting or skipping.

**Making it visible.** The ledger entry gets a `Tests` line listing tests
added as planned, added unplanned, changed and deleted, with the reason for
all but the first. The pull request description repeats it. On the quick
track it goes in the pull request description only.

**Who checks.** The reviewer's instructions today say not to report on
tests. They gain one more kind of finding for code reviews: a test the plan
and the ledger do not account for, or an existing test that was loosened,
skipped or deleted without one of the three reasons.

**The limit.** The reviewer is an agent, and tests that sit inside source
files cannot be guarded by a hook. This is a check by a fresh agent with no
stake in the change passing, not a hard block.

### Q&A

**Q1.** During implementation, may the agent add a test that the plan did
not name?
Options: Yes, with a reason (recommended) / No, it must ask me / Yes, freely
**Answer:** Yes, with a reason (Recommended)

**Q2.** The agent believes an existing test is itself wrong (not just in
the way), and no requirement in its plan covers it. What may it do?
Options: Fix it, record why (recommended) / Stop and ask me / Leave it,
report it
**Answer:** Fix it, record why (Recommended)

### Decisions

- **D-08** The implementer may add a test the plan did not name when it
  finds real behavior the plan missed, where a wrong edit would silently
  break a requirement. It records the test under Deviations in the ledger
  entry with the situation and what would go wrong.
- **D-09** When the implementer believes an existing test is itself wrong
  and no requirement in its plan covers it, it may correct the test and
  record the reason under Deviations in the ledger entry. It may not delete
  or skip a test for this reason.
- **A-07** An existing test may otherwise change for three reasons only: a
  requirement in the plan changes the behavior, and the plan names it as
  `changes <test name> — R-xxx`; the shape of what it calls changed and
  what it checks stays the same; or the behavior was removed on purpose, in
  which case its tests are deleted and no test is written to prove it is
  gone. In every other case a failing test means the code is wrong — this
  is the rule that names tampering.
- **A-08** A bug found during implementation, or confirmed from a review
  finding, gets one failing test before the fix, as in 0.1.0, and needs no
  further reason — the bug is the reason.
- **A-09** The ledger entry gets a `Tests` line listing tests added as
  planned, added unplanned, changed and deleted, with the reason for all
  but the first. The pull request description repeats it. On the quick
  track it appears in the pull request description only — so every test
  touched is visible in one place.
- **A-10** For code reviews, `reviewer.md` gains one kind of finding: a
  test that neither the plan nor the ledger accounts for, or an existing
  test that was loosened, skipped or deleted outside A-07 and D-09 — the
  check has to be made by an agent that did not write the change.

## Topic 4: Verification as evidence

### Explanation

Checking that a change works and guarding against future breakage are two
jobs. Agents do the first by writing a test and committing it, which is how
one-off checks end up in the suite for ever. This topic separates them.

**The rule.** Verification is run, read and written down. It is not
committed. A requirement with a planned test is shown by that test passing.
Every other requirement is shown by a command and its output, or by what
was seen in the running app. 0.1.0 already accepts those three kinds of
evidence; what is new is saying outright that nothing written only to check
the work stays in the repository.

**Scratch work.** A script or a temporary test written to check something
lives outside the repository and is never committed.

**Where the evidence goes.** In the ledger entry, one line per requirement,
as today, and in the pull request description. The reviewer already checks
each line of evidence against the code.

**Numbers.** When a requirement states a target (a time, a size, a count),
the evidence is the measurement, with the before and after figures and how
they were taken.

**Screens.** The first video's point was that a change you can see should
be shown, not described. Two options:

1. *Before and after screenshots in the pull request,* for any change to a
   screen, when the harness can take them. Where it cannot, the agent says
   what it did and what it saw, as today. You can judge a UI change without
   pulling the branch.
2. *Leave it as today.* A written description of what was seen.

I lean to option 1. It costs tokens only on plans that change a screen.

**Who did the work.** T3 Code ends each pull request with the model and
harness that produced it, so that over time you can see which ones do well
in your repositories. It is one line. I have no strong lean; it is useful
to you once Codex and Claude Code both run arsenal.

### Q&A

**Q1.** For a plan that changes a screen, what evidence does the pull
request carry?
Options: Before/after screenshots (recommended) / Screenshots + video for
motion / Written description only
**Answer:** Before/after screenshots (Recommended)

**Q2.** Should every arsenal pull request end with a line naming the model
and harness that did the work?
Options: Yes, one line / No
**Answer:** No

### Decisions

- **D-10** For a plan that changes a screen, the pull request carries a
  screenshot from before the change and one from after, whenever the
  harness can take them. Where it cannot, the agent describes what it did
  and what it saw. No video is required.
- **D-11** Arsenal pull requests do not carry a line naming the model and
  harness that did the work.
- **A-11** Verification is run, read and written down, and is not
  committed. A requirement with a planned test is shown by that test
  passing; every other requirement by a command and its output, or by what
  was seen in the running app. Nothing written only to check the work stays
  in the repository — this is what separates checking a change from
  guarding against future breakage.
- **A-12** A script or temporary test written to check something lives
  outside the repository and is never committed — so scratch work cannot
  drift into the suite.
- **A-13** When a requirement states a target as a number (a time, a size,
  a count), its evidence is the measurement, with the before and after
  figures and how they were taken — a stated target can only be shown by
  measuring it.
- **A-14** Evidence is written in the ledger entry, one line per
  requirement as in 0.1.0, and repeated in the pull request description —
  the ledger merges with the code and the description is what gets read.

## Topic 5: The file-tools hook

### Explanation

Your CLAUDE.md says to use the Read, Write and Edit tools for anything
involving a file's contents, and agents still slip. A hook can refuse the
command before it runs and tell the agent which tool to use. Both Claude
Code and Codex let a hook deny a shell command with a reason.

**What a hook can and cannot do.** It sees the text of the shell command
and matches patterns in it. That catches the reflex (`sed -i`, a heredoc
into a file), which is the actual problem. It cannot catch everything: a
shell command can be written in endless ways. An agent that is refused
reads the reason and switches tools, and that is the effect wanted.

**Writing to files.** These would be blocked:

- in-place edits: `sed -i`, `perl -i`, `awk -i inplace`
- authored content sent to a file: a heredoc, `echo` or `printf` redirected
  with `>` or `>>`, and `tee`
- one-line scripts that write a file: `python -c`, `node -e`, `bun -e`,
  `ruby -e`, `perl -e` containing a file write

These stay allowed, because they are a command's output and not authored
content: `git diff main...HEAD > review.diff` (arsenal's own review step
does this), a build writing its output, and anything sent to `/dev/null`.

**Reading files.** Your rule also names `cat`. Two options:

1. *Block writes only.* Reading through the shell stays allowed. Few false
   refusals.
2. *Block plain reads too:* `cat`, `head`, `tail`, `less`, `sed -n` when
   pointed at a file. They stay allowed on the receiving end of a pipe
   (`git log | head`). Search (`grep`, `rg`, `find`) is never blocked. More
   of your rule is enforced, with more chance of refusing something
   harmless.

Codex complicates option 2: as far as I know it reads files through the
shell and edits through its own patch tool, so on Codex only the write half
can apply. I will confirm that in topic 7.

I lean to option 2 on Claude Code and writes only on Codex.

**Whose rule it is.** This is your preference, and arsenal is public. A
stranger who installs a build workflow may not expect it to refuse
`sed -i`. Three options:

1. *In arsenal, on by default, with a switch to turn it off.* There is a
   reason that holds for everyone: edits made with the edit tool are shown
   as diffs and go through the harness's permission checks, and shell edits
   bypass both.
2. *In arsenal, off by default,* turned on with a switch.
3. *Not in arsenal.* I set it up in your own Claude Code and Codex
   settings, and the plugin stays a pure workflow.

I lean to option 1.

### Q&A

**Comment from Saeed on the "Reading files" options:**

> reading is not a problem though... biggest problem is writing & updating
> only... reads are fine

**Q1.** Where should this hook live, given arsenal is public?
Options: In arsenal, on by default (recommended) / In arsenal, off by
default / Not in arsenal
**Answer:** "in arsenal, no switch to turn it off"

### Decisions

- **D-12** The file-tools hook blocks writing and editing files through
  the shell only. Reading files through the shell stays allowed.
- **D-13** The file-tools hook ships in arsenal and is always on. There is
  no switch to turn it off.
- **A-15** The hook refuses: in-place edits (`sed -i`, `perl -i`,
  `awk -i inplace`); authored content sent to a file (a heredoc, `echo` or
  `printf` redirected with `>` or `>>`, and `tee`); and one-line scripts
  that write a file (`python -c`, `node -e`, `bun -e`, `ruby -e`,
  `perl -e` containing a file write) — these are the forms named in
  Saeed's rule and the ones agents reach for by reflex.
- **A-16** The hook allows a command's own output redirected to a file
  (such as `git diff main...HEAD > review.diff`), a build writing its
  output, and anything sent to `/dev/null` — these are not authored
  content, and arsenal's own review step depends on the first.
- **A-17** A refused command gets a reason that names the tool to use
  instead: the harness's write and edit tools — the agent corrects itself
  from the reason.
- **A-18** The hook is a bash script that matches patterns in the command
  text, in line with 0.1.0/A-05 — it runs on any harness without a
  runtime. It is not expected to catch every way of writing a file.

## Topic 6: Finishing: merged to main, branches cleaned up

### Explanation

You said agents leave feature and release branches behind when the plans
are done, and that all work must end up reviewed and merged into `main`
with nothing stale left. 0.1.0 has two gaps: no skill deletes a branch
after merging it, and nothing checks at the end of a release that
everything really reached `main`.

**After every merge.** This part I would simply build. Whenever arsenal
merges a pull request, in `implement`, `orchestrate` or `quick`, it then
deletes that branch on the remote and locally, removes any worktree it
made, switches the checkout back to the default branch and pulls.

**How a branch is judged safe to delete.** Only a branch arsenal itself
cut, and only once its pull request is merged or it holds no commit that
`main` lacks. A branch with work that is not in `main` is never deleted. A
branch arsenal did not cut is never touched.

**Where plan pull requests go.** This is the real decision, and it decides
what "the end" is.

1. *Straight into `main`, as in 0.1.0.* Each plan is reviewed and merged
   into `main` by itself. There is no release branch, so nothing can be
   left on one. `main` holds a half-built release while the work is going
   on.
2. *Into one release branch, then one final pull request.* Each plan is
   reviewed and merged into `release/<release>`. When the last plan is in,
   arsenal opens one pull request from the release branch into `main`,
   reviews it, fixes what is confirmed, merges, and deletes the branch.
   `main` only ever receives a finished release. The cost is one more
   review, of the whole release at once, which is large and uses many
   tokens.

A parallel phase already works like option 2 on a small scale: its plans
merge into an integration branch, and one reviewed pull request takes the
phase into `main`.

I lean to option 2 if anything deploys or ships from your `main`, because
a half-built release there is a risk. If nothing does, option 1 is cheaper
and every plan is already reviewed.

**The check at the end of a release.** When the last plan in the index has
its ledger entry, arsenal lists every branch and worktree the release cut.
One whose work is in `main` is deleted. One whose work is not in `main` is
unfinished: it gets a pull request into `main`, a review, fixes and a
merge, and is then deleted. Anything that cannot be resolved is reported to
you with its name and what it holds.

**How the check is started.** Two options:

1. *By itself,* when the last plan merges. Nothing for you to remember.
2. *By itself, and also as a skill you can call,* `arsenal:finish`, to
   sweep a project where branches were already left behind, such as your
   current ones.

I lean to option 2.

### Q&A

**Q1.** Where do the pull requests for a release's plans go?
Options: Release branch, then one final PR / Straight into main / Release
branch, final PR unreviewed
**Answer:** Release branch, then one final PR

**Q2.** How is the end-of-release step (final pull request into main, then
the branch and worktree clean-up) started?
Options: Automatic + arsenal:finish (recommended) / Automatic only / Only
when I call it
**Answer:** "Autoamtic + arsenal:finish... final pull request into main,
then the branch and worktree clean-up, then create a tagged release
(tag+release) with the version which was being worked with proper release
notes"

**Q3.** A full release now ends with a git tag and a GitHub release with
notes. What about a small change made through arsenal:quick?
Options: No tag or release (recommended) / Ask me each time / Always tag a
patch release
**Answer:** Ask me each time

### Decisions

- **D-14** The pull request for each plan of a release goes into one
  release branch, `release/<release>`, after its review. When the last
  plan is merged there, one pull request takes the release branch into
  `main`; it is reviewed, the confirmed findings are fixed, and it is
  merged.
- **D-15** The end-of-release step starts by itself when the last plan of
  the release merges, and is also a skill the user can call at any time,
  `arsenal:finish`.
- **D-16** After the final pull request is merged and the branches and
  worktrees are cleaned up, arsenal creates a git tag and a GitHub release
  for the version that was being worked on, with proper release notes.
- **D-17** After a change made through `arsenal:quick` is merged, the agent
  asks the user whether to tag a patch version and publish a release for
  it.
- **A-19** Whenever arsenal merges a pull request, in `implement`,
  `orchestrate`, `quick` or `finish`, it then deletes that branch on the
  remote and locally, removes any worktree it made, switches the checkout
  to the branch it merged into and pulls — this closes the gap that left
  branches behind in 0.1.0.
- **A-20** Arsenal deletes only a branch it cut itself, and only once its
  pull request is merged or it holds no commit its target branch lacks. A
  branch holding work that is not merged is never deleted, and a branch
  arsenal did not cut is never touched — deleting unmerged work cannot be
  undone.
- **A-21** At the end of a release arsenal lists every branch and worktree
  the release cut. One whose work is in `main` is deleted. One whose work
  is not gets a pull request, a review, fixes and a merge, and is then
  deleted. Anything it cannot resolve is reported to the user with its name
  and what it holds — the user asked that nothing stale is left.
- **A-22** A parallel phase cuts its integration branch from the release
  branch and merges back into the release branch, not into `main` — it
  follows D-14.
- **A-23** A change made through `arsenal:quick` goes straight into `main`
  through its own reviewed pull request, with no release branch — it is one
  pull request and has no release folder.
- **A-24** The final pull request is reviewed as a code review against the
  release's spec, plans and ledger. For a large release several reviewers
  each own a range of plans, as the spec review does with transcript topics
  — one reviewer cannot hold a whole large release.
- **A-25** The tag follows the repository's existing tag style, or
  `v<release>` when it has none. The release notes are written from the
  spec and the ledger in plain language for the software's users, grouped
  as added, changed, removed and fixed — those two documents say what was
  decided and what was actually built.
- **A-26** Before the final pull request, arsenal sets the release's
  version wherever the project records one (a package manifest, a plugin
  manifest), on the release branch — the tag and the project's own version
  must agree.

## Topic 7: Codex support

### Research for this topic (Codex source and docs, 2026-11-20)

- A plugin's hook commands run with `PLUGIN_ROOT` set, and with
  `CLAUDE_PLUGIN_ROOT` as a compatibility alias. Arsenal's existing hook
  command works in Codex unchanged.
- When a plugin's manifest has no `hooks` field, Codex discovers
  `hooks/hooks.json` by itself. Installed as it is today, arsenal's
  `SessionStart` hook would already register in Codex, after a trust
  prompt at install.
- In a `PreToolUse` hook Codex reports shell commands under the tool name
  `Bash`, with the command text in `tool_input.command`, the same as Claude
  Code. Its file-edit tool is `apply_patch`, and `Write` and `Edit` are
  accepted as matcher aliases.
- Plugin skills are named `<plugin>:<skill>`, so `arsenal:brainstorm`, and
  a user invokes one as `$arsenal:brainstorm`.
- A Codex marketplace is described by `.agents/plugins/marketplace.json`
  in the repository.
- Superpowers removed its bootstrap hook on Codex, saying Codex triggers
  skills reliably by itself and the hook made the experience worse. Its
  bootstrap is long and forces skill use; arsenal's is ten lines and does
  not.
- Codex reads files through the shell and edits them through
  `apply_patch`.
- Subagents need the `multi_agent_v2` feature, which is off on this
  machine.
- I found no ask-question tool in the Codex documentation.
- None of this has been run yet. It is read from the source and the docs.

### Explanation

Codex turned out closer to Claude Code than expected: the same plugin
layout, the same hook events, the same tool name for shell commands. Most
of arsenal should work with two small manifest files added. What needs
deciding is how far "full" goes.

**Packaging.** Arsenal gains `.codex-plugin/plugin.json` and
`.agents/plugins/marketplace.json`, so a Codex user adds the repository as
a marketplace and installs the plugin, as a Claude Code user does. This I
would simply build.

**The file-tools hook.** One script serves both harnesses, because both
report shell commands the same way.

**Tool differences.** The skills are already written without tool names
and with a fallback where a tool may be missing. For Codex I would add one
short reference file that maps the three things that differ: questions are
asked in plain text with numbered options; a subagent is started with a
clean context and needs the `multi_agent_v2` feature; edits go through
`apply_patch`. The skills themselves stay harness-neutral.

**Subagents.** `review` and `orchestrate` rely on them. On a Codex without
the feature switched on, the existing fallbacks apply: the review is done
as a separate pass and reported as not independent, and a parallel phase
runs its plans in order. The README says how to switch the feature on.

**The bootstrap.** Two options:

1. *The same ten-line `SessionStart` hook as on Claude Code.* One behavior
   everywhere, and the tracks are announced in every session. A Codex user
   sees a trust prompt for the hook at install.
2. *No bootstrap on Codex,* relying on Codex finding skills by their
   descriptions, as superpowers now does. Nothing to trust at install, but
   the file-tools hook needs that trust prompt anyway, since D-13 makes it
   always on.

I lean to option 1.

**How far "full" goes.** Codex exists as the CLI and as the Codex app and
cloud. The app runs work in a sandbox that can block branching and pushing,
and arsenal's whole flow is branch, pull request, merge. Two options:

1. *The Codex CLI in 0.2.0.* Everything arsenal does on Claude Code works
   in the CLI. The app and cloud are not promised.
2. *The CLI and the app.* The skills also need a path for a sandbox that
   cannot push, where the agent commits and hands the branch and pull
   request text to you. More to build and to test, and I cannot test the
   app from here.

I lean to option 1.

**Proving it works.** Reading the source is not proof. To test, arsenal
has to be installed into a Codex and run. The Codex on this machine is the
one you use every day, so I will not install into it without your say.

### Q&A

**Q1.** How far does "full Codex support" go in 0.2.0?
Options: Codex CLI only (recommended) / CLI and the Codex app
**Answer:** Codex CLI only (Recommended)

**Q2.** Does arsenal announce itself at the start of a Codex session, as it
does on Claude Code?
Options: Same hook as Claude Code (recommended) / No bootstrap on Codex
**Answer:** Same hook as Claude Code (Recommended)

**Q3.** Codex support has to be proven by running arsenal in a Codex. The
Codex on this machine is your everyday one. How should it be tested?
Options: Install into my Codex (recommended) / Separate Codex home / I test
it myself
**Answer:** "temporary install to me codex remove after testing"

### Decisions

- **D-18** Codex support in 0.2.0 covers the Codex CLI only. The Codex app
  and cloud are not promised.
- **D-19** On Codex, arsenal injects the same `SessionStart` bootstrap text
  as on Claude Code.
- **D-20** Codex support is tested by installing arsenal temporarily into
  the Codex on Saeed's machine and running it there. The plugin and its
  marketplace entry are removed from that Codex after testing.
- **A-27** Arsenal gains `.codex-plugin/plugin.json` and
  `.agents/plugins/marketplace.json`, so a Codex user adds the repository
  as a marketplace and installs the plugin — these are the files Codex
  reads for a plugin and a marketplace.
- **A-28** One hook script per hook serves both harnesses — both report a
  shell command under the tool name `Bash` with the text in
  `tool_input.command`, and both set `CLAUDE_PLUGIN_ROOT`.
- **A-29** The skills stay free of harness tool names. One Codex reference
  file maps what differs: questions are asked in plain text with numbered
  options; a subagent is started with a clean context and needs the
  `multi_agent_v2` feature; file edits go through `apply_patch` — this
  follows 0.1.0/D-02, which adds a per-harness reference file once a
  harness is actually used. — superseded by A-53
- **A-30** On a Codex where subagents are not switched on, the fallbacks
  already in the skills apply: a review is done as a separate pass and
  reported as not independent, and a parallel phase runs its plans in
  order. The README says how to switch the feature on — arsenal does not
  change a user's Codex configuration.
- **A-31** The Codex test checks, in non-interactive sessions, that the
  skills are listed under `arsenal:<name>`, that the bootstrap text reaches
  the session, and that the file-tools hook refuses a shell edit and allows
  a shell read — these are the parts that differ from reading the source.

## Topic 8: Release housekeeping

### Explanation

Most of this needs no decision from you. I list it so that it is recorded.

**What I would simply do.**

- *Version.* The plugin manifests say `0.2.0`.
- *README.* It is updated for the release branch and `arsenal:finish`, for
  tests named in plans, for the file-tools hook, and for installing on
  Codex.
- *Arsenal's own tests.* Arsenal has no tests today. The file-tools hook
  and the coverage script's new check are exactly the kind of logic D-06
  says gets tests, so the plans name them. They run as a plain bash script,
  with no new dependency.
- *Arsenal follows its own new rules while being built.* The sessions that
  build 0.2.0 run the 0.1.0 skills, which know nothing of release branches
  or tags. I would apply the new rules by hand: plans merge into
  `release/0.2.0`, one reviewed pull request goes into `main`, then the
  tag `v0.2.0` and a GitHub release with notes.
- *This folder is committed.* `docs/arsenal/0.2.0/` goes into the
  repository as the record of this release, and as a worked example for
  people reading the README.

**Projects with a release already in progress.** This one is yours. A
project whose plans were written under 0.1.0 has no `Tests` blocks and
merges plans straight into `main`. Once you update the plugin, those plans
meet the 0.2.0 skills. Two options:

1. *They carry on as they were written.* A plan without a `Tests` block is
   implemented under the new test rule anyway (D-06 and D-08): the
   implementer decides which tests the task needs by that rule and lists
   them on the ledger's `Tests` line. Plans keep merging into `main`. The
   coverage script does not fail a release folder in which no plan has a
   `Tests` block. `arsenal:finish` can still be called at the end to sweep
   branches and tag.
2. *They are brought up to 0.2.0 first.* The plans are revised to add
   `Tests` blocks and reviewed again, and the remaining plans move to a
   release branch. Everything follows one set of rules, at the cost of
   reworking and re-reviewing plans you already approved.

I lean to option 1.

### Q&A

**Q1.** A project has a release in progress whose plans were written under
arsenal 0.1.0. What happens to it once the plugin is 0.2.0?
Options: Carry on as written (recommended) / Bring it up to 0.2.0 first
**Answer:** Carry on as written (Recommended)

**Comment from Saeed afterwards:**

> this doesn't matter much because i will only update arsenal on my system
> once i'm done implementing the one thing i'm implementing right now

The answer stands as given; it also covers other users who update in the
middle of a release.

### Decisions

- **D-21** A release whose plans were written under arsenal 0.1.0 carries
  on as written. A plan without a `Tests` block is implemented under D-06
  and D-08: the implementer decides which tests the task needs by that rule
  and lists them on the ledger's `Tests` line. Its plans keep merging into
  `main`. `arsenal:finish` can be called at its end to sweep branches and
  tag.
- **A-32** The coverage script's `Tests` check (A-03) applies only to a
  release folder in which at least one plan has a `Tests` block — so a
  0.1.0 release in progress is not failed by it (D-21).
- **A-33** The plugin manifests for Claude Code and Codex both say version
  `0.2.0`.
- **A-34** The README is updated for the release branch and
  `arsenal:finish`, tests named in plans, the file-tools hook, and
  installing on Codex, and the bootstrap text names `arsenal:finish` — both
  are what a new user reads first.
- **A-35** The file-tools hook and the coverage script get tests, named in
  the plans under D-06, run by a plain bash script with no new dependency —
  both are logic where a wrong edit silently gives a wrong answer, and
  0.1.0/A-05 keeps arsenal free of a runtime.
- **A-36** Arsenal 0.2.0 is built by its own new rules, applied by hand
  because the building sessions run the 0.1.0 skills: plans merge into
  `release/0.2.0`, one reviewed pull request goes into `main`, then the tag
  `v0.2.0` and a GitHub release with notes. This replaces 0.1.0/D-08 for
  this release.
- **A-37** `docs/arsenal/0.2.0/` is committed to the arsenal repository —
  it is the record of the release and a worked example for readers.

## Sweep

Reading the decisions as a set raised one question for Saeed and four
points settled as agent calls.

**Q1.** You chose before/after screenshots in the pull request (D-10), but
GitHub has no way for a command-line agent to attach an image to a pull
request. Where do the image files live?
Options: A separate evidence branch (recommended) / Committed in the
release folder / Not in the pull request
**Answer:** A separate evidence branch (Recommended)

### Decisions

- **D-22** Screenshots are pushed to one branch, `arsenal/evidence`, that
  never merges into `main`, and the pull request links to them there.
- **A-38** The clean-up steps (A-19, A-20, A-21) never delete
  `arsenal/evidence` — it is arsenal's own branch but holds the record
  D-10 asks for.
- **A-39** Arsenal knows a branch as its own by name:
  `release/<release>`, `plan/<release>-NN-<name>`,
  `feat/<release>-phase-NN`, and the branch a quick change was cut on in
  the same session — A-20 needs a rule that can be applied without
  guessing. — superseded by A-54
- **A-40** When a release has a single plan, so that the final pull
  request's diff is the same as that plan's already reviewed pull request,
  the final pull request is not reviewed a second time — a second review of
  an identical diff costs tokens and can find nothing new. This narrows
  D-14 and is listed for Saeed to overrule.
- **A-41** Where the repository has no GitHub remote, `arsenal:finish`
  creates the tag and writes the release notes into the conversation,
  without a GitHub release — D-16 cannot be carried out there in full.
- **A-42** Scratch files and the saved plan of a quick change are written
  with the harness's write tool, since the file-tools hook (D-13) refuses a
  heredoc — the skills must not tell the agent to do what the hook blocks.

## Calls made while writing the spec

The build needed these and the brainstorm left them open.

- **A-43** The file-tools hook is the script `hooks/file-tools`, registered
  in `hooks/hooks.json` under `PreToolUse` with the matcher `Bash`. It
  refuses a command by printing JSON whose
  `hookSpecificOutput.permissionDecision` is `"deny"`. It allows any
  command it does not recognise and any input it cannot read — a hook that
  fails closed would stop all shell work on a harness whose input differs.
- **A-44** Three points narrow A-15. A redirect to `/dev/null` or to a file
  descriptor (`>&2`, `2>&1`) is not a file write. `tee` is refused only
  when it is fed by a heredoc, `echo` or `printf`; `tee` capturing another
  command's output is allowed. An interpreter fed its script through a
  heredoc (`python3 - <<'EOF'`) is treated like its `-c` or `-e` form —
  without these the hook would refuse `echo "failed" >&2` and
  `build | tee log.txt`, and would miss the commonest scripted write.
- **A-45** The reason given with a refusal is exactly: "arsenal: this
  command writes or edits a file through the shell. Use your file write or
  edit tool instead." — it names no harness, so one text serves both.
- **A-46** `arsenal:plan-review` cuts `release/<release>` from the latest
  default branch as its last step, commits the release folder to it and
  pushes. `arsenal:implement` cuts it the same way if it does not exist —
  D-14 needs the branch before the first plan, and the release folder must
  be on it for every implementing session to read.
- **A-47** `arsenal/evidence` is a branch with no shared history with
  `main`. Screenshots are stored as `<release>/plan-NN/<name>-before.png`
  and `<release>/plan-NN/<name>-after.png`, or under `quick/<branch>/` for
  a quick change, committed and pushed from a temporary worktree so the
  working checkout is not switched, and linked in the pull request as
  `https://github.com/<owner>/<repo>/blob/arsenal/evidence/<path>?raw=true`
  — that link form shows the image inside a pull request, in private
  repositories too.
- **A-48** In `arsenal:quick` the question of D-17 is asked once the review
  fixes are pushed and before the merge, so that on a yes the version
  change goes into the same pull request. The patch version is the next
  one after the latest tag, and the tag and release are created after the
  merge. This moves D-17's question from just after the merge to just
  before it and is listed for Saeed to overrule — asked after the merge, a
  version change would need a second pull request.
- **A-49** The Codex reference file is `references/codex.md` at the plugin
  root. The bootstrap text ends with one line giving its absolute path and
  telling a Codex session to read it before using an arsenal skill, on both
  harnesses — telling the harnesses apart inside the hook is one more thing
  that could silently fail.
- **A-50** Arsenal's tests are one bash script, `tests/run`, that exits
  non-zero when a case fails — A-35 needs a place and a way to run them.
- **A-51** After a merge, `arsenal:implement` and `arsenal:orchestrate`
  follow `arsenal:finish` in the same session when every plan in the index
  has a ledger entry whose status is merged. `arsenal:finish` then sets the
  version, rebases the release branch on the latest default branch, runs
  the project's checks, opens the final pull request, has it reviewed,
  fixes, merges, cleans up, and tags. Called by hand on a release whose
  plans are not all merged, it only cleans up and reports. Called on a
  release with no release branch (D-21), it skips the final pull request —
  D-15 needs a rule for when the automatic start fires and what a manual
  call does.

## Calls made after the first spec review

- **A-52** A release counts as written under arsenal 0.1.0 when no plan
  file in its folder has a `**Tests:**` block. For such a release
  `arsenal:implement` and `arsenal:orchestrate` cut no release branch, use
  the default branch as the base and the target of pull requests, and do
  not start `arsenal:finish` by themselves — without a rule to tell the two
  kinds of release apart, an implementing session would cut a release
  branch in the middle of a 0.1.0 release and split it, against D-21. It is
  the same test A-32 uses.
- **A-53** The skills name no Codex tool and gain no new mention of any
  harness's tools; the one mention of Claude Code's workflow tool already
  in `skills/plan-review/SKILL.md` stays. One Codex reference file maps
  what differs: questions are asked in plain text with numbered options; a
  subagent is started with a clean context and needs the `multi_agent_v2`
  feature; file edits go through `apply_patch` — A-29 said the skills are
  free of harness tool names, which is not true of 0.1.0 and would have
  removed guidance 0.1.0/D-02 allows.

## Spec approval

Saeed approved the spec on 2026-11-20, with one change:

> oky i approve the spec just one thing... idk if even needed this workflow
> for arsenal seems like an overkill but i believe 1 single plan will be
> enough for this whole implementation

- **D-23** Arsenal 0.2.0 is implemented as one single plan.

## Calls made after the second spec review

- **A-54** Arsenal knows a branch as its own in three ways: by name
  (`release/<release>`, `plan/<release>-NN-<name>`,
  `feat/<release>-phase-NN`); as the head branch of a pull request whose
  base is `release/<release>` or a phase's integration branch; or as a
  branch it cut in the same session — `arsenal:implement` follows a
  repository's own branch naming where it has one, so names alone would
  leave those plan branches undeleted, which is the gap the brief asked to
  close.

