# Arsenal

A set of skills that gives your coding agent a calm, repeatable way to build
software: talk it through, write it down, split it up, build it one reviewed
pull request at a time, and keep a record of what actually got built.

Arsenal is a plugin for [Claude Code](https://claude.com/claude-code) and
the [Codex CLI](https://developers.openai.com/codex). The skills are plain
`SKILL.md` files, so the ideas travel to other harnesses too.

## Why it exists

Agents are fast, and fast is how small details get lost. A limit you agreed
on in conversation never reaches the spec. A field name in the spec is
paraphrased in a plan. Plan 12 doesn't know what plan 7 quietly changed.

Arsenal's answer is to give every decision a number and follow it all the
way through:

```
You decide something        D-07   "A trial lasts 14 days"
The spec requires it        R-012  ... (D-07)
A plan task carries it      Task 2 (R-012), with the value copied in
The ledger proves it        R-012 — test "trial ends on day 14" passes
```

A small script checks that nothing fell out of that chain, so it doesn't
depend on anyone noticing.

## How a build goes

1. **Brainstorm.** You give a brief. The agent researches, proposes an
   agenda of topics, then takes them one at a time: it explains a topic,
   waits until you've read it, and asks one question at a time. Every
   answer is saved to a transcript.
2. **Spec.** The agent writes the spec from the transcript. Fresh reviewer
   agents compare the two, twice, before you read and approve it.
3. **Plan.** The spec is split into small plans, one pull request each.
   Plans carry exact values, shared names and the tests each task gets, but
   no code.
4. **Plan review.** Reviewers check the plans against the spec, looking
   only for things that would really go wrong during implementation. Then
   a release branch is cut for the work.
5. **Implement.** In a fresh session, say `Implement Plan 03`. The agent
   reads the ledger, the plan and the spec, builds on its own branch,
   verifies each requirement, opens a pull request into the release
   branch, has it reviewed by a fresh agent, fixes what's confirmed, merges
   and deletes its branch.
6. **Finish.** When the last plan has merged, one reviewed pull request
   takes the release branch into `main`. The branches and worktrees the
   release cut are removed, and the release is tagged and published with
   release notes.

For a bug fix or a small change, there's a short path: `quick` agrees a
short plan with you in the conversation and goes straight to a reviewed
pull request into `main`.

## Tests come from the plan

Left alone, an agent writes a test to prove each thing it just built, and
rewrites old tests until they pass. The pile grows with every release and
protects very little.

In arsenal the plan names the tests, before any code exists:

```markdown
**Tests:**
- R-012: a trial started on any date ends exactly 14 days later
```

- Tests are named only for logic where a wrong edit would silently give a
  wrong answer: calculations, state changes, permission checks, parsing,
  and boundaries a requirement states. Everything else is shown by running
  it, and the output goes in the pull request and the ledger, not into the
  test suite.
- The implementer writes the tests the plan names. It may add one the plan
  missed, or correct one that is itself wrong, only with a reason written
  in the ledger.
- A fresh reviewer flags any test the plan and ledger don't account for,
  and any existing test that was loosened, skipped or deleted.

The plans index shows the number of tests per plan, so you can see the
total for a release, and cut it, before anything is built.

## The skills

| Skill | What it does |
|-------|--------------|
| `arsenal:brainstorm` | Topic-by-topic discussion with a saved transcript and numbered decisions |
| `arsenal:spec` | Writes the spec from the transcript and has it checked against it |
| `arsenal:plan` | Splits the spec into small plans, grouped into phases |
| `arsenal:plan-review` | Checks the plans against the spec before any code is written |
| `arsenal:implement` | Builds one plan as one reviewed, merged pull request |
| `arsenal:orchestrate` | Runs a phase's plans in parallel with subagents in worktrees |
| `arsenal:finish` | Takes a finished release into `main`, cleans up its branches, tags and publishes it |
| `arsenal:review` | The single review bar every review uses |
| `arsenal:quick` | The short path for patches and bug fixes |
| `arsenal:debugging` | Find the cause before changing anything |
| `arsenal:testing` | The tests the plan names, and what may happen to existing ones |

## The hooks

Arsenal installs two small hooks:

- **Session start.** Tells the agent these skills exist and which path
  fits which kind of work. Your own instructions always win over them.
- **File tools.** Refuses a shell command that writes or edits a file: an
  in-place `sed`, a heredoc or `echo` redirected into a file, a one-line
  script that writes one. The agent is told to use its write and edit
  tools instead, so every change shows up as a diff and goes through the
  harness's own permission checks. Reading files through the shell, and
  redirecting a command's output to a file, stay allowed. This hook is
  always on.

The file-tools hook matches patterns in the command text. It catches the
reflex, not every possible way to write a file.

## The review bar

Ask a reviewer to find problems and it always will. Fix everything it finds
and you end up with more code, more tests and more scope than you asked
for. So every arsenal review reports only:

- real bugs
- places the work contradicts or leaves out something that was decided
- security flaws
- tests the plan and ledger don't account for, and existing tests that were
  weakened
- for plans: things realistically likely to go wrong during implementation

Each finding has to name a concrete failure: this situation, this goes
wrong. No style notes, no "consider adding", no hardening for cases that
won't happen.

## What ends up in your project

One folder per release, kept as a record once that release ships:

```
docs/arsenal/
  0.1.0/
    transcript.md   the brainstorm, with every decision numbered
    spec.md         the requirements, each citing its decisions
    plans/          index.md plus one file per plan
    ledger.md       what each plan actually built, with evidence
  0.2.0/
    ...             describes only what changes from 0.1.0
```

There is no "current state of the app" document. The code is the truth
about the code; these folders are the record of why.

Arsenal's own release folders are in this repository under
[docs/arsenal](docs/arsenal), if you want to see a real one.

While a release is being built its work lives on `release/<release>`.
Screenshots for pull requests that change a screen are kept on a separate
branch, `arsenal/evidence`, which never merges into `main`.

## Install

### Claude Code

```
/plugin marketplace add msaeedsakib/arsenal
/plugin install arsenal@arsenal
```

To update later:

```
/plugin marketplace update arsenal
```

and then update the plugin from the `/plugin` menu.

### Codex CLI

```
codex plugin marketplace add msaeedsakib/arsenal
codex plugin add arsenal@arsenal
```

Then start `codex` once and approve arsenal's hooks when it asks. Codex
does not run a plugin's hooks until you have trusted them, so until then
the skills work but the session-start text and the file-tools hook do not.

The skills are named `arsenal:<name>`; mention one as
`$arsenal:brainstorm`.

Reviews and parallel phases use subagents. In Codex these need a feature
that may be off. To switch it on, add this to `~/.codex/config.toml`:

```toml
[features]
multi_agent_v2 = true
```

Without it arsenal still works: reviews are done as a separate pass and
reported as not independent, and a parallel phase runs its plans in order.

Arsenal supports the Codex CLI. The Codex app and Codex cloud are not
covered yet.

Arsenal is meant to be the one workflow plugin in a session. If you have
another that also steers how work is planned and built, disable it first so
the two don't pull in different directions.

## Try it

Start a session in a project and say:

> Let's brainstorm. I want to build ...

or, for something small:

> Fix this bug: ...

## Working on arsenal itself

Load the plugin straight from a checkout, without installing it:

```
claude --plugin-dir /path/to/arsenal
```

Check the manifests and skills with:

```
claude plugin validate /path/to/arsenal
```

Run the tests for the file-tools hook and the coverage script with:

```
bash tests/run
```

Codex installs a plugin from the repository's latest commit, not from the
working tree, so commit before trying a change there.

Arsenal is young. If a skill gets in your way or an agent slips past one,
an issue describing what happened is very welcome.

## License

See [LICENSE](LICENSE).
