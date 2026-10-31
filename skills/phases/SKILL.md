---
name: phases
description: Slice a confirmed spec into phases that each fit one session, and write docs/phases/<initiative>/ — README (the phase table and order), PROGRESS (status, Next pointer, log) and one plan-mode-ready phase doc per phase. Use when the user wants a build plan, phases, a breakdown or a roadmap from a spec, or to re-slice phases that are not started.
argument-hint: "<initiative>"
---

# /phases — the spec, cut into sessions

Load `phase-kit:conventions`, `phase-kit:templates` and `phase-kit:phase-example` with the Skill
tool, then read the project's `CLAUDE.md` › Workflow and the spec `docs/specs/<initiative>.md` (no
argument → the Workflow section's default initiative; several specs → ask). The templates
`initiative-README`, `PROGRESS` and `phase` give the shape; the example gives the level of detail. This
skill writes documents only.

## Gate

- Spec `draft` → stop: "the spec has open questions; `/spec <initiative>` confirms it first".
- `docs/phases/<initiative>/` exists with phases past `specced` → re-slicing touches only the
  phases not started; say which ones stay as they are.

## Slice

1. Read the spec whole, then the code the initiative touches (an existing project) so the phases
   are cut along what exists, not along the spec's chapters.
2. Cut phases so that each: fits one focused session (a few hours of Claude's work; when in doubt,
   smaller); leaves the project working and the checks green; has a clear "done" the owner can
   see; depends only on earlier phases. Order by risk and by what the owner needs first. Keep the
   owner's manual steps together: within a phase, one stop at its end wherever the work allows.
   A new project starts with **Phase 0, Foundation**: the scaffold, the checks (lint, typecheck,
   tests, build), a first deploy or run, the Workflow section's `{{CHECKS}}` / `{{RUN}}` /
   `{{DEPLOY}}` lines filled in. Fewer is better: two to six phases is usual, one is fine, more
   than eight means the spec is two initiatives.
3. Propose the table (id, name, goal in one line, depends on, sessions) and the order **before
   writing the docs**; take the owner's changes. If any slicing choice is a real decision (what
   comes first, what is left out), it goes to `docs/DECISIONS.md`.

## Write

- `README.md` from its template: the calendar, the table, the order, the conventions the initiative
  adds to CLAUDE.md's rules.
- `PROGRESS.md` from its template: every phase `specced`, **Next** pointing at the first, a first
  log entry (today: phases written, the decisions taken).
- One `phase-<id>-<slug>.md` per phase from the template, **self-contained but short** (about 40–80
  lines; conventions, "Say it once"): a session that reads only this doc, PROGRESS and the spec
  sections it names can build the phase, so point at the spec instead of retelling it. **Read first**
  names the real files. Tasks are concrete and checkable. Out of scope names where each thing went.
  Acceptance criteria are the goal made checkable, each independently, each naming its proof (a test,
  a walk-through). "Your turn" is written for the owner, with exact commands and clicks wherever they
  are already known (conventions, "The owner's steps"). Handoff is empty.
- Phase ids `0`, `A`, `B`, …; slugs short and lower-case.

Then say, in plain words, what was written and that the next step is `/phase <initiative> 0`.
Commit `docs(phases): <initiative> phases` if there is a repository.
