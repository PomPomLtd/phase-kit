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

A request rather than a phase id, small enough for the quick lane (conventions, "Two lanes") → say so
and offer `/quick`, before anything below.

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
4. Run the **verify command** of the last built phase (its doc's top line); the full checks wait
   for the first commit. Red before you start → say so; fix only if the owner wants that first (it
   is not this phase). Nothing landed since that phase closed and the command is slow (it needs
   services started, or takes more than a minute or so) → skip it and say so.
5. Show the board (conventions, "The board").

## Step 1 — Orient

Read what the doc's **Read first** line names (older docs: its Session protocol), the code as it is
now, not as the doc remembers it, and the doc itself. From `PROGRESS.md` the row, the neighbours and
the latest log entry. Verify framework and library behaviour against their current docs, never from
memory.

## Step 2 — Plan

Status `in progress` → an earlier session's plan was approved (it is in the Tasks, or in a Plan block
in older docs): resume at the first open task and say so. Otherwise **enter plan mode**, even when the
doc looks complete, and plan this phase only, inside its Out of scope. The doc's Tasks are the plan's
steps: do not copy them out. The plan says what the owner will see at the end, what has changed since
the doc was written, what you would do differently, any open choice, and what the owner will have to
do and when, as one bundle (conventions, "The owner's steps"). Written for the owner: outcomes first,
files second. Before it goes to the owner, pick the phase's review level (conventions, "Review levels")
and act on it; the level also sets the plan's length. A clear plan with little at stake is a dozen
lines, has no review and goes straight to the owner. The owner's approval is the go for code; it is
not a go for deploying, sending or deleting anything. Questions on the way go through AskUserQuestion
with options and a recommendation. Record the approval by correcting the doc's Tasks where the plan
differs, not with a copy of the plan, add the `**Review level**` line, and set the row to `in progress`.

## Step 3 — Build

Small commits with conventional messages, no AI attribution; the checks green before each one.
Every behaviour ships with its test in the same commit. Tick each task in the doc as it finishes
and give the owner the board's one-line update. Stay inside Out of scope: an idea that does not fit
goes to `docs/BACKLOG.md`, a question to the owner, a decision to `docs/DECISIONS.md`. A step only the
owner can do goes on their list, and the build carries on with whatever does not depend on it. After
three failed attempts at one thing: stop, write down what failed, reassess. A phase that grows beyond
a session is split, and the split recorded.

## Step 4 — Verify

Tick each acceptance criterion **in the doc** with its evidence (the conventions' "Evidence"), and
make sure the doc's verify command exists and fails when it should.
Where the phase has a user interface, run the app (the Workflow section's run line) and look at
it, on a phone-sized window too, before ticking; a screenshot is evidence. Run the diff review at the
phase's level; fix what is real. Whatever only the owner can do or judge goes to "Your turn", with
every step written the way the conventions' "The owner's steps" asks, and the status becomes
`awaiting <Owner>` rather than `verified`.

## Step 5 — Close out and stop

Run `/handoff` (the close-out checklist lives there, and it is the same whether the phase finished
or not). One session = one phase: say what the next phase is and start it only if the owner says to
keep going and the context has room.
