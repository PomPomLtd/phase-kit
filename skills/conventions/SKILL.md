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

## Files and their roles

| File | Role | Written by |
|---|---|---|
| `docs/specs/<initiative>.md` | What and why. Status `draft` / `confirmed` / `superseded`. | `/spec` |
| `docs/phases/<initiative>/README.md` | The phase table, the order, the calendar, conventions. | `/phases` |
| `docs/phases/<initiative>/PROGRESS.md` | Status per phase, the **Next** pointer, open items, the log (newest first). "An outdated PROGRESS is a bug." | every session |
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
count, a command and what it printed, a screenshot, a page opened in the browser. "Done" without
evidence is not ticked. Numbers and judgements only a person can make go to "Your turn".

Every phase names one **verify command** at the top of its doc: a command that fails when the
phase's main promise is broken (a test file, a smoke script, a build). It is the first thing the
next session runs, so a phase that quietly broke is caught before new work starts; a phase with no
such command yet gets one as its first task. The session that built a phase is not the one that
grades it alone: the diff review (below) reads the doc and the diff with fresh eyes.

## Sessions

- **One session, one phase.** Do not start the next phase in the same session; hand off instead.
- **Plan once, resume many**: a phase doc that already holds an approved plan is resumed at the
  first open task; plan mode is for a plan that does not exist yet or no longer fits.
- **Three attempts**: after three failed attempts at one thing, stop, write down what failed and
  why, and reassess (a different approach, a smaller problem, or a question to the owner).
- **A phase that blows up** mid-session is split: the doc records what landed and what moved to a
  new phase doc; PROGRESS says so.
- **Context**: when the session is long, hand off early. The next session reads the docs, not the
  transcript.
- **Small changes are not phases.** A fix or tweak that fits in one sentence skips the ceremony:
  do it with its test, run the checks, commit, one line in `CHANGELOG.md` if a user would notice.
  No phase doc, no plan mode, no handoff. If it touches a phase in progress, that phase's doc gets
  a line. Three such changes in a row that belong together are a phase.
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

Two reviews, both by a fresh subagent that reads the phase doc and nothing of the session:

- **Plan review**, before code, on a phase that touches money, mail, authentication, deletion,
  data migration or anything the spec calls risky: "find what this plan gets wrong or leaves
  out". The findings that change the plan are taken to the owner; the rest are noted in the doc.
- **Diff review**, before every handoff: the diff against the phase doc's Tasks and Acceptance
  criteria, hunting bugs, missing tests and scope creep. Real findings are fixed in the session;
  the ones that are not go to the Handoff with the reason. Claude Code's own `/code-review`, when
  it exists, is that review.

## Writing for the owner

The owner may not read code. Plans, handoffs and "Your turn" lists say what the user will see and
do, in plain words, naming a file only when the owner has to open it. A question offers options
with a recommendation. A problem is stated with what it blocks and what the choices are.
