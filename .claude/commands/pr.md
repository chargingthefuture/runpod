---
description: Autonomously unblock open PRs — fix conflicts, fix failing checks, update branches, and drive each one to merge without being told twice.
---

Work the repository's open pull requests until they merge, without asking me to confirm each step.
`$ARGUMENTS` may name one or more PR numbers or a branch name — if it is empty, sweep **every** open
PR that is blocked, behind, conflicted, or failing checks. The point of this command: too many PRs
get opened by agents and then abandoned. Opening a PR is the start of the job, not the end. Do not
report back with "the PR needs X" — do X.

## 1. Take stock

List all open PRs with their `mergeable_state`, check status, and review state. For each one decide
what is in the way:

- `dirty` — real merge conflicts against the base branch.
- `behind` — branch is out of date with `main`.
- `blocked` — failing or pending required checks, or a missing review.
- A title that is not a Conventional Commit.
- Waiting on owner review (risky lane) — nothing to fix; leave it, but say so.

Skip a PR only when it is a draft someone is actively working, or it is explicitly waiting on owner
review with green checks. Everything else gets worked.

## 2. Fix conflicts

Check out the PR branch and merge the latest `main` into it (or rebase, if the branch history is
clean and small). Resolve conflicts by understanding both sides — never resolve by blindly taking
one side. Production wins over any older mockup or copy; never "resolve" a conflict by reverting
shipped design or copy. If a conflict is genuinely ambiguous — both sides changed the same logic and
picking one loses behavior — stop on that one PR, say exactly what the two sides do, and keep
working the other PRs.

## 3. Fix failing checks

Read the actual failure log before touching anything. Then:

- **Real defect**: fix it on the PR branch, run the same check locally until it passes, and push. There is no CI here. For a `Dockerfile` change, build the image (`docker build .`) where Docker is available, and say so when it is not.
- **Title**: edit it to a Conventional Commit.
- **Environmental** (rate limit, flaky runner, repo setting): re-run the check. Do not push commits
  to paper over an environmental failure, and say which failures were environmental.

## 4. Bring behind branches current, once

Every sibling merge pushes the remaining PRs behind. Update each PR that reads `behind` in this
pass. A PR in the low-risk lane with auto-merge enabled completes itself once it is green and
current.

This command is one pass, not a watch (owner directive, 2026-09-26). Do not subscribe to PR
activity, do not schedule check-ins, and do not wait for checks you just triggered to finish.
Watching fills the session with notices and check lists, which brings on compaction sooner. Fix what
is broken now, push, report, and stop; if something is still running, say so in the summary. The
owner runs `/pr` again when they want the next pass.

If a low-risk PR has no auto-merge enabled, enable it (SQUASH) where the settings allow it. A risky PR (changing the base image or its digest, the model, or the handler's request or response shape) waits for owner review, and that is the one kind of "stuck"
that is correct.

## 5. Address review comments

If a PR has unresolved review comments, verify each one against the code: fix the real ones, and reply once with a short factual explanation for any that do not
hold. Do not leave a comment unanswered and do not silently ignore it.

## 6. Know when a PR is dead

If a PR is superseded (its change already landed another way) or its branch is beyond saving,
say so and recommend closing it — with the reason — instead of pouring commits into it. Do not
close someone else's PR without saying so in the summary.

## 7. Report back

One short summary: what merged, what is green and waiting only on owner review, what you fixed and
how, what was environmental, and anything genuinely stuck with the exact reason. Plain language,
no jargon, no pleasantries. Commit messages end with the session URL on its own line. Never put a model identifier in any repo artifact.
