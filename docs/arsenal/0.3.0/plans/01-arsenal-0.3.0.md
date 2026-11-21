# Plan 01: arsenal-0.3.0

**Release:** 0.3.0
**Phase:** 1
**Depends on:** none
**Goal:** Arsenal is a native Antigravity CLI (`agy`) plugin and both of its
hooks work there.

This release had no brainstorm or spec. The plan was agreed in the
conversation and is recorded here.

## Why

Measured against agy 1.5.13 before this release:

- `agy plugin install` converted the Claude plugin and installed all 11
  skills, so the skills worked.
- `agy plugin validate` failed: there was no Antigravity manifest.
- The file-tools hook was installed but allowed everything. It reads
  `tool_input.command`, and agy sends `toolCall.args.CommandLine`, so a
  `sed -i` went through.
- The session-start hook printed Claude's `hookSpecificOutput`, and agy
  reads only `injectSteps`.
- The skills say `arsenal:<name>`, and agy names plugin skills `<name>`.

## Shared names

- **Provides:** `.antigravity-plugin/plugin.json`
- **Provides:** `hooks.json` at the repository root, read by agy only
- **Provides:** `references/antigravity.md`
- **Provides:** the `antigravity` argument to `hooks/session-start` and
  `hooks/file-tools`

## Tasks

### Task 1: Manifest and hooks file

`.antigravity-plugin/plugin.json` carries the fields of
`.claude-plugin/plugin.json` plus `displayName`, at version `0.3.0`.

`hooks.json` at the root holds one group, `arsenal`, with a `SessionStart`
handler `bash ./hooks/session-start antigravity` and a `PreToolUse` group
matching `run_command` with handler `bash ./hooks/file-tools antigravity`,
timeout 10. agy runs plugin hooks with the plugin root as cwd.

**Tests:** none — configuration, shown by `agy plugin validate` and a live
run

### Task 2: The hooks speak agy's format

`hooks/file-tools antigravity` reads the command from `"CommandLine"`. It
always prints a decision: `{"decision":"deny","reason":"arsenal: …"}` or
`{"decision":"allow"}`, because agy blocks a call when a pre-tool hook
fails. Without the argument it behaves as before.

`hooks/session-start antigravity` prints
`{"injectSteps":[{"systemMessage":{"systemMessage":"…"}}]}`. Its last line
points to `references/antigravity.md` instead of the Codex reference.

**Tests:**
- every existing file-tools case in `tests/run` also passes in agy's input
  and output format
- in agy's format the command is read from `toolCall.args.CommandLine`, not
  from command-like text in `lastUserInput`

### Task 3: Reference file, README, versions

`references/antigravity.md` maps skill names, `ask_question`,
`invoke_subagent`, and the file write and edit tools, and notes that
`--dangerously-skip-permissions` turns hooks off. The README gets an
Antigravity install section and the validate command. All three manifests
go to `0.3.0`.

**Tests:** none — documentation

## Verification

- `bash tests/run` passes.
- `agy plugin validate .` and `claude plugin validate .` pass.
- In a scratch project with the plugin in `.agents/plugins/arsenal/`,
  `agy -p` quotes the session-start text and refuses `sed -i` with
  arsenal's reason.
