# Arsenal on the Antigravity CLI

The arsenal skills are written without naming a harness's tools. A few
things work differently on the Antigravity CLI (`agy`). Read this once
before using a skill.

## Invoking a skill

Antigravity names a plugin's skills without the plugin's name. Where a
skill says `arsenal:review`, load the skill named `review`; the same goes
for every other `arsenal:<name>`. The user invokes one as `/<name>`, such
as `/brainstorm`.

If another installed plugin has a skill with the same name, use the one
whose description matches the arsenal skill you were sent to.

## Asking the user

Where a skill says to use the ask-question tool, use `ask_question`: one
question per call, the recommended option first. Wait for the answer
before the next question.

## Subagents

Where a skill says to dispatch a fresh subagent or a reviewer, use
`invoke_subagent`. Give the child only what the skill says its prompt
contains, so it sees none of your session.

If you cannot start a subagent, use the fallback each skill gives:

- arsenal:review: do the review yourself as a separate pass, and tell the
  user it was not independent.
- arsenal:orchestrate: run the phase's plans in order with
  arsenal:implement.

## Editing files

Create and change files with `write_to_file`, `replace_file_content` and
`multi_replace_file_content`. A hook refuses shell commands that write or
edit a file, such as an in-place `sed`, a heredoc or `echo` redirected into
a file, or a one-line script that writes one. Reading files through the
shell is fine.

Where a skill says "your file write tool" or "your file edit tool", that is
one of these.

## When the hooks do not run

Antigravity runs no hooks under `--dangerously-skip-permissions`. In such
a session nothing refuses shell edits, so follow the rule above on your
own.
