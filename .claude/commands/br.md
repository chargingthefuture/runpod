---
description: Do the task on a descriptive branch and open the PR correctly the first time — title, body, and lane set at creation.
---

Do the work described in `$ARGUMENTS` on a properly named branch and open its PR so that every
metadata check passes on the first run. This is the default workflow this repo already mandates —
this command exists because sessions keep ignoring it and developing on the auto-generated
`claude/<slug>` branch. If `$ARGUMENTS` is empty, apply this routine to the work already done in
this session: move any commits off the session branch onto a descriptive branch and open the PR
from there.

## 1. Branch first, before any code

- Create a descriptive branch off the latest `main`: a Conventional-Commit-style prefix plus a
  short kebab-case summary of the task (`chore/bump-ollama-base-image`,
  `docs/readme-model-override`).
- Never develop on, commit to, or open a PR from the session's auto-generated `claude/<slug>`
  branch. If commits already exist there, move them: branch off `main` with the descriptive name,
  cherry-pick or rebase the commits onto it, and abandon the session branch.

## 2. Do the task

- Keep the change minimal and surgical — change only what the task requires.
- Never overwrite approved production design or copy without explicit owner approval.
- Update anything the change makes out of date in the same commit, such as `README.md`. A change is
  not done until those match.
- Commit messages end with the session URL on its own line. Never put a model
  identifier in any repo artifact.

## 3. Verify locally before pushing

There is no CI here. For a `Dockerfile` change, build the image (`docker build .`) where Docker is available, and say so when it is not. Fix what they find. Do not push red.

## 4. Open the PR correctly the first time

Set the title and body **at creation** so no check goes red and needs re-triggering:

- Title: Conventional Commit (`feat:`, `fix:`, `refactor:`, `chore:`, `docs:`, `ci:`, `perf:`,
  `test:`, `build:`, `style:`, `revert:`).
- Body: say what changed and why, and `Closes #<issue>` for any issue it resolves.
- Open ready for review, never as a draft.

## 5. Pick the lane

- **Low-risk** — copy, styling, responsive layout, types, refactors, docs, dead code, test-only
  changes: enable auto-merge (SQUASH) right after opening, where the repository settings allow it.
- **Risky** — changing the base image or its digest, the model, or the handler's request or response shape: open it ready but do **not** enable
  auto-merge. Say in the body that it is waiting on my review, and tell me in your summary.

## 6. Report and stop — no PR watching

Do not watch the PR (owner directive, 2026-09-26). Do not subscribe to its activity, do not schedule
a check-in, and do not wait for CI to finish. Watching fills the session with notices and check
lists, which brings on compaction sooner, and the owner sees the checks on their phone anyway. The
local checks in step 3 are what keep the PR from going red; that is why they are not optional.

Report once, right after opening, in one short summary: branch name, PR number, lane, and what the
local checks showed. Plain language, no jargon, no pleasantries. When the owner wants to know where
open PRs stand, they run `/pr`.
