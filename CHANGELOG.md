# Changelog

## 0.2.0 — 2026-10-30

From the first week on real projects, where the kit felt complicated and slow.

- **`/quick`, the quick lane**: a small change with its test and a commit, without a spec, phase doc,
  plan mode or handoff. It stops and says so when the job turns out bigger or risky.
- **The board**: the whole project with a bar per phase and an approximate % done, at the start of
  every `/phase`, whenever a phase stops to wait on the owner, at `/handoff` and in `/progress`, plus
  a one-line update after each finished task.
- **Shorter docs**: a phase doc aims for 40–80 lines (Read first replaces Session protocol, Why folds
  into Goal, Testing into the criteria), the example shrank from 238 lines to about 90, and a new "Say
  it once" rule sets target sizes. Older docs keep working.
- **Faster sessions**: `/phase` runs only the last phase's verify command before starting, and its
  plan says what differs from the Tasks instead of restating them. `/handoff` updates only the docs
  that have something to add and reuses a green check. The diff review runs once per phase. The owner
  can say "keep going" into the next phase.
- The jokes are gone from the README.

## 0.1.0 — 2026-10-24

First version: the seven skills (`kit-setup`, `spec`, `phases`, `phase`, `handoff`, `progress`,
`backlog`), the templates, the conventions, one example phase doc, the guide. Distilled from the
workflow of three Pom Pom projects (k4-swisssystem, batzen, selnau).
