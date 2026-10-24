# Notes for Sam

What the kit took from each project, what it changes, and how to use it in the existing repos.

## Where it comes from

| Idea | From |
|---|---|
| One folder per initiative: README (table), PROGRESS (status + log), one doc per phase; the phase doc's sections; one session = one phase; the Handoff at the back of the doc | k4-swisssystem |
| A hard gate on an unconfirmed spec; spec sessions as interviews with batched AskUserQuestion and recommendations; numbered decisions cited everywhere; a TODO file for the owner; revival triggers on backlog entries; adversarial plan reviews on risky work; worktree rule, no bare stash | batzen |
| Status vs sign-off as two states (`built` vs `awaiting <Owner>` vs `verified`); evidence on every acceptance criterion; "plan once, resume many" for multi-session phases; every doc says its own role; BACKLOG with From / Why / Opinion | selnau |
| Spec interview, plan mode first, "give Claude a check it can run", CLAUDE.md under 200 lines | Claude Code docs, best practices |

## What is different from the three repos

- **Paths are a setting**, not a convention: `CLAUDE.md` › Workflow (phase-kit) names where specs,
  phases, decisions, backlog, the owner's TODO and the changelog live. New projects get `docs/…`;
  k4 keeps `_SPECS/`, batzen `docs/specs/phase-N/`, selnau `docs/phases/` as they are.
- **Decisions are one sequence per project** (`D-001`…) in `docs/DECISIONS.md`, instead of
  batzen's `D-14.1-a` or k4's `D1…D19` inside each spec. Simpler to cite, no renumbering.
- **"Your turn" replaces "Manual stop" / "Sam verification"**, written for someone who does not
  read code. Same thing.
- **Branching is a setting** (`main directly` / phase branches / pull requests) and the handoff
  does the branch, push and pull request; the merge stays the owner's go.
- **Two reviews** by a fresh subagent: the plan review on risky phases (batzen) and a diff review
  before every handoff (new; `/code-review` where it exists).
- **A PROGRESS archive rule** (over ~400 lines, oldest entries move out): k4's classical PROGRESS
  is near 1,000 lines and batzen's was split by hand.
- **`/handoff` is its own command**, so a session that ran long can still close properly; k4's
  Step 5 assumed the phase finished in one go.
- **`/progress`** is the morning command; `/backlog` the "park it" command.
- **No hooks**: nothing fires without being asked, so nothing to debug from the desktop app.

## What the research said (24 Oct 2026)

A research pass compared the workflow with GitHub's spec-kit, AWS Kiro, BMAD, Ralph loops,
Anthropic's best practices and long-running-agent harness, OpenAI's harness engineering, Cursor's
plan mode, memory banks and PRPs. The short version:

- **Shared with the consensus**: a spec from an interview before code; one bounded unit per fresh
  context; state in versioned markdown in the repo; plan approval before edits; a progress file
  the next session reads first. Anthropic's own docs describe nearly this loop.
- **Stronger than the field**: numbered decisions and an open-questions list that must be empty
  before building (the one controlled study on this, SpecFirst, found resolving ambiguity before
  synthesis lifts pass rates by 7 to 21 points); an "Out of scope" fence per phase (unwanted
  features are a top reason agent PRs are not merged); evidence on criteria; it is proven on live
  products, where spec-kit and Ralph say they are greenfield tools; less ceremony than spec-kit,
  Kiro or BMAD, which Thoughtworks rates "Assess" for heavy spec files.
- **Weaker, and now addressed in the kit**: the builder graded its own work (→ the diff review by a
  fresh subagent at handoff); nothing executable proved a phase still works (→ a verify command per
  phase, run at the start of the next session); small changes paid full ceremony (→ "small changes
  are not phases"); specs froze while the product moved (→ the spec is corrected in the handoff);
  the always-loaded docs grew unbounded (→ CLAUDE.md under 200 lines, PROGRESS archive).
- **Weaker, and left so on purpose**: serial by construction (no task graph, no parallel agents;
  worktrees exist when two independent phases must run at once); no automatic loop (Ralph-style
  retries suit mechanical tasks with automatic checks, not design decisions; a later option for a
  phase whose criteria are all executable); the human is the loop, which for a non-coder means
  the verify commands and the reviews carry the weight a code review would.

## Using it in the existing repos

Each repo has its own `/phase` (k4, batzen as skills; selnau as a command). **A project skill with
the same name shadows the plugin's**, so nothing changes there until you decide to. Options:

1. Leave the repo's `/phase` and use only the kit's `/handoff`, `/progress`, `/backlog` there: run
   `/kit-setup`, which writes the Workflow section with the repo's real paths and nothing else.
2. Retire the repo's `/phase` in favour of the kit's: the repo-specific steps (k4's dev DB check,
   batzen's RLS choreography, selnau's locales) move into the Workflow section's Rules line or
   the initiative README's Conventions, which the kit's `/phase` reads.

I would do 1 now and 2 per repo when its next initiative starts.

## Installing it for yourself

On the box it is installed at user scope from the local clone (`~/code/phase-kit`), so every
project's Remote Control session has it. On the Mac:

```bash
claude plugin marketplace add PomPomLtd/phase-kit
claude plugin install phase-kit@pompom --scope user
```

Updates: `claude plugin update phase-kit@pompom` (or turn auto-update on under `/plugin` ›
Marketplaces). The box's install tracks the clone: `git pull` there is the update.

## Releasing

Bump `version` in `.claude-plugin/plugin.json` and add the `CHANGELOG.md` entry; `claude plugin
validate --strict .` before pushing. Marketplace installs update to the new version on
`claude plugin update phase-kit@pompom` (the version must change for an update to be seen). The
box's install is a local-path marketplace, so there `git pull` plus the same update command.
