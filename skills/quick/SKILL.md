---
name: quick
description: The quick lane — do a small change now, with no spec, phase doc, plan mode or handoff; the change, its test, the checks, a commit. Use only when the user types /quick, asks for the quick lane, or accepts an offer of it from another phase-kit skill. An ordinary request to fix or change something is not a reason to load this.
argument-hint: "<what to change>"
---

# /quick — small things, no ceremony

`/quick`, typed or accepted when offered, is the owner's go for this change: no plan mode, even where
`CLAUDE.md` says "no code before a plan" (that rule is about phases). Nothing to load, and no
`/kit-setup` needed. No argument → ask what to change, in one line.

## Is it quick?

Yes: a fix, a tweak, a copy change, a small feature with an obvious shape. Also yes: a bigger job that
still fits one sitting and is not on the list below. Show a plan of about ten lines in the chat first,
and wait for the owner's go.

No, so say why in a sentence and offer `/spec` or `/backlog` before touching anything:

- it changes how money moves, who gets mail, who can log in or see what, deletes data, or migrates
  a database;
- it needs a decision that other work will depend on;
- it belongs to a spec that is still `draft`;
- it will not fit in one sitting.

## Do it

1. **Read only the code the change touches**, plus `CLAUDE.md` for how to run the checks. Open a spec
   or phase doc only if the change belongs to one, and then only its status and the part concerned.
2. **One obvious way** → say it in a line and go. **A real choice** → one AskUserQuestion with a
   recommendation, then go.
3. **Where**: `git status` first. Uncommitted changes → stop: commit your own first; for anyone
   else's, propose a worktree. On a phase branch and the change belongs to that phase → stay. Workflow
   section says `main directly` → stay on the project's main line. Otherwise, or with no Workflow
   section and a main line that may deploy, make a `quick/<slug>` branch off the main line (the
   Workflow section's, else the repository's default branch).
4. **Build**: for a bug, a test that fails first; for new behaviour, a test with it; copy and
   styling need none. If the change is visible and the app is cheap to run, look at it. If you could
   not, say so.
5. **Commit**: the project's checks green, then one conventional commit, no AI attribution.

Pushing, merging and deploying still wait for the owner's word, like everywhere in the kit. So do
sending, publishing and deleting.

## The trail

Only what applies, one line each, in the same commit:

- `CHANGELOG.md`, if the project has one and a user would notice.
- The phase doc's Handoff, if this touches a phase in progress.
- `docs/DECISIONS.md` and the spec line, if the owner made a ruling or this changes what a confirmed
  spec promises.

No PROGRESS entry, no TODO, no board: a quick change does not move a phase.

## If it grows

More than a handful of files and no plan shown yet: stop and show the ten lines. More than one
question, more than about an hour in, or something risky comes up: stop. Say what you found, what is
done so far and whether to keep or undo it, and offer `/spec` or `/backlog`. Never turn a quick change
into a large one quietly. The third quick change in a row on the same thing is a phase: say so.

## Tell the owner

Two or three lines: what is different now, how to see it, the checks' result, and the word that
ships it ("merge it" where that deploys). Anything only they can do comes last and in full: where,
the exact command or clicks, what they should see. Then stop.
