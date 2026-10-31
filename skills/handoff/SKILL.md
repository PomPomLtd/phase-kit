---
name: handoff
description: Close a work session properly — update the phase doc (Status, criteria, Handoff), PROGRESS (row, Next, log), CHANGELOG, DECISIONS, the owner's TODO and BACKLOG; run the checks; review the diff; commit on the right branch, push, open the pull request if the project uses them; leave a note the next session can start from. Use at the end of any phase session, when the user says wrap up, hand off, stop here, or when the context is getting long.
argument-hint: "[initiative] [phase id]"
---

# /handoff — leave the project so the next session can start cold

Load `phase-kit:conventions` with the Skill tool unless this session already has, and
`phase-kit:templates` only when one of the files below does not exist yet. Then read `CLAUDE.md` ›
Workflow. The phase comes from the argument, else from what this session worked on, else from
`PROGRESS.md`'s **Next**. The next session reads the docs, not this transcript: write for it, and
say each thing once (conventions, "Say it once").

## 1. The state of the work

- Which tasks are done, which are not, and why. Which acceptance criteria have evidence.
- The checks: run them unless they already went green on this exact tree; name each with its
  result. Red → fix if small; otherwise the handoff says exactly what is red and why, and the status
  stays `in progress`.
- The diff review at the phase's level (conventions, "Review levels"), if the phase's tasks are done
  and it has not run yet, in the background while you write the docs; fix what is real, note the rest.

## 2. The docs

The first two always. The rest only when there is something to add; do not open them otherwise.

1. **The phase doc**: Status (`in progress` / `built` / `awaiting <Owner>`, with the date), the
   criteria ticked with evidence, and the **Handoff** section, about 10–20 lines (review level None: a
   few): what was built and where it lives, what was decided (with `D-` ids), what was left out and
   where it went, what the next session must know (gotchas, a command that must run first, a half-done
   thing). Not a retelling of the diff; commands for the owner go in "Your turn". Newest session first
   when there are several.
2. **PROGRESS.md**: the row; **Next** in one sentence (the next open task of this phase, or the next
   phase); a log entry on top, dated, up to three lines, pointing at the doc's Handoff; open items
   that came up. Over about 400 lines → move the oldest log entries to `PROGRESS-archive.md`
   beside it and say so at the top.
3. **CHANGELOG.md**: an entry on top only when the product changed, written for a reader who does
   not read code: what is different now, why, and the phase.
4. **docs/DECISIONS.md**: every ruling the owner made this session, newest first, next free id.
5. **docs/TODO-<Owner>.md**: a dated section on top, one line per thing only the owner can do now,
   pointing at the phase's "Your turn" for the steps; done items struck through with what happened.
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

The board (conventions, "The board"), then a few plain sentences: what works now, what the next
session starts with, and the one command that starts it (`/phase <initiative> <id>`). Last, so it is
the first thing they see, everything waiting on them as one bundle, with every step written out in
this message (conventions, "The owner's steps"), not pointed at. Then stop.
