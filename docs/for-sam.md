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
- **Two reviews** by a fresh subagent: the plan review, at the level the plan calls for (it began as
  batzen's review of risky phases), and a diff review once per phase (new; `/code-review` where it
  exists).
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
  phase, run at the start of the next session); small changes paid full ceremony (→ `/quick`); specs
  froze while the product moved (→ the spec is corrected in the handoff);
  the always-loaded docs grew unbounded (→ CLAUDE.md under 200 lines, PROGRESS archive).
- **Weaker, and left so on purpose**: serial by construction (no task graph, no parallel agents;
  worktrees exist when two independent phases must run at once); no automatic loop (Ralph-style
  retries suit mechanical tasks with automatic checks, not design decisions; a later option for a
  phase whose criteria are all executable); the human is the loop, which for a non-coder means
  the verify commands and the reviews carry the weight a code review would.

## What the first week of use said (30 Oct 2026)

Measured from the box's transcripts, across the kit sessions in six projects, counting active time
only (gaps over 3 minutes and waits on you left out). The stage clock runs until the next kit command,
so `/handoff` is an upper bound: it includes work done after the handoff.

| Stage | Active minutes, total | Median per session |
|---|---|---|
| The build itself | 327 | 47 |
| `/spec` | 126 | 14 |
| `/handoff` | 99 | 10 |
| `/phase` before the plan is approved | 67 | 9 |
| `/phases` | 43 | 8 |
| `/kit-setup` | 32 | 3 |

So a bit over half the active time went around the build rather than into it. The docs look like a
large part of that, though the numbers do not prove it: phase docs averaged 126–242 lines per project,
and one project's four phase docs reached 969 lines. The 238-line example set that size. Across all 39
phase docs the Session protocol, which mostly repeats `/phase`, ran to a median of 11 lines (up to 27),
and one approved Plan block sat beside the Tasks at 60 lines.

What 0.2.0 did about it: `/quick`, a shorter phase doc (Read first instead of Session protocol, Why
folded into Goal, Testing folded into the criteria), a trimmed example, "Say it once" with target sizes,
and the slow steps cut (no full checks before starting, no copy of the plan, templates loaded at
handoff only when a file is missing, one diff review per phase). Left alone on purpose: the gate on an
unconfirmed spec, plan approval before phase code, decisions, evidence, "Your turn".

Docs written before 0.2.0 keep their old sections; `/phase` reads both.

## What the plan review cost (31 Oct 2026)

Measured from the box's transcripts. Clock time from `/phase` to the first plan shown, waits on you
included:

| Sessions | Count | Minutes to the first plan |
|---|---|---|
| With a plan review | 5 | 30–43 |
| Without | 8 | 2–15, median 7 |

Those plans were also bigger, so the review is not the whole gap; on its own it ran 7 minutes on
28 Oct and 14–20 from 30 Oct, at about 300,000 tokens and 48–61 tool calls, and the session then has
to check and fold in a long report. The old trigger went by topic, so even a VAT line printed on an
invoice would count as "money".

It earns its wait where the plan touches something live. It found a rollback that would have stopped
at its first step, and a `craft up` that would have skipped a production migration on an Enter, with
exit 0. So 0.3.0 keeps it and scales it: none by default, light, or deep, by what a mistake would cost,
asking you with what is at stake when unsure.

Fresh sessions given five made-up phases picked as intended: a sort and the VAT line got none, a local
change behind a switch light, a payment cutover deep, and a customer data export asked first.

The light brief was replayed on one real plan, rotpunkt's Phase A as it stood before its deep review.
One plan, one run each, so these are indications:

| Run | Minutes | Tool calls | Tokens | Its 5 plan-changing findings, found |
|---|---|---|---|---|
| Deep, 28 Oct (the original) | 6.8 | 26 | 157,000 | all 5 |
| Light brief, session's model | 7.4 | 12 | 132,000 | 4 |
| Light brief, smaller model | 3.0 | 11 | 107,000 | 3 (one only named as unsettled), and not the top one |

Capping the reads alone saved no time on this plan. The smaller model is what made it fast, and it
costs findings, so light is a quick look for contained plans, and anything live gets deep.

Left alone on purpose: the diff review, which now costs about the same (10–22 minutes and
233,000–316,000 tokens on the seven project phases reviewed on 30 Oct, up from 2–7 minutes on 28 Oct).

## Using it in the existing repos

Each repo has its own `/phase` (k4, batzen as skills; selnau as a command). **A project skill with
the same name shadows the plugin's**, so nothing changes there until you decide to. Options:

1. Leave the repo's `/phase` and use only the kit's `/handoff`, `/progress`, `/backlog`, `/quick` there: run
   `/kit-setup`, which writes the Workflow section with the repo's real paths and nothing else.
2. Retire the repo's `/phase` in favour of the kit's: the repo-specific steps (k4's dev DB check,
   batzen's RLS choreography, selnau's locales) move into the Workflow section's Rules line or
   the initiative README's Conventions, which the kit's `/phase` reads.

I would do 1 now and 2 per repo when its next initiative starts.

## Installing it for yourself

On the box it is installed at user scope from the local clone (`~/code/phase-kit`), so every
project's Remote Control session has it. Sessions run a **cached copy**
(`~/.claude/plugins/cache/pompom/phase-kit/<version>`), not the working tree: edits reach them only
after a version bump and `claude plugin update phase-kit@pompom`, and only in sessions started after
that. On the Mac:

```bash
claude plugin marketplace add PomPomLtd/phase-kit
claude plugin install phase-kit@pompom --scope user
```

Updates: `claude plugin update phase-kit@pompom` (or turn auto-update on under `/plugin` ›
Marketplaces).

## Releasing

Bump `version` in `.claude-plugin/plugin.json` and add the `CHANGELOG.md` entry; `claude plugin
validate --strict .` before pushing. Marketplace installs update to the new version on
`claude plugin update phase-kit@pompom` (the version must change for an update to be seen). The
box's install is a local-path marketplace, so there `git pull` plus the same update command.
