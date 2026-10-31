---
name: progress
description: Say where the project stands — a progress board of every phase with an approximate % done, the spec statuses, the Next pointer, open items and the owner's open to-dos — and propose the next step. Use at the start of a session, when the user asks what's next, where are we, what's left, what's waiting on me, or for a status report.
argument-hint: "[initiative]"
---

# /progress — where things stand

Load `phase-kit:conventions` with the Skill tool and read `CLAUDE.md` › Workflow (none → say the kit
is not set up here and offer `/kit-setup`). Then for every initiative (or the one named): the spec's
status line, `PROGRESS.md`'s table, **Next** pointer, open items and latest log entry, the README's
Sessions column, `docs/TODO-<Owner>.md`'s open items, and `git status` plus the current branch.
Read, do not change.

Report, short and in plain words:

- The board (conventions, "The board"), first. A `draft` spec has no board yet: say so and name its
  open questions. No initiative at all → say so and offer `/spec` or `/quick`.
- What is waiting on the owner (their TODO's open items, phases `awaiting <Owner>`), as one bundle
  they can do from this message, with every step written out from each phase's "Your turn"
  (conventions, "The owner's steps"), not pointed at.
- Anything odd: uncommitted changes, a branch that is not `main`, a `draft` spec with phase docs,
  a PROGRESS whose Next points at a finished phase, a phase `in progress` with commits of its own
  newer than its latest log entry (quick-lane commits do not count).
- The proposed next step, as one command (`/phase …`, `/spec …`, `/phases …`, `/quick …`, or "nothing
  until the owner has checked X"), with the reason. Ask before doing it.
