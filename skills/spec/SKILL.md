---
name: spec
description: Write or revise a specification with the owner, as an interview — motivation, decisions, goals, flows, data, screens, stack, risks, open questions — and save it as docs/specs/<initiative>.md, draft until every question is answered. Use when the user wants to plan, spec, think through or describe something to build, before any phases or code exist; also to confirm a draft spec.
argument-hint: "<initiative> [what it is about]"
---

# /spec — the what and the why, written down

Load `phase-kit:conventions` and `phase-kit:templates` with the Skill tool, then read the project's
`CLAUDE.md` › Workflow (no section → run `/kit-setup` first, with the owner's yes). The spec's
shape is the `spec` template. A spec session writes documents only: no code, no
scaffolding, no installs.

## Which spec

The first word of the argument is the initiative slug (`v1`, `shop`); the rest is the topic. No
argument → list the specs that exist with their status and ask which one, or whether this is a
new one. A spec that exists and is `draft` → the **confirmation** below. `confirmed` → a
revision: changes are new decisions, and a change that invalidates built phases is said out loud.

## Writing a new spec

1. **Orient.** Read the existing specs, `docs/DECISIONS.md`, `docs/BACKLOG.md` and, for an existing
   project, the code the initiative touches (use subagents for a wide look; read the key files
   yourself). Research what the spec needs (a library, a service, a rule) and say what you found;
   verify against current docs, not memory.
2. **Interview.** Keep asking until the sections of the template can be filled. Batch related
   questions, three or four at a time, each with concrete options and a recommendation
   (AskUserQuestion). Ask about: who it is for and what they do today; what "done" looks like;
   what must never happen; data and privacy; the screens; what already exists; money, mail,
   accounts and other things that touch the world; dates. Stop interviewing when a new question
   would not change the build.
3. **Write** `docs/specs/<initiative>.md` from the template, status `draft`, every section filled
   or marked "none" with the reason. Decisions the owner took in the interview go into §3 **and**
   `docs/DECISIONS.md` (next free `D-` number, newest first). What the owner declined goes to
   §4 non-goals or `docs/BACKLOG.md`. Questions that are still open go to §11 with options and a
   recommendation. Write for the owner: plain words, the user's view, no code.
4. **Show it.** Summarise the spec in a few sentences, name the open questions, and say what
   comes next: answering them (the confirmation), then `/phases <initiative>`.
5. Commit `docs(spec): <initiative> draft` if there is a repository. PROGRESS does not exist yet
   for a new initiative; nothing else to update.

## Confirmation (a `draft` spec)

Walk the owner through §11, question by question, options and recommendation each, through
AskUserQuestion. Each answer: strike the question through in §11 with the answer, add the decision
to §3 and `docs/DECISIONS.md`. When §11 holds no open line: status `confirmed`, with the date; say
that `/phases <initiative>` is next. Never flip a spec to `confirmed` with a question still open,
and never answer a question for the owner.

## A spec for an empty project

§9 (stack, hosting, tools) carries the recommendation and why: something boring, well documented,
with a free tier, that Claude Code builds well, and that gives the owner a running app at the end of
Phase 0. The owner decides; the choice is a decision. Phase 0 (`/phases` adds it) is then the
scaffold, the checks and a first deploy.
