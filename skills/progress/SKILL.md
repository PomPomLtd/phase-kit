---
name: progress
description: Say where the project stands — every initiative's spec status, the phase table with statuses, the Next pointer, open items and the owner's open to-dos — and propose the next step. Use at the start of a session, when the user asks what's next, where are we, what's left, what's waiting on me, or for a status report.
argument-hint: "[initiative]"
---

# /progress — where things stand

Read `CLAUDE.md` › Workflow (none → say the kit is not set up here and offer `/kit-setup`), then
for every initiative (or the one named): the spec's status line, `PROGRESS.md`'s table, **Next**
pointer, open items and the two most recent log entries, `docs/TODO-<Owner>.md`'s top section, and
`git status` plus the current branch. Read, do not change.

Report, short and in plain words:

- Per initiative: spec `draft` / `confirmed`; a one-line table of phases and statuses, or just
  the counts when there are many; what **Next** says.
- What is waiting on the owner (their TODO's open items, phases `awaiting <Owner>`), as things
  they can do now.
- Anything odd: uncommitted changes, a branch that is not `main`, a `draft` spec with phase docs,
  a PROGRESS whose Next points at a finished phase, a log entry older than the latest commit.
- The proposed next step, as one command (`/phase …`, `/spec …`, `/phases …`, or "nothing until
  the owner has checked X"), with the reason. Ask before doing it.
