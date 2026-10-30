---
name: templates
description: The phase-kit document templates (the CLAUDE.md Workflow section, spec, initiative README, PROGRESS, phase doc, DECISIONS, BACKLOG, the owner's TODO, CHANGELOG) with their {{placeholders}}. Loaded by kit-setup, spec, phases and handoff; not for direct use.
user-invocable: false
---

# phase-kit templates

Each block below is one file. Placeholders: `{{OWNER}}` (first name), `{{OWNER_UPPER}}`,
`{{DATE}}` (today, `YYYY-MM-DD`), `{{INITIATIVE}}` (slug), `{{TITLE}}`, `{{ID}}`, and in the
CLAUDE.md section `{{CHECKS}}`, `{{RUN}}`, `{{DEPLOY}}`, `{{BRANCHING}}`. Lines in parentheses
or starting with "…" are guidance to replace, not text to keep. Write the file with every
placeholder filled; never leave a `{{…}}` behind.

## `claude-md-section` → appended to `CLAUDE.md`

```markdown
## Workflow (phase-kit)

This project is built in phases from written specs with the phase-kit skills: `/spec` (write or
revise a spec with the owner), `/phases` (slice a confirmed spec into one-session phase docs),
`/phase <id>` (one build session = one phase), `/handoff` (close a session), `/progress` (where
things stand), `/backlog` (park an idea), `/quick` (a small change now, without the above). The
vocabulary (statuses, decision ids, what each file is for) is the kit's `conventions` skill.

- **Owner**: {{OWNER}} — decides, signs off, and does what only a person can (the "Your turn"
  lists). Never deploy, send, publish or delete without the owner's explicit go in the session.
- **Where things are**: specs `docs/specs/<initiative>.md`; phases `docs/phases/<initiative>/`
  (`README.md` the phase table, `PROGRESS.md` status + log, `phase-<id>-<slug>.md` one per
  phase); decisions `docs/DECISIONS.md`; parked ideas `docs/BACKLOG.md`; the owner's to-dos
  `docs/TODO-{{OWNER_UPPER}}.md`; what shipped `CHANGELOG.md`.
- **Default initiative**: `{{INITIATIVE}}` (used when a command names none).
- **Checks**, green before every commit and at every close-out: {{CHECKS}}
- **Running the app**: {{RUN}}
- **Deploy**: {{DEPLOY}}
- **Branches**: {{BRANCHING}}
- **Rules**: no phase code before a plan the owner approved (plan mode; `/quick` is the exception);
  a build session works on one phase and stays inside its "Out of scope"; every behaviour ships with
  its test in the same change; after three failed attempts at one thing, stop and reassess; small conventional commits
  (`feat(area): …`), no AI attribution; a spec that is still `draft` is never built.
```

## `spec` → `docs/specs/<initiative>.md`

```markdown
# {{TITLE}} — Specification

**Status**: draft · **Owner**: {{OWNER}} · **Written**: {{DATE}}
**Phases**: `docs/phases/{{INITIATIVE}}/` (written by `/phases` once this spec is confirmed).
**Parents**: (other specs this one builds on, or "none")

_A spec is `draft` until the owner has answered every open question (§11) and said so; then it
is `confirmed` and may be sliced into phases. It describes what the thing does and why, not how
the code is laid out — that is the phase docs' job. Decisions taken while writing it are listed
in §3 with their `D-…` ids from `docs/DECISIONS.md`. A section that does not apply is left out;
the others keep their numbers._

## 1. Motivation

What problem this solves, for whom, and what happens if it is not built. One or two paragraphs.
Name the trigger (a request, a complaint, a deadline).

## 2. Glossary

| Term | Meaning |
|---|---|
| | |

## 3. Decisions ({{OWNER}}, {{DATE}})

Numbered, dated, attributed. Each one is also an entry in `docs/DECISIONS.md`.

- **D-001** — …

## 4. Goals and non-goals

**Goals** (what must be true when this is done):

1. …

**Non-goals** (explicitly not this initiative; where they went, e.g. "BACKLOG"):

- …

## 5. Who uses it and how

The people (or systems) involved and what each one does. For every kind of user: what they see,
what they can do, what they must never be able to do.

## 6. How it works

The behaviour, flow by flow. Each flow: the starting point, the steps, what the user sees at the
end, the ways it can go wrong and what happens then. Edge cases live here, not in a footnote.

## 7. Data

What is stored, what the fields mean, what is derived, what must never be stored. (Tables and
columns only if the project has a database; otherwise files, settings, whatever holds state.)

## 8. Screens and messages

Every page, screen, mail or message the user meets, in words: what is on it, what the buttons
do, the empty state, the error state. A sketch only where the layout is not obvious.

## 9. Stack, hosting and tools

What it is built with and where it runs, with the reason. For a new project: the recommended
stack and the alternatives considered. For an existing one: what this initiative adds.

## 10. Risks

| Risk | What it would break | What we do about it |
|---|---|---|
| | | |

## 11. Open questions

_Must be empty (every line struck through with its answer and `D-…` id) before the status
becomes `confirmed`._

1. **Question.** Options: (a) …, (b) …. _Recommendation_: (a), because ….
```

## `initiative-README` → `docs/phases/<initiative>/README.md`

```markdown
# {{TITLE}} — phases

Build plan for **{{TITLE}}**. Parent spec: [`../../specs/{{INITIATIVE}}.md`](../../specs/{{INITIATIVE}}.md)
(the `§` references below point there). Status and the session log live in
[`PROGRESS.md`](PROGRESS.md). Run a phase with `/phase {{INITIATIVE}} <id>`.

Each phase file is self-contained and plan-mode-ready: Status, Prerequisites, Read first, Goal,
Tasks, Out of scope, Acceptance criteria, Your turn, Handoff. **One session = one phase.** Every
phase leaves the project working and the checks green.

## Calendar

(Dates that matter: a launch, an event, a deadline. "None" is a fine answer.)

## Phases

| Phase | File | Goal | Depends on | Sessions |
|---|---|---|---|---|
| 0 | [`phase-0-foundation.md`](phase-0-foundation.md) | … | — | 1 |
| A | [`phase-a-….md`](phase-a-….md) | … | 0 | 1 |

Order: 0 → A → B → …. (Say which phases could run in either order, and why a phase waits.)

## Conventions (apply to every phase)

- The project's rules are in `CLAUDE.md` › Workflow; the kit's vocabulary is its `conventions`
  skill.
- (Anything this initiative adds: a design system to follow, a service that must never be
  called from tests, a data rule.)
```

## `PROGRESS` → `docs/phases/<initiative>/PROGRESS.md`

```markdown
# Progress — {{TITLE}}

Single source of truth for build status. Every phase session updates this file (the row, and a log
entry of up to three lines, newest first) before it ends. Spec:
[`../../specs/{{INITIATIVE}}.md`](../../specs/{{INITIATIVE}}.md).

**Next**: Phase 0 (nothing started). (One sentence.)

| Phase | Name | Doc | Status |
|---|---|---|---|
| 0 | … | [phase-0-….md](phase-0-….md) | specced |

Statuses: `specced` · `in progress` · `built` · `awaiting {{OWNER}}` · `verified` · `parked`
(the kit's `conventions` skill).

## Open items

- (Things that are nobody's phase yet but must not be forgotten; each with a date and who said it.)

## Log

### {{DATE}}: phases written

(What the session did, in up to three lines, with a link to the phase doc's Handoff. Newest entry
first.)
```

## `phase` → `docs/phases/<initiative>/phase-<id>-<slug>.md`

```markdown
# Phase {{ID}}: {{TITLE}}

**Status**: specced {{DATE}}.
**Prerequisites**: (phases that must be `built` or `verified` first; anything the owner must
have done or provided, e.g. an account, a decision, a file).
**Expected sessions**: 1.
**Calendar**: (a date this phase must meet; leave the line out if none).
**Verify command**: (one command that fails when this phase's promise is broken; "to be added as
the first task" until it exists).
**Read first**: (the spec sections it implements, and the code it touches: real files or folders,
or "the project is empty").

## Goal

One paragraph: what works at the end of this phase that did not before, seen from the outside, and
why it matters. The rest of the why stays in the spec.

## Tasks

- [ ] …
- [ ] …

## Out of scope

- (What this phase deliberately leaves alone, and where it went: a later phase, BACKLOG.)

## Acceptance criteria

What must be true, each with how it will be proved (a test by name, a walk-through, a command).
Tick each with its evidence, in a line.

- [ ] …
- [ ] The project's checks are green (CLAUDE.md › Workflow › Checks).

## Your turn

What only the owner can check or decide, as a list they can follow without reading code: what
to open, what to press, what they should see, what to tell Claude afterwards. "Nothing" is fine.

1. …

## Handoff

(Written at the end of the session, about 10–20 lines: what was built and where it lives, what was
decided, what was left out, what the next session must know. Not a retelling of the diff. Newest
session first if there are several.)
```

## `DECISIONS` → `docs/DECISIONS.md`

```markdown
# Decisions

Rulings that shape the project, numbered `D-001`, `D-002`, … in the order they were taken.
Newest first. Each entry says who decided, when, what the alternatives were and why this one won.
A decision is changed by a new entry that names the old one, never by editing the old one.
Specs, phase docs, commits and the changelog cite the ids.

## D-001 — (title) · {{OWNER}}, {{DATE}} · {{INITIATIVE}}

**Decided**: …
**Instead of**: …
**Because**: …
```

## `BACKLOG` → `docs/BACKLOG.md`

```markdown
# Backlog

Ideas and requests that belong to no phase yet. Each entry says where it came from, why it
matters, what would make it worth doing, and a first take on the shape. Promote an entry into a
spec or a phase doc when it is scheduled; strike it through (`~~title~~ — done in phase X`) when
done. Newest first.

## (Title)

**From**: who, when, in which context.
**Why**: what it would improve, for whom.
**Revival trigger**: the event that makes this worth doing (a user asking twice, a number
crossing a line, another phase landing).
**Shape**: a first idea of the work, in a few lines.
```

## `TODO-OWNER` → `docs/TODO-<OWNER>.md`

```markdown
# To do — {{OWNER}}

Things only {{OWNER}} can do: sign-offs ("Your turn" lists), accounts to create, keys to paste,
decisions to take, things to look at in the real app. Newest section first. Done items stay,
struck through, with a note of what happened.

## From {{DATE}} — setup

1. …
```

## `CHANGELOG` → `CHANGELOG.md`

```markdown
# Changelog

What shipped, newest first, one entry per session that changed the product, written for a reader
who does not read code: what is different now, and why. The session story lives in
`docs/phases/<initiative>/PROGRESS.md`, the decisions in `docs/DECISIONS.md`.

## {{DATE}} — the workflow set up

phase-kit's docs added (`docs/`, `CHANGELOG.md`, the Workflow section of `CLAUDE.md`). Nothing
in the product changed.
```
