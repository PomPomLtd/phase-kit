---
name: conventions
description: The phase-kit vocabulary (the loop, files and roles, statuses, decisions, evidence, sessions, commits, branches and reviews, writing for the owner). Loaded by the other phase-kit skills; also useful when the user asks how the workflow works or what a status means.
user-invocable: false
---

# phase-kit conventions

The vocabulary every skill uses. A project's own rules (checks, owner, deploy) live in its
`CLAUDE.md` › **Workflow (phase-kit)** section; this file is the part that is the same everywhere.

## The loop

1. **Spec** (`/spec`): one document per initiative, written with the owner, `draft` until every
   open question is answered, then `confirmed`.
2. **Phases** (`/phases`): the confirmed spec sliced into phases that each fit one session, as one
   doc per phase plus a README (the table) and a PROGRESS (status and log).
3. **Build** (`/phase <id>`): one session, one phase: orient, plan mode, approval, build with tests,
   verify with evidence, hand off.
4. **Hand off** (`/handoff`): the docs updated, the work committed, a note for the next session.
5. **Your turn**: the owner checks what only a person can, says what they saw, and the phase becomes
   `verified`.

## Two lanes

- **Quick lane** (`/quick`): a fix, a tweak or a small feature with an obvious shape, done now: the
  change, its test, the checks, a commit. No spec, phase doc, plan mode or handoff. A request that
  fits in a sentence or two belongs here, unless the full lane's list below catches it; say so rather
  than starting a phase for it. A bigger job that still fits one sitting belongs here too, with a plan
  of about ten lines in the chat and the owner's go first.
- **Full lane** (the loop above): anything that needs design decisions, spans sessions, belongs to a
  `draft` spec, changes how money moves, who gets mail or who can log in or see what, deletes data or
  migrates a database.

## Files and their roles

| File | Role | Written by |
|---|---|---|
| `docs/specs/<initiative>.md` | What and why. Status `draft` / `confirmed` / `superseded`. | `/spec` |
| `docs/phases/<initiative>/README.md` | The phase table, the order, the calendar, conventions. | `/phases` |
| `docs/phases/<initiative>/PROGRESS.md` | Status per phase, the **Next** pointer, open items, the log (newest first). "An outdated PROGRESS is a bug." | every phase session |
| `docs/phases/<initiative>/phase-<id>-<slug>.md` | One phase: plan-mode-ready in front, the Handoff at the back. | `/phases`, then `/phase` |
| `docs/DECISIONS.md` | Rulings, `D-001`… newest first, dated and attributed. | anyone who decides |
| `docs/BACKLOG.md` | Parked ideas with a revival trigger. | `/backlog`, close-outs |
| `docs/TODO-<OWNER>.md` | What only the owner can do; newest section first, done items struck through. | close-outs |
| `CHANGELOG.md` | What shipped, for a reader who does not read code. Newest first. | close-outs |
| `CLAUDE.md` › Workflow | The project's own rules: owner, paths, checks, deploy. | `/kit-setup` |

Initiative names are short slugs (`v1`, `shop`, `newsletter`). Phase ids are `0` for the foundation
and then `A`, `B`, `C`… (or numbers if the owner prefers); a phase inserted later takes a letter suffix
(`B2`) rather than renumbering. A phase that needs several sessions numbers them (`C1`, `C2`) in its
own doc; the id stays.

## Statuses

**Spec**: `draft` (open questions remain; never built) → `confirmed` (the owner answered every
question; the answers are decisions) → `superseded` (replaced by a newer spec; say which).

**Phase** (in PROGRESS and at the top of the phase doc):

| Status | Meaning |
|---|---|
| `specced` | the doc exists, nothing built |
| `in progress` | a session started it; the doc holds the approved plan |
| `built` | the tasks are done, the checks green, the criteria ticked with evidence; nothing waits on the owner |
| `awaiting <Owner>` | built, and "Your turn" holds something only the owner can check |
| `verified` | the owner checked and said so (date it) |
| `parked` | stopped on purpose; the reason and the decision id are in the row |

A phase is never `verified` by Claude alone when "Your turn" lists anything.

## The board

The whole project at a glance, worked out from the docs each time and never stored.

```
shop: about 40% done (≈4 of 10 sessions)

0  Foundation            ████████████  verified
A  Product pages         ████████████  awaiting you (one look on a phone)
B  Cart and checkout     █████░░░░░░░  in progress (9 of 21)
C  Payments              ░░░░░░░░░░░░
D  Order mails           ░░░░░░░░░░░░
E  Go-live               ░░░░░░░░░░░░

Next: B, the empty cart. Waiting on you: one look.
```

- **Rows**: one per phase in table order, in a code block so the columns line up; the name cut to
  about 20 characters. A `phase/*` branch not merged yet holds its phase's truth: read that row and
  doc from the branch (`git show`).
- **Share**: `specced` 0. `in progress` the doc's ticked checkboxes over all its checkboxes
  (`grep -cFi -e '- [x]' <doc>` and `grep -cF -e '- [ ]' <doc>`, no need to read the doc; no
  checkboxes → your honest estimate). `built`, `awaiting <Owner>`, `verified` and an older doc's own
  words for done ("Complete", "✅") 1. `parked` 0, and left out of the %.
- **Bar**: 12 cells; 12 × share, rounded half up; `in progress` at least 1 cell and at most 11.
- **Right column**: nothing for `specced`. Otherwise the status in the owner's words, with what it
  needs from them in a few words: "awaiting you (one look on a phone)", "in progress (9 of 21)",
  "built", "verified", "parked (D-012)".
- **The %**: each phase counts for its sessions in the README table (a range: the upper end; no
  column: 1). Share × sessions, summed, over all sessions, not read off the bars; rounded half up to
  the nearest 5 and always "about", with the ≈ count beside it, to the nearest half, so the number
  can be checked. At most "about 95%" until every phase is built; then "all built, waiting on you"
  until nothing waits on the owner.
- **Several initiatives**: the one being worked on in full, each other one line
  (`newsletter  ██████░░░░░░  about 50%`).

Show the full board when a `/phase` session starts, when a phase session stops to wait on the owner
with a result, at `/handoff` and in `/progress`. Between those, after each finished task, one line:
`B  █████░░░░░░░  9 of 21 · about 40% · cart page done`.

## Say it once

Every doc costs time to write and is read again at the start of a session.

- A fact has one home; elsewhere, link to it. A phase doc points at the spec's § instead of retelling
  it. PROGRESS's log points at the phase doc's Handoff. A TODO item points at "Your turn". The one
  exception is a message to the owner: what they have to do is written out in it ("The owner's steps").
- Sizes to aim for: a phase doc 40–80 lines before its session, a Handoff 10–20 lines (review level
  None: a few), **Next** one sentence, a log entry up to three lines, a spec no longer than its
  decisions need (a small initiative: a page). A phase doc that wants more is two phases, or holds
  design that belongs in the spec.
- A section with nothing to say is left out.

## Decisions

`D-001`, `D-002`, … per project, in `docs/DECISIONS.md`, newest first. An entry has a title, who
decided and when, which initiative and phase, what was decided, what the alternatives were, and
why. Specs cite them (§3), phase docs cite them, commits may cite them (`feat(shop): … (D-014)`).
A decision is changed by a new entry naming the old one. The owner's words are quoted, not
paraphrased, where the wording matters.

Open questions are asked with concrete options and a recommendation, batched, through
AskUserQuestion when it exists; never as open-ended essays. The answer becomes a decision at once.

## Evidence

An acceptance criterion is ticked with what proves it: the test's name and that it passes, a
count, a command and what it printed, a screenshot, a page opened in the browser; a line is usually
enough. "Done" without evidence is not ticked. Numbers and judgements only a person can make go to
"Your turn".

Every phase names one **verify command** at the top of its doc: a command that fails when the
phase's main promise is broken (a test file, a smoke script, a build). It is the first thing the
next session runs, so a phase that quietly broke is caught before new work starts; a phase with no
such command yet gets one as its first task. Where more is at stake than a revert, the session that
built a phase does not grade it alone: the diff review (below) reads the doc and the diff with fresh
eyes.

## Sessions

- **One session, one phase.** Hand off rather than start the next phase. Carry on in the same
  session only when the owner says to and the context has room; the handoff still comes first.
- **Plan once, resume many**: a phase `in progress` has its approved plan in the doc's Tasks (older
  docs: a Plan block) and is resumed at the first open task; plan mode is for a phase not started, or
  a plan that no longer fits.
- **Three attempts**: after three failed attempts at one thing, stop, write down what failed and
  why, and reassess (a different approach, a smaller problem, or a question to the owner).
- **A phase that blows up** mid-session is split: the doc records what landed and what moved to a
  new phase doc; PROGRESS says so.
- **Context**: when the session is long, hand off early. The next session reads the docs, not the
  transcript.
- **The spec stays true.** When a phase departs from the spec (the owner changed their mind, reality
  disagreed), the spec is corrected in the same session with a `D-` entry, so the next phase is
  built from a spec that matches the product, not from history.
- **Parallel sessions**: `git status` before starting; uncommitted changes that are not yours mean
  another session may own the tree, so propose a worktree rather than sharing it. Never a bare
  `git stash`.

## Commits and gates

- Conventional messages (`feat(area): …`, `fix(area): …`, `docs(phases): …`), no AI attribution of
  any kind in messages. Small commits that each leave the project working.
- The project's checks (CLAUDE.md › Workflow › Checks) are green before every commit. A failing
  check is fixed, never skipped or disabled.
- Every change that adds or changes behaviour ships with its test in the same commit.
- Deploying, sending, publishing, deleting data, spending money: only on the owner's explicit go in
  this session, never inferred from a plan approval.
- Without git the kit still works; the close-out says "not committed (no repository)".

## Branches, reviews and pull requests

The project's Workflow section says which of these applies:

- **main directly**: every commit lands on `main`; fine while nothing deploys from it and one
  person works on it.
- **phase branches** (the recommendation wherever a push to `main` deploys): a phase works on
  `phase/<initiative>-<id>` off `main`, the close-out leaves it committed and pushed, and merging
  is the owner's go, in the session ("merge it" is the go; a plan approval is not). Merging is
  deploying; a database migration, if any, goes first.
- **pull requests**: phase branches, and the close-out opens a pull request whose body is the phase
  doc's Handoff, when the project is on GitHub and `gh` is available; the checks run in CI (Phase 0
  sets that up). The owner merges on GitHub or says "merge it".

Two reviews, both by a fresh subagent that knows nothing of the session, both at the phase's review
level ("Review levels", below):

- **Plan review**, before code: "find what this plan gets wrong or leaves out". The findings that
  change the plan are taken to the owner; the rest are noted in the doc.
- **Diff review**, once per phase, when its tasks are done: the diff against the phase doc's Tasks
  and Acceptance criteria, hunting bugs, checks that pass when they should not, missing tests and scope
  creep. Not for a docs-only diff or a session that stops mid-phase. It runs in the background while
  the session writes the docs, and no code changes until it reports. Real findings are fixed in the
  session; the ones that are not go to the Handoff with the reason.

## Review levels

A review is worth its wait only where a mistake would cost more than the wait. Each phase gets one
level, picked once the plan is drafted, from what a mistake would cost rather than from the topic:
rewording an order mail is not the same as changing who receives it. The level sets both reviews and
the length of the plan.

| Level | When | Plan | Each review |
|---|---|---|---|
| **None** (the default) | The plan is clear and follows a pattern the project already has. A mistake would show in the tests or at the owner's first look, and reverting a commit undoes it. | a dozen lines | no subagent; the session reads its own diff once against the Tasks and criteria |
| **Light** | A mistake would cost more than a revert but stays contained (a local copy, behind a switch, nothing live), or one or two things in the plan rest on something unproven, or several parts depend on each other. | up to 30 lines | 3–5 minutes |
| **Deep** | A mistake could reach real people or live data and be hard to undo, and the tests would not catch it: a production migration, deletion or cutover; how money moves; who gets mail; who can log in or see what, or another security boundary; commands the owner will type on a live server. | as long as it needs | 15–20 minutes |

Then act on it:

- **Clear**: do it. None: the plan goes straight to the owner. Light or Deep: say which and why in one
  line, and run the review without asking.
- **Unsure between two levels**: ask once, through AskUserQuestion, before running anything. Say what
  is at stake in a sentence or two (what would go wrong, who would notice, how hard it is to undo),
  offer the two levels with their waits, and recommend one.
- **The plan's first line** names the level and why ("Review level: none, a new filter on a page that
  has tests"), so the owner can ask for more when approving. On approval the doc's top lines get the
  same words as `**Review level**`, and the diff review and later sessions use that instead of deciding
  again. A doc without the line (started earlier): pick the level when you first need it.
- **Going up**: a build that turned out riskier than its plan (a new command for a live server, a much
  bigger diff) raises the level for the diff review; say so in one line.
- **Who overrules**: the owner's word beats everything ("skip the review", "do a deep one"). A spec or
  phase doc that rules a review out settles it; one that asks for a review without naming a level gets
  Light or Deep, by the table.
- **Once per plan**: a phase resumed `in progress` does not get another plan review.

What the subagent is told:

- **Both**: read-only; the plan or the diff, the phase doc and the one to three things you worry about
  most. Findings only, most serious first, each with its evidence and the change it asks for; no style
  nits; what checked out gets one line at most.
- **Light**: on the next model down from the session's, not the smallest, where the tool allows it.
  Those and the files they hinge on, at most about 12 reads or commands in total, without tracing
  into dependencies; at most five findings, under 300 words. What it cannot settle within that, it
  names rather than chases. If that is a reason for a Deep, treat it as "Unsure" above. It is a quick
  second look and will miss things, which is why anything live gets a Deep.
- **Deep**: the session's model and no limit on reads; it checks claims against the code and its
  dependencies, and a diff review may run the tests. Claude Code's own `/code-review`, when it exists,
  can be the Deep diff review.

## Writing for the owner

The owner may not read code. Plans, handoffs and "Your turn" lists say what the user will see and
do, in plain words, naming a file only when the owner has to open it. A question offers options
with a recommendation. A problem is stated with what it blocks and what the choices are.

## The owner's steps

Anything only the owner can do: a command on a server, a Cloudflare rule, a setting in a dashboard, a
look at a page.

- **Everything else first.** Finish all you can before asking: the code, the checks, the docs, the
  commands written and tried wherever they can be tried, and what comes after their steps, prepared so
  work resumes the moment they report back. A step that comes up mid-build goes on the list, and the
  build carries on with whatever does not depend on it.
- **One bundle.** Hand over all their steps at once, in one numbered list, in the order to do them,
  rather than one at a time across a session. Stop for them earlier only when your next work really
  depends on the result, and say so. A step that has to wait (a quiet hour, an overnight run) gets its
  own bundle, with the time on it.
- **In full, in the message, every time.** The message alone is enough to act on, with each step saying:
  - where: which server, user and folder, or which site and page;
  - what: the exact command in its own code block, ready to paste, or the exact clicks and values; no
    placeholder you could have filled in, and if one is unavoidable, say where to find the value;
  - what they should see, and what to do if they do not.

  End with what to send back ("paste what step 4 printed", "say done"). Never "now do step 3", "as
  above" or "see the runbook": the doc keeps its copy, and the message works without it. Commands that
  must stop on a failure get a step each, so a failed line cannot run on into the next.
