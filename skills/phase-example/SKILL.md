---
name: phase-example
description: A real, filled-in phase doc from a Pom Pom project (a chess tournament app), as the example of how much detail a phase doc carries before and after its session. Loaded by the phases skill; not for direct use.
user-invocable: false
---

# A phase doc, filled in

From a real project. The project's own file names and rules are not the point; the shape and
the level of detail are: Why, Session protocol naming real files, Tasks that are checkable, Out
of scope saying where things went, Testing by layer, Acceptance criteria ticked with evidence,
Your turn for the owner, and a Handoff the next session can start from.

---

# Phase J: add a late pairing to a round in play

**Status**: Specced and **built 14 Oct 2026** on `feat/late-pairing` (off `main`; no migration), one
session on the claude-box, gates green; **on `main` and deployed 14 Oct** (merge `0403833`, on Sam's
word). The two late approvals and their pairing are Sam's (Manual stop).
**Prerequisites**: A–I on production (Season 3 round 1 in play since Mon 12 Oct).
**Expected sessions**: 1. Branch `feat/late-pairing` off `main` (with the backlog note).
**Calendar**: round 1 runs to Tue 20 Oct (results due Wed 21 Oct, 12:00). The two late
registrations of 13 Oct can only play round 1 if this is on production by about Thu 15 Oct;
otherwise it serves the next late entry. No migration, so the merge is a plain push.

## Why (Sam, 13 Oct)

Two registrations came in on 13 Oct with round 1 in play. A late approval today gets the next
seed and nothing for the round in play: catch-up ½-point byes cover **completed** rounds only
(`awardMissedRoundByes`), and nothing adds a board to a confirmed round (swap and flip end at
Confirm; Regenerate only works while the round is PAIRED, and would reshuffle everyone). So the
two late players sit out round 1 and take ½ point each, when they could play each other.

Until this exists: approve them now and award their round-1 ½-point byes from the Players tab
once round 1 is completed (or approve them after it completes).

## Session protocol (read first)

1. Read [PROGRESS.md](PROGRESS.md), this file, and the code it names:
   - `src/lib/actions/rounds.ts`: `generateRoundPairings` (how games and byes are written, board
     numbers, the pairing history), `confirmPairings`, `completeRound` (the round lock).
   - `src/lib/actions/league-rounds.ts`: `publishPairings` (the build → claim → send → record
     path, `resend: "unsent"`), `getPublishStatus`.
   - `src/lib/league-rounds/publish.ts`: `buildPairingNotices` (`unpaired`), `publishPreflight`,
     `deliveryState`.
   - `src/lib/pairing/colors.ts` and `constraints.ts`: `assignColors` (the first-round rule when
     neither player has a colour history), `canPair` (A1–A3).
   - `src/lib/actions/approvals.ts` `approveRegistration` and `entry-helpers.ts`
     `awardMissedRoundByes`.
   - `src/lib/actions/game-locks.ts`, `broadcast-push.ts` `scheduleBroadcastPush`.
   - `src/components/admin/league-round-panel.tsx` (the Publish block's preflight already names
     "Not in the pairings (entered after pairing)").
2. **Enter plan mode.** Plan this phase only; get Sam's approval before code. UI through the
   `frontend-design` skill in the K4 system, mobile first.
3. On completion: this doc's Status, Acceptance and Handoff, [PROGRESS.md](PROGRESS.md),
   `CHANGELOG.md`, `CLAUDE.md` (the Classical League notes and the test counts).

## Goals

On a Swiss league round in progress, the organiser pairs two entered players who have no game
in it against each other on a new board. Colours come from the pairing engine. If the pairings
are published, both players get their pairing mail with their own link, the board goes to
Lichess, and the round's delivery count includes them. Anything else is refused with a sentence.

## Tasks

- **Pure** `src/lib/league-rounds/late-pairing.ts`, with unit tests:
  - `latePairingHistory(tournamentPlayerId, games)`: colour history and opponents from the
    completed rounds' games, as the generator builds them.
  - `planLatePairing(input)` returns `{ board, whiteId, blackId, moveByes }` or a refusal
    sentence:
    - two different entries, both ACTIVE in the event, neither with a game (or a bye) in the
      round;
    - `canPair`: no rematch, colours legal;
    - colours by `assignColors`;
    - the board right after the last game, with a pairing-allocated bye on that number moved
      one down so it stays last.
  - `latePairingCandidates(notices)`: the ACTIVE entries without a game (the preflight's
    `unpaired`).
- **Actions** in `src/lib/actions/league-rounds.ts` (`requireAdmin()`):
  - `previewLatePairing(roundId, aId, bId)`: the plan, read-only, for the dialog.
  - `addLatePairing(roundId, aId, bId)`:
    - under the round lock: the round still IN_PROGRESS, a scheduled round of an ACTIVE Swiss
      event, both still without a game;
    - writes the game and moves the bye;
    - one audit row `late-pairing`, ids only;
    - if the round is published: mails both through the publish path (a shared internal
      sender, so the `publish-pairings` audit row records them with `mode: "late"` and
      `getPublishStatus` counts them), then `scheduleBroadcastPush({ roundId })`.
  - `publishPairings` keeps its behaviour; its sending moves into the shared sender.
- **UI** in the league round panel, while the round is in progress and someone has no game: a
  section naming them and a way to pair two of them.
  - With exactly two players: one button.
  - With more: two selects.
  - With one: the sentence about the ½-point bye after the round.
  - A confirmation names the board and the colours (○ ●) and says who gets mailed.
  - A notice afterwards says what was sent.
- **Living reference**: the panel's state on `/admin/design` if the panel is shown there.

## Out of scope

- Ordinary (unscheduled) Swiss events: no publish, no panel. A later item if a club evening needs
  a latecomer's board.
- Pairing a late player against the player with the pairing-allocated bye (turning the bye into
  a game). A follow-up if it comes up.
- Knockout events, rounds that are PAIRED (Regenerate covers them) or COMPLETED.
- Any change to the pairing engine, the mails' wording or the catch-up byes.

## Testing

- **Unit:** `late-pairing.test.ts`:
  - colours by seed parity with no history, and by history when there is some;
  - every refusal;
  - the board after the last game, and the bye moved;
  - the candidates.
- **Int:** `tests/int/late-pairing.int.test.ts` on a published league round with two late
  approvals:
  - the game, its board and colours;
  - both mails captured;
  - the `publish-pairings` record (`mode: "late"`), with `getPublishStatus` counting them;
  - the `late-pairing` audit row;
  - the feed carrying the new board as `*`;
  - the player page's pairing card;
  - an unpublished round: no mail, publishing later mails them;
  - an odd field: the bye moves after the new board;
  - two clicks at once: one game;
  - every refusal (PAIRED, COMPLETED, knockout, a player with a game, the same player twice, a
    withdrawn or pending entry, a rematch in a later round);
  - completing the round needs the new board's result.
- **Browser** (verify skill):
  - the panel with two late players;
  - the dialog;
  - the result notice;
  - 390 px and dark.

## Acceptance criteria

- [x] Two late entries of a published round 1 are paired on the next board with the engine's
      colours, both are mailed their pairing card with their own link, and the delivery count
      includes them. — `late-pairing.int.test.ts` "the board after the last one…": board 3,
      seeds 5 and 6 (the odd higher seed White), two captured mails each with its own link and
      not the other's, `getPublishStatus` 6 of 6 with nothing pending, the `publish-pairings`
      record `mode: "late"`, the `late-pairing` row without a token. The browser run: "Board 3
      added: Emil Steiner against Fiona «Knight» Huber. Both have their pairing mail." and "6 of 6
      players have the mail" after the refresh.
- [x] The new board is on the player page, in the Close-round helper, in the feed and on Lichess,
      like any other board; completing the round needs its result. — The int tests "the new board
      is in the feed and on the player page" (`[Board "3"]`, the dashboard's slot board 3 Black) and
      "completing the round needs the late board's result…" ("1 game(s) still missing results").
      Fiona's player page in the browser: "You play Black on board 3 against Gaston G." with his
      contact. The Close-round helper and Lichess read the round's games, so the board is theirs by
      construction (`scheduleBroadcastPush({ roundId })` after the mails); the push itself is a
      no-op under test and without a token.
- [x] Every refusal answers with a sentence and writes nothing; two concurrent clicks make one
      board. — The two refusal tests (the same player, a seated player, withdrawn, pending, a
      stranger; paired, pending, unscheduled, knockout, a finished event, no such round) each end
      on `expectUntouched`; "two clicks at once make one board and send two mails" (the second
      waits on the round's lock and reads the new board). The unit tests cover every sentence.
- [x] Unflagged and ordinary events are unchanged (no panel, the action refuses). — The section
      lives inside the league round panel, which only a scheduled round renders, and only while
      the preflight lists someone without a game; an unscheduled round is refused ("Only a
      scheduled league round takes a late pairing"). `publishPairings` sends through the same
      extracted helper with the same arguments: `publish-pairings.int.test.ts` (34) unchanged and
      green.
- [x] Gates: `npm test`, `npm run build`, `npm run lint`, `npm run typecheck`, `npm run test:int`.
      — See the Handoff (the counts and the one caveat about the untracked staging scripts).

## Your turn

Sam:

1. Merges and deploys. There is no migration.
2. Approves the two late registrations of 13 Oct (if not done), pairs them from the round 1
   panel, and checks that both have the mail.
3. If round 1 completes before this ships, awards their ½-point byes from the Players tab
   instead.

## Handoff

**Built (14 Oct 2026, one session on the claude-box, `feat/late-pairing` off `main`; no
migration).** As planned and approved:

- **Pure `src/lib/league-rounds/late-pairing.ts`** (barrel-exported, 22 unit tests):
  - `latePairingHistory` (colours and opponents from the completed rounds' games, as the
    generator builds them), `lateBoard` (after the last game; the pairing bye on that number
    moves one down, ½-byes stay on 0), `planLatePairing` (the same player, not entered, a game or
    a bye in the round, a rematch, colours illegal either way round; then `assignColors`),
    `lateRoundRefusal` (knockout, not active, unscheduled, PAIRED → regenerate, PENDING,
    COMPLETED → the ½-point bye).
  - The words: `latePairingView` (one / pair / choose), `latePairingConfirm` (the board, who has White and
    who is mailed), `latePairingResult`.
  - Names are the organiser's form (`formatAdminPlayerName`, no trailing initial), like the
    preflight's, so a sentence can end on one.
- **Actions** (`actions/league-rounds.ts`): `publishPairings`' entry query and its send-and-record
  loop became `loadNoticeEntries` and `deliverPairingMails` (unchanged behaviour; the record's
  `mode` is `"first" | "unsent" | "all" | "late"`). `previewLatePairing` (reads only) and
  `addLatePairing`: `lockRound`, the checks again under the lock, the bye moved, the game
  written, one `late-pairing` audit row (ids only), then on a published round the two players'
  notices through `deliverPairingMails(…, "late")` and `scheduleBroadcastPush({ roundId })`.
- **UI** `components/admin/late-pairing-section.tsx` (5 component tests), in the league round
  panel after the Publish block while someone has no game: the title states the fact ("2 players
  without a game"), the two sentences, two selects from three players on (Against never offers
  the Play choice; a chosen player who got a board meanwhile falls back to the first left), one
  button "Pair on a new board" (secondary until the pairings are published, since Publish is
  the panel's primary until then), the `ConfirmDialog`, the status notice. With one player
  there is nothing to press. Not on `/admin/design`: the panel isn't there either.
- **Int** `tests/int/late-pairing.int.test.ts` (9): the board and colours, the mails and the
  delivery count, the feed and the player page, the moved bye, an unpublished round then Publish,
  two clicks at once, the preview, completing the round and the rematch, every refusal.

**Gates (14 Oct, the box):** `npm test` 3189 green, `npm run test:int` 605 green on `int_lp`
schemas (46 files; `late-pairing` 9, `publish-pairings` 34 unchanged), lint clean, `npm run
build` and `npx tsc --noEmit` clean with the untracked staging scripts held aside. One caveat: the checkout
holds the five untracked `scripts/__*.ts` of the live-boards staging pair, two of which name
`liveBoards`, a column this branch's Prisma client doesn't have, so `npm run typecheck` prints
those two files' errors here until the staging clean-up; CI (on the PR) never sees them.

**The browser run** (dev server on 3901 with `POSTMARK_SERVER_TOKEN=`, a throwaway admin, two
seeded events with round 1 published and late approvals, Playwright 1.64 headless): event B
(two late players) on a desktop in light: the section, the dialog "Add board 3 to round 1?" with
"Emil Steiner has White against Fiona «Knight» Huber" (the glyphs ○ ● came out of the message after the dark screenshot: a text glyph takes the text colour, so the filled disc read as White), the status "Board 3 added …
Both have their pairing mail.", after the refresh no section, board 3 in the list and "6 of 6
players have the mail"; event A (three) on a phone at 390 px in dark: the two selects, Fiona
against Gaston paired, then "A player without a game" with Emil's sentence; Fiona's player page:
"You play Black on board 3 against Gaston G." with his contact; four `[email:capture]` lines in
the server log, the subjects "Classical League round 1: White against …" / "Black against …";
`scrollWidth` 390. The events, players, admin and scripts were removed; the server stopped.

**Decisions taken in plan mode** (approved): scope = scheduled rounds in progress of an ACTIVE
Swiss event (PAIRED keeps Regenerate; ordinary events and turning the pairing bye into a game are
follow-ups); the board after the last game with the bye moved down; colours from the engine's
own `assignColors`; mail through the publish path with `mode: "late"` so the delivery count is
one; an unpublished round mails nobody.

**Your turn (Sam):** 1. Merge and deploy (no migration). 2. Approve the two late registrations
of 13 Oct, open round 1's Rounds tab, pair them from the section, check both have the mail. 3.
If round 1 completes first, award their ½-point byes from the Players tab instead.
