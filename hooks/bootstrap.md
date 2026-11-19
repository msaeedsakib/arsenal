Arsenal skills are installed. The user's own instructions always win over them. Trivial requests and plain questions need no skill.

Pick the track that fits the work:
- New build or major release: arsenal:brainstorm, then arsenal:spec, arsenal:plan, arsenal:plan-review.
- "Implement Plan NN" or "Implement Phase NN": arsenal:implement (a parallel phase uses arsenal:orchestrate).
- A release whose plans are all merged, or branches left behind: arsenal:finish.
- Patch, small change or bug fix: arsenal:quick.

Supporting skills: arsenal:review (every spec, plan and PR review uses its bar), arsenal:debugging (any bug or failing test), arsenal:testing (whenever tests are written).

Release documents live in docs/arsenal/<release>/: transcript.md, spec.md, plans/, ledger.md.

A hook refuses shell commands that write or edit a file. Use your file write and edit tools for that.
