# Arsenal 0.3.0 — Ledger

## Plan 01: arsenal-0.3.0
- **Status:** merged
- **Tasks:**
  - Task 1 — `agy plugin validate .` (agy 1.5.13) reports "Validating
    arsenal (.antigravity-plugin/plugin.json)", "skills 11 · hooks 1",
    "Validation passed with 1 warning". The warning is that
    `hooks/hooks.json` is never loaded, which is intended: that file is for
    Claude Code and Codex. `claude plugin validate .` passes.
  - Task 2 — `bash tests/run`: "All 103 cases passed." The file-tools table
    runs in both formats, plus the two `lastUserInput` cases. Live, in a
    scratch project with the plugin in `.agents/plugins/arsenal/`:
    `agy -p` asked what arsenal says for a bug fix and quoted "Patch, small
    change or bug fix: arsenal:quick." from the injected session-start
    text. Asked to run `sed -i 's/a/b/' f.txt`, it got "tool call denied by
    pre-tool hook: arsenal: this command writes or edits a file through the
    shell. Use your file write or edit tool instead." The file was
    unchanged, and `cat f.txt` ran.
  - Task 3 — `references/antigravity.md`, the README's "Antigravity CLI"
    section and "Working on arsenal itself", and version `0.3.0` in
    `.claude-plugin/plugin.json`, `.codex-plugin/plugin.json` and
    `.antigravity-plugin/plugin.json`.
- **Tests:** planned: every existing file-tools case also run in agy's
  format; the command is read from `toolCall.args.CommandLine`, not from
  `lastUserInput` (two cases). Changed: the R-056 unreadable-input and
  empty-input checks. They now go through the same `decision` helper as
  every other case, so they also run in agy's format. In Claude format
  they still require empty output.
- **Deviations:**
  - The reference file does not say that a resumed conversation skips
    session-start. The text injected on the first turn stays in the
    conversation, so a resumed session is not left without it.
  - The live run listed the plugin's skills as `plan`, `review` and so on,
    without the `arsenal:` prefix. This confirms the mapping in the
    reference file.
- **For later plans:** none
