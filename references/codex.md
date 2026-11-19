# Arsenal on Codex

The arsenal skills are written without naming a harness's tools. Three
things work differently on Codex. Read this once before using a skill.

## Asking the user

Where a skill says to use the ask-question tool, ask in plain text instead:
one question per message, numbered options, the recommended option first.
Wait for the reply before the next question.

## Subagents

Where a skill says to dispatch a fresh subagent or a reviewer, spawn an
agent with a clean context, so it sees none of your session. If your spawn
tool copies your transcript into the child by default, turn that off. Give
the child only what the skill says its prompt contains.

Subagents need the `multi_agent_v2` feature. If your tool list has no way
to spawn an agent, the feature is off. Do not change the user's
configuration. Use the fallback each skill gives:

- arsenal:review: do the review yourself as a separate pass, and tell the
  user it was not independent.
- arsenal:orchestrate: run the phase's plans in order with
  arsenal:implement.

Tell the user they can switch it on by adding this to
`~/.codex/config.toml`:

```toml
[features]
multi_agent_v2 = true
```

## Editing files

Create and change files with `apply_patch`. A hook refuses shell commands
that write or edit a file, such as an in-place `sed`, a heredoc or `echo`
redirected into a file, or a one-line script that writes one. Reading files
through the shell is fine.

Where a skill says "your file write tool" or "your file edit tool", that is
`apply_patch`.

## Invoking a skill

The skills are named `arsenal:<name>`. Where one skill says to follow or
continue with another, such as arsenal:review, load that skill by that
name.
