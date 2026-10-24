---
name: phase
description: Run one build session for one phase from its doc in docs/phases/<initiative>/ — resolve and gate (spec confirmed, prerequisites met, tree clean), orient, enter plan mode and get approval, build with tests, verify with evidence, hand off. Use whenever the user names a phase to work on ("start phase B", "continue A", "/phase 0"); no argument proposes the next one.
argument-hint: "[initiative] <phase id>"
---

# /phase — one session, one phase

Load `phase-kit:conventions` with the Skill tool, then read the project's `CLAUDE.md` › Workflow
section (none → say so and offer `/kit-setup`; a project with its own `/phase` skill or command
uses that one instead, not this). The argument is a phase id, optionally preceded by the
initiative (`B`, `shop B`); no initiative → the Workflow section's default. No argument → read
`PROGRESS.md`'s **Next** pointer and propose it; never pick silently.

## Step 0 — Resolve and gate

1. Find `docs/phases/<initiative>/phase-<id>-*.md` (the paths the Workflow section names). No
   doc → the phase is unspecced: offer to write it (`/phases`) first; never improvise a build.
2. Read the doc's **Status** and **Prerequisites**, its row in `PROGRESS.md`, and the spec's
   status line. Then gate:
   - spec `draft` → stop: never build from an unconfirmed spec; offer `/spec` to confirm it.
   - prerequisites not met → say what is missing and stop.
   - status `built`, `awaiting <Owner>` or `verified` → nothing to build; offer to walk the
     owner through "Your turn", or ask what they actually want.
   - status `parked` → name the decision that parked it; proceed only on the owner's say-so.
   - `specced` or `in progress` → continue.
3. `git status`. Uncommitted changes that are not yours → another session may own the tree;
   propose a worktree rather than sharing it. Then the branch the Workflow section asks for:
   `phase/<initiative>-<id>` off `main` (create it, or switch to it if it exists), or `main`.
4. Run the project's checks once, and the **verify command** of the last built phase (its doc's
   top line). Red before you start → say so; fix only if the owner wants that first (it is not
   this phase). A request that fits in one sentence is not a phase: say so and offer to just do it
   (conventions, "Small changes are not phases").

## Step 1 — Orient

Read fully, in the order the doc's **Session protocol** lists: the spec sections it names, the
code it names, `PROGRESS.md` (the row, the neighbours, the latest log entries), `docs/DECISIONS.md`
entries it cites, the doc itself. Look at the code as it is now, not as the doc remembers it.
Verify framework and library behaviour against their current docs, never from memory.

## Step 2 — Plan

The doc already holds an approved plan from an earlier session → resume at the first open task
and say so. Otherwise **enter plan mode** and plan this phase only, inside its Out of scope: what
the owner will see at the end, the steps, the files, the tests, the data changes, what is deferred
and where it goes. Written for the owner: outcomes first, files second. A risky phase (money, mail,
authentication, deletion, migration, or the spec says so) gets the plan review of the conventions
before the plan goes to the owner. The owner's approval is the go for code; it is not a go for
deploying, sending or deleting anything. Questions on the way go through AskUserQuestion with
options and a recommendation. Record the approved plan in the doc (Session protocol or a
"Plan" block) and set the row to `in progress`.

## Step 3 — Build

Small commits with conventional messages, no AI attribution; the checks green before each one.
Every behaviour ships with its test in the same commit. Stay inside Out of scope: an idea that does
not fit goes to `docs/BACKLOG.md`, a question to the owner, a decision to `docs/DECISIONS.md`.
After three failed attempts at one thing: stop, write down what failed, reassess. A phase that
grows beyond a session is split, and the split recorded.

## Step 4 — Verify

Tick each acceptance criterion **in the doc** with its evidence (the conventions' "Evidence"), and
make sure the doc's verify command exists and fails when it should.
Where the phase has a user interface, run the app (the Workflow section's run line) and look at
it, on a phone-sized window too, before ticking; a screenshot is evidence. Run the diff review of
the conventions; fix what is real. Whatever only the owner can judge goes to "Your turn", as a
list a person can follow, and the status becomes `awaiting <Owner>` rather than `verified`.

## Step 5 — Close out and stop

Run `/handoff` (the close-out checklist lives there, and it is the same whether the phase finished
or not). One session = one phase: do not start the next phase; say what it is.
