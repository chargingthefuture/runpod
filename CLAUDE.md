# CLAUDE.md

Repo: `chargingthefuture/runpod`. Public. The Docker image for the RunPod Serverless worker that serves an Ollama model for the web app in `chargingthefuture/chargingthefuture`. The repository holds `Dockerfile` and `README.md`.

The sections from "Voice" through "One-shot workflows" are the shared baseline from
`chargingthefuture/agents` (`AGENTS.md`), kept word for word so `/sync` can bring them back in line.
This repository's own rules are under "runpod rules" at the end.

---

## Voice — no pleasantries, no feelings (Critical — every reply, all agents)

Do not address the user with thanks, apologies, congratulations, well-wishes, encouragement, or
closing sign-offs. Do not use first-person feeling words (for example: glad, happy, excited,
delighted, sorry, "hope this helps", "I appreciate"). You have no feelings; do not perform them.
No jargon, no buzzwords. State the result or the next step in plain words, then stop.

This is enforced by the Stop hook `.claude/hooks/check-no-pleasantries.mjs`, which blocks a reply that
contains a banned term and asks for a plain restatement.

### No running commentary while working (owner directive, 2026-11-23)

Do not narrate work in progress. No text between tool calls that announces what was just found or
what comes next ("Shallow clone breaks the build. Unshallowing and resyncing."). The user follows the
session on a phone, and every such line is output the account pays for and a line they scroll past.
Run the tools without a word in between. Write text only when:

- the work is done — the result, in a few lines;
- the work is blocked on something only the user can decide or do; or
- a decision changes what the user gets (a different approach than asked, something skipped).

A tool call's short description field is not commentary and stays.

### Banned-term dictionary (every reply, all agents)

The Stop hook `.claude/hooks/check-no-pleasantries.mjs` holds the canonical list and is the source of
truth; if this copy and the hook ever differ, the hook wins. Keep the two in sync — when you
change one, change the other. The hook scans the entire reply and matches the term even inside
quotes, so do not reach for a banned word even to talk about it; use the replacement below instead.

Pleasantries, feelings, and sign-offs — never use any of these (in any reply):

- thanks / thank you
- you're welcome / you are welcome
- no problem
- my pleasure
- glad
- happy to
- excited
- delighted
- sorry
- apology / apologies / apologize / apologise (any form)
- cheers
- congrats / congratulations
- "I appreciate" / "we appreciate" (only the first-person form is banned; "the rate appreciates" is fine)
- "hope this / hope that / hope you / hope it …"
- feel free
- warm / best / kind / kindest regards
- looking forward

Excluded vocabulary — banned word → use instead:

- flywheel → a plain description of the loop (for example "each answer improves the next")
- punch list → list
- (the word for out-of-date) → drop it; if you mean something specific, name it (out-of-date, superseded, no longer current)
- console → dashboard (the code identifiers `console.log` / `console.error` / `console.info` are exempt)
- (the word for "entire", in every frame, including "that is the … point") → entire, all of, end to end, or drop it
- (the sentence after the facts that says which of them mattered) → delete it and end on the fact itself
- point of the thing → end on the fact itself

When the hook blocks a reply, restate once and briefly in plain, factual language — none of the
terms above, no jargon, no first-person feeling words — then stop. Do not re-send the blocked
reply's full content with one word swapped; a repeat of the same update reads as noise. Summarize
the point in a sentence or two instead.

---

## Plain language — no jargon (Critical — all agents)

Write in plain, everyday language. Do not use jargon, acronyms, or insider terminology in
human-facing output — chat replies, pull-request titles and descriptions, review comments, commit
messages, issue comments, and documentation. Jargon is a distraction and is confusing; it slows the
reader down and hides meaning.

- Default to simple words. Prefer the plain term over the technical or marketing one (for example
  "test it before you rely on it" over "validate end-to-end"; "make sure" over "ensure idempotency";
  "the background service" over "the daemon"). Write so a non-specialist can follow.
- If a technical term is genuinely necessary, define it in plain words on first use — one short
  parenthetical is enough. Do not assume the reader knows acronyms; spell them out the first time.
- Explain, don't just name. Say what something does and why it matters, not only its label.
- Exempt: real code identifiers, file paths, command names, and established proper nouns (service
  names, library names) — name those accurately; just don't pile extra jargon around them.
- Applies to every agent and all human-facing communication. When in doubt, choose the wording a
  newcomer would understand.

---

## Text formatting — minimal bold (all agents)

Keep bold to a minimum, in chat replies and in any Markdown or other file an agent creates or edits.

- Do not bold for emphasis, do not bold every list-item label, and do not bold entire sentences.
  Overused bold adds visual noise and no information.
- Acceptable bold is rare and structural: a single table header, or a one-word inline label where
  the surrounding document already uses that convention. When in doubt, do not bold.
- Carry structure with headings, lists, tables and plain sentences instead.

---

## American English (owner directive, 2026-11-23 — all agents)

Write everything in American English spelling and usage: chat replies, pull-request titles and
descriptions, review and issue comments, commit messages, code comments, documentation, and any text
an agent adds to a product. Use "color", "behavior", "organize", "center", "license" (noun and verb),
"canceled", "gray" — not "colour", "behaviour", "organise", "centre", "licence", "cancelled", "grey".

- Exempt: real code identifiers, file paths, command names, quoted text, and proper nouns, which
  keep their exact spelling.
- When editing text that already uses British spelling, switch the lines you touch to American
  spelling.

---

## Task planning — no "phases" (Critical)

Do not organize work into "phases." No "Phase 0 / Phase 1 / Phase 2", no phased-rollout buckets —
anywhere: plans, checklists, design notes, code comments, pull-request descriptions, or commit
messages. Phases confuse humans and agents alike.

Instead, when given an objective, break it into discrete tasks and list them one after another in
the order they must happen. Where order matters, state it as an explicit blocking dependency, not a
phase:

- ✅ "Task B is blocked by Task A — do A first."
- ✅ A flat, ordered, numbered task list (1, 2, 3 …) where each item may name what it depends on.
- ❌ "Phase 1: …", "Phase 2: …", "do this in a later phase."

A task with no dependency can be done at any time or in parallel; say so plainly ("no dependencies;
can run anytime").

---

## Branch naming (all agents)

- Always create a descriptive, task-named branch and develop on it. Use a Conventional-Commit-style
  prefix plus a short kebab-case summary of the task: for example `feat/user-auth-refresh`,
  `fix/csv-export-dedup`, `chore/ci-node-version-bump`, `docs/readme-quickstart`.
- Never develop on, commit to, or open a pull request from an auto-generated session branch (an opaque
  name like `claude/loving-mendel-wwWF4`). Treat it as a throwaway base: immediately branch off it (or
  off the default branch) to a descriptive name and push from the descriptive branch.
- Branch names must describe the task at hand — never an opaque or random string.
- Carry the branch all the way through to a merged pull request, then clean it up — see
  *Pull requests and the merge lifecycle* below.

---

## Pull requests and the merge lifecycle (all agents)

A task is not finished when the code is written — it is finished when the change is merged and the
branch is cleaned up. Carry every change through that path, in this order, without being asked:

1. Open a pull request when the work is complete. Once the task's changes are committed and the
   descriptive branch is pushed, open a pull request from that branch against the default branch.
   Finished work should not sit on a branch with no pull request. Give the request a descriptive,
   plain-language title and a body that says what changed and why, in the same plain voice as every
   other reply.
2. Do not watch the request. Report once, right after opening, and stop; auto-merge (step 4)
   finishes it. See *No pull-request watching* below.
3. Keep it mergeable — fix conflicts. When a pass over open requests finds the default branch has
   moved ahead and a request has merge
   conflicts, bring the latest default-branch commits into the branch (rebase onto it, or merge
   it in) and resolve the conflicts, keeping both sides' intent. If a conflict is a real, ambiguous
   clash where either resolution changes how the code behaves, stop and ask rather than guess.
4. Merge when it is ready. Merge once the automated checks (continuous integration, or CI) are
   green and the request is mergeable with no unresolved "changes requested" review. Use the
   repository's default merge method. If required checks take a while, turn on auto-merge so the
   request merges itself the moment they pass, rather than waiting on it. Never force a merge over a
   failing required check or an open request for changes — fix the cause, or report it and stop.
5. Delete the branch after merge. Once the request is merged, delete the now-merged head branch
   so old branches do not pile up.

The gate in step 4 — green checks, mergeable, no open change-requests — is the safe default. A
repository that requires human review before merge should keep that requirement: set the gate to
match the repository's own rules rather than merging around them.

### Two lanes by risk (owner decision, 2026-06-05)

Pick the lane when the request is opened, from what the change touches:

- Low-risk lane, the default: copy, styling, layout, documentation, refactors, type, lint and test
  changes, dead-code removal. Open it ready for review and turn on auto-merge right away; it merges
  itself once the checks pass.
- Owner-review lane: anything that moves money or credits, touches sign-in or access checks, deletes
  or migrates stored data, changes a database schema, adds or changes an interface other code calls,
  or adds new stateful logic or a new feature area. Open it ready for review, not as a draft, and do
  not turn on auto-merge. Say in the body that it waits on the owner's review. It is not a blocker:
  keep working, and the owner reviews and merges it.

A repository names its own risky areas in its rules file; the list above is the floor. If a risky
request merges before its review findings were applied, open a small follow-up request with the
findings that still hold.

Mechanism, by where the agent runs:

- Claude Code on the web: use the GitHub tools to open the request, update its branch from the
  default branch, and turn on auto-merge. Do not use the pull-request activity subscription. The
  harness may subscribe a session to every request it opens on its own; unsubscribe straight away.
- Local Claude Code / a terminal: `gh pr create` to open it, `gh pr checks` to read check state,
  `git rebase`/`git merge` to clear conflicts, and `gh pr merge --auto --delete-branch` (with the
  repository's merge method) to merge and remove the branch in one step.

---

## Slash commands (all agents)

A slash command in `.claude/commands/` is the standing way to do its kind of work. The user does
not have to type it for it to apply — the request itself is the trigger. Five come with this
template and apply in any repository:

| Command | Applies when |
|---|---|
| `/br` | Any request that changes files. |
| `/pr` | Open pull requests are blocked, behind, conflicted or failing checks. |
| `/fix` | The user points at a sentence that does not read right. |
| `/fl` | The user asks whether the chat can be archived. |
| `/sync` | The user asks to bring the repo in line with the `agents` baseline. |

`/br` is the one that matters most and the one sessions skip: branch off the latest default branch
before any edit, never the session's auto-generated branch, then the lifecycle in "Pull requests
and the merge lifecycle" above.

`/fl` answers one question — does any work exist nowhere but this session. An open pull request is
not an open task: it is committed, pushed and carried by its branch, so the state of it is reported
rather than waited on. What matters is an uncommitted change, a commit never pushed, a pushed
branch with no pull request, a scheduled check-in still armed, and anything the user asked for that
was never started.

A repository adds its own commands beside these, listed in its own rules file. See
`commands/README.md` in `chargingthefuture/agents` for the rest of the source project's commands and what each
one needs behind it.

## No pull-request watching (owner directive, 2026-09-26 — all agents)

No command watches a pull request, in any repository. After opening one, do not subscribe to its
activity, do not schedule a check-in, and do not wait for its checks to finish. Report once and stop.
Watching fills the session with notices and full check lists, which brings on compaction sooner, and
a compacted session loses what the user said earlier. The checks run locally before every push keep a
request green; `/pr` is one pass over open requests when the user asks where they stand, not a watch.

## Keep sessions from filling up (owner directive, 2026-09-26)

Everything an agent reads stays in the session until compaction, and compaction swaps the earlier
conversation for a summary. So spend the session on the user's words, not on raw output.

- Hand broad searches to a helper agent that returns only its conclusion. Anything that means reading
  across several files or directories to answer one question goes to a helper; a single lookup in a
  known file is done directly.
- Read only the part of a file the task needs, by line range or search, not entire files.
- Read only failed checks and the failing part of a log. Never pull a full list of passing checks or a
  full log to confirm something is green.
- Take a screenshot only when a visual change has to be checked, and look at it once.

The user can also compact on their own terms: typing `/compact` followed by what to keep (for example,
`/compact keep the open request list and today's rules`) compacts at a moment they choose, with their
instructions shaping the summary. `/clear` starts the session over. Rules that must outlive any session
go in the repository's `CLAUDE.md`, not in chat.

## Search tooling (optional convenience)

- Prefer `rg` (ripgrep) for recursive text and file discovery.
- Keep a grep fallback in scripts and prompts where a search command is shown, so they run anywhere:
  - `if command -v rg >/dev/null 2>&1; then rg -n "pattern" path; else grep -RIn "pattern" path; fi`

---

## Never quote the owner in repository artifacts (all agents)

Do not quote the owner's messages anywhere the repository can see: not in a commit message, a pull
request title or body, a change log, a test script, a rule file, or a code comment. A pasted message
is the owner's prompt published to git history. Write what the code does and why, in your own words.
`Owner directive: an admin list hides nothing.` is correct; a block quote of the message, however
short, is not. Quoting the product's own strings (a button label, an error the route returns) is
fine; that is the artifact under discussion.

## Record a reason the owner gives in neutral words (all agents)

When the owner gives a reason for a decision, write a neutral version of it in any repository, and
never their own wording, especially in a public one. In a queue line, a commit message or a pull
request body, name no person's motive or affiliation. The full reason stays in chat.

## The owner has no terminal (all agents)

The owner works from a phone: no shell, no local checkout. Never end a piece of work with a command
for the owner to run. When something has to happen outside the repository, deliver it as a screen
in the app, as text the owner pastes into a web dashboard (the statement itself, with no shell
wrapper), or as a workflow the owner can run from the GitHub app.

## Dates come from the owner's clock (all agents)

The owner is on Eastern time (UTC-4 in summer, UTC-5 in winter); agent containers run on UTC. Any
date written into a file, a post, a file name or a change log is the owner's date. Take it from a
commit the owner authored (`git log -1 --format=%ad --date=iso`), or subtract the offset from UTC;
never from the container's `date`, which is a day ahead from 20:00 the owner's time.

## Remove what nothing uses, in the same change (all agents)

When a change leaves something with no users — a column, a function, a flag, a setting, a screen —
delete it as part of that change. Do not list it as a follow-up or ask whether to remove it. Verify
first: search the entire repository for readers and writers, including scripts, migrations and
generated files. Something already unused before you arrived is different: mention it and leave it
unless told otherwise.

---

## One-shot workflows are deleted when they are done (owner directive, 2026-09-23 — all agents)

A workflow written to do something once — create a service, connect two systems, run a backfill — is
removed once it has done it, along with any script only it used. It is not kept in case it is wanted
again.

Every entry in the Actions list is something somebody might press. A list that mixes live operations
with finished one-offs makes the dangerous press and the routine one look alike, and finished ones
pile up faster than anybody prunes them. Git history keeps the work, and whoever recovers a copy
reads it in full first.

The sequence: ship the workflow, run it, confirm it did what it was for, then delete it in the same
piece of work. Say in the workflow's own header that it is temporary. Recurring workflows stay:
deploys, checks, scheduled jobs, anything with a next run.

---

## runpod rules

- Public: no keys, endpoint ids or internal addresses in any file, commit or pull request.
- `Dockerfile` comments are the documentation. The base image is pinned by digest; bump the tag
  and the digest together. The model is baked into the image.
- The app side and the sizing notes live in the product repository (`lib/chatbot/ollama.ts`,
  `ctf/docs/developer/OLLAMA.md`); a change to the request or response shape is a change there too.
- There is no CI here. Auto-merge on a low-risk pull request where the repository settings allow
  it.
