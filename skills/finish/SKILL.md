---
name: finish
description: Use when every plan of a release has merged and the release must reach the default branch, when the user says "finish release" or asks to tag or publish a release, and when branches or worktrees from earlier arsenal work were left behind and need cleaning up.
---

# Finish

Take a finished release into the default branch through one reviewed pull
request, remove the branches and worktrees it cut, and publish it as a
tagged release. Nothing stale is left behind.

arsenal:implement and arsenal:orchestrate start this by themselves when the
last plan of a release merges. The user can also call it at any time.

## Which case this is

Read `docs/arsenal/<release>/plans/index.md` and `ledger.md`.

- **Every plan has a ledger entry whose status is merged, and
  `release/<release>` exists:** a complete release. Do all the steps.
- **Some plans are not merged:** do only "Clean up" below, and report what
  is still open. Open no final pull request and create no tag.
- **A release written under arsenal 0.1.0** (no plan file in its folder has
  a `**Tests:**` block, so its plans merged straight into the default
  branch and it has no release branch): skip steps 1 to 3. Clean up, then
  tag.
- **No release at all,** only branches left behind: do only "Clean up".

## Steps for a complete release

### 1. Set the version

On the release branch, set the release's version wherever the project
records one: a package manifest, a plugin manifest, a version file. The tag
and the project's own version must agree. Commit it.

### 2. Open the final pull request

Rebase `release/<release>` on the latest default branch, run the project's
tests, type check and lint, and read the output. Push, and open one real
pull request, not a draft, from `release/<release>` into the default
branch. Its description says what the release is for, lists the plans with
their pull requests, and lists every deviation from the ledger.

### 3. Review, fix and merge

Follow arsenal:review for a code review of the pull request against the
release's spec, plans and ledger. For a large release, dispatch several
reviewers in parallel, each owning a range of plans. Fix the confirmed
findings, run the checks again, and post the review record.

When the release has a single plan, the final pull request's diff is the
same as that plan's pull request, which was already reviewed. Do not review
it a second time; say so in the review record.

Merge once the checks pass, unless the user's instructions say they merge
themselves.

### 4. Clean up

Do "Clean up" below for everything the release cut.

### 5. Tag and publish

Create a git tag on the merged commit and push it. Follow the repository's
existing tag style; when it has no tags, use `v<release>`.

Write release notes from the spec and the ledger, in plain language for the
people who use the software, grouped as Added, Changed, Removed and Fixed.
Leave out empty groups, requirement IDs and anything only a contributor
would care about. Publish a GitHub release for the tag with those notes.

Where the repository has no GitHub remote, create the tag and write the
release notes into the conversation instead.

Then report in a few lines: the pull request, the tag, the release, and
anything you could not resolve.

## Clean up

Whenever arsenal merges a pull request, in this skill or in
arsenal:implement, arsenal:orchestrate or arsenal:quick, it then:

1. deletes the merged branch on the remote and locally
2. removes any worktree it made for that branch
3. switches the checkout to the branch it merged into, and pulls

At the end of a release, also list every branch and worktree the release
cut, and deal with each:

- **Its work is in the default branch:** delete it.
- **Its work is not in the default branch:** it is unfinished. Open a pull
  request for it, have it reviewed as arsenal:review says, fix what is
  confirmed, merge, then delete it.
- **You cannot resolve it:** leave it, and report its name and what it
  holds.

### Which branches are arsenal's

A branch is arsenal's own when one of these is true:

- its name is `release/<release>`, `plan/<release>-NN-<name>` or
  `feat/<release>-phase-NN`
- it is the head branch of a pull request whose base is `release/<release>`
  or a phase's integration branch
- you cut it yourself in this session

### What may be deleted

- Delete a branch only when its pull request is merged, or it holds no
  commit its target branch lacks.
- Never delete a branch holding work that is not merged.
- Never touch a branch arsenal did not cut.
- Never delete `arsenal/evidence`.

## The evidence branch

Screenshots for pull requests live on one branch, `arsenal/evidence`, which
has no shared history with the default branch and never merges into it.

- **Paths:** `<release>/plan-NN/<name>-before.png` and
  `<release>/plan-NN/<name>-after.png`. For a quick change,
  `quick/<branch>/<name>-before.png` and `-after.png`.
- **Adding to it:** check the branch out in a temporary worktree, creating
  it as an orphan branch when it does not exist yet, commit the images
  there, push, and remove the worktree. The working checkout is never
  switched.
- **Linking:** in the pull request, link each image as
  `https://github.com/<owner>/<repo>/blob/arsenal/evidence/<path>?raw=true`.

## Common slips

| Slip | Instead |
|------|---------|
| Tagging before the final pull request has merged | The tag goes on the merged commit. |
| Deleting a branch because its name looks old | Only when its pull request is merged or it holds nothing its target lacks. |
| Force-deleting a branch with unmerged work to get a clean list | It is unfinished work. Open a pull request for it or report it. |
| Release notes copied from the ledger | Rewrite them for users: what they can now do, what changed for them. |
| Reviewing a one-plan release twice | The diff is identical. Say so and merge. |
| Leaving the checkout on a deleted branch | Switch to the branch you merged into, and pull. |
