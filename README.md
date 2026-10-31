# Phase kit

A way of building software with Claude Code that does not fall apart after the third session.

You write down **what** you want (a spec). Claude cuts it into **phases** that each fit one
session. Each session builds **one phase**: Claude reads the plan, proposes how, you approve, it
builds and tests, and it leaves a **handoff** so the next session can start cold. Everything that
was decided is written down with a number. Nothing is built from a spec you have not confirmed.

Small things skip all of that: `/quick` makes the change, tests it and commits it.

It is a Claude Code plugin: eight slash commands plus the document templates they use. It works in
the Claude Code desktop app, in the terminal and in the VS Code extension. It was distilled from
three real projects at Pom Pom GmbH in Zurich.

## Install

Once, in any Claude Code session (type these in the prompt box, one after the other):

```
/plugin marketplace add PomPomLtd/phase-kit
/plugin install phase-kit@pompom
```

Choose **user** scope when asked, so it is on in every project. In the desktop app you can also
click **+** next to the prompt box › **Add plugins**, add the marketplace `PomPomLtd/phase-kit`
and install **phase-kit** from it. Updates: `/plugin update phase-kit@pompom`, or switch
auto-update on under `/plugin` › Marketplaces.

Then, in a project (new or old): `/kit-setup`.

## The loop

```
/spec v1        write the spec with Claude, as an interview        → docs/specs/v1.md (draft)
/spec v1        answer the open questions                           → confirmed
/phases v1      cut it into phases                                  → docs/phases/v1/…
/phase v1 0     build Phase 0 in this session                       → built, or "awaiting you"
/handoff        close the session (docs, commit, note)
                … you check what only you can (Your turn) and say so → verified
/phase v1 A     next session, next phase
/progress       any time: where are we, what's next, what's waiting on me
/backlog …      park an idea instead of building it now
```

One session = one phase. When a session gets long, say `/handoff`; the next one reads the docs,
not the old conversation.

## The quick lane

```
/quick the date on the news page is wrong
```

For a fix, a tweak or a small feature with an obvious shape. Claude makes the change with its test,
runs the checks and commits. There is no spec, no phase doc, no plan to approve and no handoff, and
it works in a project that never ran `/kit-setup`. A bigger job that still fits one sitting works too:
Claude shows a plan of about ten lines in the chat and waits for your go. If the job turns out bigger
than it looked, or
changes how money moves, who gets mail or who can log in, or deletes data, Claude stops and says so.
Deploying still waits for your word. It only runs when you ask for it: an ordinary "fix this" gets
Claude's normal behaviour.

## Where we stand

`/progress`, the start of every `/phase` and every `/handoff` show the whole project, and during a
phase you get a one-line update after each finished task:

```
shop: about 40% done (≈4 of 10 sessions)

0  Foundation            ████████████  verified
A  Product pages         ████████████  awaiting you (one look on a phone)
B  Cart and checkout     █████░░░░░░░  in progress (9 of 21)
C  Payments              ░░░░░░░░░░░░
D  Order mails           ░░░░░░░░░░░░
E  Go-live               ░░░░░░░░░░░░

Next: B, the empty cart. Waiting on you: one look.
```

A phase in progress fills up as its checklist is ticked. The % counts each phase by how many sessions
it is expected to take, so it tracks the work left rather than the rows on the board.

## What the files are for

| File | What it holds |
|---|---|
| `docs/specs/<name>.md` | What and why. `draft` until you answered every open question; then `confirmed`. |
| `docs/phases/<name>/README.md` | The phase table and the order. |
| `docs/phases/<name>/PROGRESS.md` | Status per phase, what is next, the session log. The first thing a session reads. |
| `docs/phases/<name>/phase-A-….md` | One phase: the plan in front, the handoff at the back. |
| `docs/DECISIONS.md` | Every ruling, numbered `D-001`, `D-002`…, with who, when and why. |
| `docs/BACKLOG.md` | Ideas for later, each with what would make it worth doing. |
| `docs/TODO-<You>.md` | What only you can do: look at the app, create an account, decide something. |
| `CHANGELOG.md` | What shipped, in plain words. |
| `CLAUDE.md` › Workflow | The project's rules: who the owner is, where things are, how it is checked, run and deployed. |

Statuses of a phase: `specced` → `in progress` → `built` → `awaiting <you>` → `verified`
(or `parked`). Claude never marks a phase `verified` while something waits on you.

## How to be a good owner

- **Answer the questions.** Claude asks in batches, with options and a recommendation. Picking the
  recommendation is a fine answer. "I don't know, you choose" is also an answer; it gets written
  down as a decision all the same.
- **Read the plan before saying yes.** It is written for you: what you will see at the end, then
  the steps. If it is not what you meant, say so there; it is cheap before code.
- **Approving a plan is not approving a deploy.** Claude never deploys, sends mail, publishes,
  deletes data or spends money without you saying so in the session. "Merge it" is the go to
  put a finished phase live, where the project deploys from `main`.
- **Do your turn.** Claude does everything it can first, then gives you all your steps at once, in the
  message itself: where to run it, the exact command or clicks, what you should see. Tell Claude what
  you saw. That is how a phase becomes `verified`.
- **One phase per session.** Resist "and while you're at it". Say `/backlog the thing` instead, or
  `/quick the thing` when it is small.
- **Start sessions with `/progress`.** It reads the docs and proposes the one command to run.

## Words you will meet

- **Plan mode**: Claude only reads and thinks, and shows you a plan; nothing is changed until you
  approve it. The kit makes Claude enter it before any build.
- **Reviews**: a second Claude, knowing nothing of the session, looks for what the plan gets wrong
  before any code is written, and for bugs in the code once it is. Most phases do not need one, and
  their plans come straight to you. Claude picks a level by what a mistake would cost: none, light (3–5
  minutes each) or deep (15–20 minutes each). It says which and why, and asks you, with what is at
  stake, when it is unsure. "Skip the review" or "do a deep one" overrules it.
- **Checks**: the project's own tests and build steps. They must pass before every commit; Claude
  runs them, you do not have to.
- **Commit**: a saved snapshot of the project with a message. **Branch**: a separate line of
  snapshots for one phase, merged into `main` when you say so. **Pull request**: the branch
  offered for merging on GitHub, with the handoff as its description. `/kit-setup` picks the
  simplest arrangement that fits your project.
- **Evidence**: a ticked acceptance criterion names what proved it (a test, a screenshot, a
  command's output).

## For the curious

- Everything is in `skills/`, one folder each: the eight commands, and three hidden ones the
  commands load (`conventions`, the shared vocabulary; `templates`, the documents; `phase-example`,
  a real, filled-in phase doc).
- `docs/for-sam.md` says where each idea came from and how to use the kit in a project that already
  has its own way of doing this.
- Licence: MIT.
