---
name: kit-setup
description: Set up the phase-kit workflow in a project, new or existing. Creates docs/ (specs, phases, DECISIONS, BACKLOG, the owner's TODO), CHANGELOG.md and the "Workflow (phase-kit)" section of CLAUDE.md, adapting to specs and phase docs the project already has. Use when the user asks to set up, install or adopt the workflow, or when another phase-kit skill finds no Workflow section in CLAUDE.md.
argument-hint: "[owner name]"
---

# /kit-setup — the workflow in this project

Goal: after this, `/spec`, `/phases`, `/phase`, `/handoff`, `/progress` and `/backlog` know where
everything is, and the owner knows what happened. Load `phase-kit:conventions` and
`phase-kit:templates` with the Skill tool first.

## 1. Look before writing

- Is this a git repository (`git status`)? If not, say that the kit works without git but nothing
  gets committed, and offer `git init` (do it only on a yes).
- Does `CLAUDE.md` exist, and does it already hold a **Workflow (phase-kit)** section? If it does,
  this is an update: show what would change and stop unless the owner wants it.
- Does the project already run a spec/phase workflow in its own layout? Look for `_SPECS/`,
  `docs/SPEC.md`, `docs/specs/`, `docs/phases/`, `docs/KICKOFF.md`, `PROGRESS.md` anywhere,
  `IMPLEMENTATION_PLAN.md`, `DECISIONS.md`, `BACKLOG.md`, `.claude/skills/phase`,
  `.claude/commands/phase.md`. **Never move or rename existing docs.** The Workflow section records
  the paths as they are, and the kit's skills follow it. A project-level `/phase` skill or command
  of its own wins over the plugin's; say so and leave it alone.
- How is the project run and checked? Read `package.json` scripts (or the Makefile, `pyproject`,
  whatever is there) for lint, typecheck, test, build, dev. An empty folder has none yet: the
  checks line then says "none yet; Phase 0 adds them".
- Who is the owner? The argument, else `git config user.name`, else ask. One first name is enough.

## 2. Ask what you cannot read

One AskUserQuestion round at most, with recommendations. Typical questions: the owner's name if
unknown; the default initiative slug (recommend `v1` for a new project, the obvious name for a
feature); how the app is run and deployed if nothing says it; whether to `git init`.

## 3. Write

For a project without its own layout, create (skipping any file that exists):

- `docs/specs/` (empty, with no placeholder file), `docs/phases/`.
- `docs/DECISIONS.md`, `docs/BACKLOG.md`, `docs/TODO-<OWNER>.md`, `CHANGELOG.md` from the
  templates, with `{{OWNER}}`, `{{OWNER_UPPER}}`, `{{DATE}}` (today, `YYYY-MM-DD`) and
  `{{INITIATIVE}}` filled in. An existing `CHANGELOG.md` is left alone.
- `CLAUDE.md`: append the Workflow section from the `claude-md-section` template with every
  `{{…}}` filled: `{{CHECKS}}` as the commands in the order lint → typecheck → test → build;
  `{{RUN}}` the dev command and address; `{{DEPLOY}}` how and who says go (unknown → "not set up
  yet; decided in Phase 0"); `{{BRANCHING}}` one of `main directly`, `phase branches, merged on
  the owner's go`, `phase branches and pull requests` (recommend phase branches wherever a push
  to `main` deploys; pull requests where the project is on GitHub and `gh` is installed; see the
  conventions' "Branches, reviews and pull requests"). No `CLAUDE.md` → create one with a one-line project description on
  top and the section. Keep the whole file under 200 lines; the section is the rules, not a
  tutorial.

For a project with its own layout, write only the Workflow section, with the real paths (for
example specs `_SPECS/<name>-spec.md`, phases `_SPECS/tasks/<initiative>/`), and create only the
files that are missing and wanted (ask).

## 4. Say what happened

In plain words: the files made, the section added, what the owner should do next. For a new
project that is `/spec <initiative>`; for a project that already has docs it is `/progress`.
Commit as `docs: set up the phase-kit workflow` if there is a repository, after showing the diff
of `CLAUDE.md` when it existed before.
