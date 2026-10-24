---
name: handoff
description: Close a work session properly — update the phase doc (Status, criteria, Handoff), PROGRESS (row, Next, log), CHANGELOG, DECISIONS, the owner's TODO and BACKLOG; run the checks; review the diff; commit on the right branch, push, open the pull request if the project uses them; leave a note the next session can start from. Use at the end of any phase session, when the user says wrap up, hand off, stop here, or when the context is getting long.
argument-hint: "[initiative] [phase id]"
---

# /handoff — leave the project so the next session can start cold

Load `phase-kit:conventions` and `phase-kit:templates` with the Skill tool, then read `CLAUDE.md` ›
Workflow. The phase comes
from the argument, else from what this session worked on, else from `PROGRESS.md`'s **Next**. The
next session reads the docs, not this transcript: write for it.

## 1. The state of the work

- Which tasks are done, which are not, and why. Which acceptance criteria have evidence.
- The checks: run them; name each with its result. Red → fix if small; otherwise the handoff says
  exactly what is red and why, and the status stays `in progress`.
- The diff review of the conventions, if it has not run yet in this session; fix what is real,
  note the rest.

## 2. The docs, in this order

1. **The phase doc**: Status (`in progress` / `built` / `awaiting <Owner>`, with the date), the
   criteria ticked with evidence, and the **Handoff** section: what was built and where it lives,
   what was decided and why (with `D-` ids), what was left out and where it went, what the next
   session must know (gotchas, a command that must run first, a half-done thing). Newest session
   first when there are several.
2. **PROGRESS.md**: the row; the **Next** pointer (the next open task of this phase, or the next
   phase); a log entry on top, dated, that a reader can follow without the transcript; open items
   that came up. Over about 400 lines → move the oldest log entries to `PROGRESS-archive.md`
   beside it and say so at the top.
3. **CHANGELOG.md**: an entry on top only when the product changed, written for a reader who does
   not read code: what is different now, why, and the phase.
4. **docs/DECISIONS.md**: every ruling the owner made this session, newest first, next free id.
5. **docs/TODO-<Owner>.md**: a new section on top with what only the owner can do now, starting
   with the phase's "Your turn" list; done items struck through with what happened.
6. **docs/BACKLOG.md**: ideas parked this session, with their revival trigger.
7. **The spec**, where the phase departed from it: correct the spec and cite the `D-` entry, so
   the next phase is built from a spec that matches the product.
8. `CLAUDE.md` where the project's shape changed (a new check, a new run command, a rule learned
   the hard way). Keep it under 200 lines.

## 3. Git

On the branch the Workflow section asks for: commit the code and the docs (`feat(area): …`,
`docs(<initiative>): phase <id> handoff`), then push the branch when a remote exists. Pull
requests in the Workflow → open one with `gh pr create`, title "Phase <id>: <title>", the Handoff
as the body; an existing pull request gets a comment with what changed. Never merge, deploy,
migrate or publish here: those need the owner's go in the session. No repository → say "not
committed (no repository)".

## 4. Tell the owner

A few plain sentences: what works now, what is waiting on them (point at their TODO file and
"Your turn"), what the next session starts with, and the one command that starts it
(`/phase <initiative> <id>`). Then stop.
