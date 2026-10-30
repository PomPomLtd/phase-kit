---
name: phase-example
description: A real, filled-in phase doc from a Pom Pom project (a chess tournament app), as the example of how much detail a phase doc carries before and after its session. Loaded by the phases skill; not for direct use.
user-invocable: false
---

# A phase doc, filled in

From a real project, trimmed to the size a phase doc should be: about 40–80 lines before its session
(everything down to "Your turn"), and 10–20 for the Handoff. The project's own file names and rules
are not the point; the shape and the level of detail are. It points at code rather than explaining
it, and every criterion says what proved it in a line or two.

---

# Phase J: add a late pairing to a round in play

**Status**: built 14 Oct 2026 on `feat/late-pairing`, merged and deployed on Sam's word; awaiting Sam
(pairing the two late players).
**Prerequisites**: A–I on production (Season 3 round 1 in play since Mon 12 Oct).
**Expected sessions**: 1.
**Calendar**: the two late registrations of 13 Oct can only play round 1 if this is live by Thu 15 Oct.
**Verify command**: `npm run test:int -- late-pairing`
**Read first**: `src/lib/actions/rounds.ts` (`generateRoundPairings`, `completeRound`, the round lock),
`src/lib/actions/league-rounds.ts` (`publishPairings`), `src/lib/league-rounds/publish.ts`,
`src/lib/pairing/colors.ts` (`assignColors`), `src/components/admin/league-round-panel.tsx`.

## Goal

On a Swiss league round in progress, the organiser pairs two entered players who have no game in it
against each other on a new board. Colours come from the pairing engine. If the pairings are
published, both players get their pairing mail, the board goes to Lichess, and the round's delivery
count includes them. Anything else is refused with a sentence. Today those two would sit out the
round and take ½ point each, when they could play each other.

## Tasks

- [x] Pure `src/lib/league-rounds/late-pairing.ts`: `planLatePairing(input)` returns the board, the
      colours and any bye to move, or a refusal sentence. Colours by `assignColors`; the board right
      after the last game, a pairing-allocated bye moved one down so it stays last.
- [x] Actions: `previewLatePairing` (reads only) and `addLatePairing` (under the round lock: writes
      the game, moves the bye, one audit row with ids only; on a published round, mails both through
      the publish path with `mode: "late"` and schedules the Lichess push).
- [x] UI in the league round panel while someone has no game: two players, one button; more, two
      selects; one, the sentence about the ½-point bye. A confirmation names the board, the colours
      and who gets mailed.

## Out of scope

- Ordinary (unscheduled) Swiss events, knockout events, and rounds that are PAIRED (Regenerate covers
  them) or COMPLETED. A later item if a club evening needs it.
- Turning a pairing-allocated bye into a game. A follow-up if it comes up.
- Any change to the pairing engine, the mails' wording or the catch-up byes.

## Acceptance criteria

- [x] Two late entries of a published round are paired on the next board with the engine's colours,
      both are mailed, and the delivery count includes them. — `late-pairing.int.test.ts` "the board
      after the last one…": board 3, two mails each with its own link, `getPublishStatus` 6 of 6.
- [x] The new board is on the player page and in the feed, and completing the round needs its result.
      — int tests "the new board is in the feed and on the player page", "completing the round needs
      the late board's result…"; the player page in the browser at 390 px and dark.
- [x] Every refusal answers with a sentence and writes nothing; two clicks at once make one board. —
      the two refusal tests end on `expectUntouched`; "two clicks at once make one board".
- [x] Unflagged and ordinary events are unchanged. — `publish-pairings.int.test.ts` (34) unchanged
      and green.
- [x] Gates green. — `npm test` 3189, `npm run test:int` 605, lint and build clean; one caveat in the
      Handoff.

## Your turn

1. Approve the two late registrations of 13 Oct, open round 1's Rounds tab and pair them from the new
   section. Both should have the mail.
2. If round 1 completes first, award their ½-point byes from the Players tab instead.

## Handoff

**Built 14 Oct 2026, one session.** Pure logic in `late-pairing.ts` (22 unit tests), the actions in
`actions/league-rounds.ts`, the section in `components/admin/late-pairing-section.tsx` (5 component
tests), `tests/int/late-pairing.int.test.ts` (9).

**Decided in plan mode**: scheduled rounds in progress of an active Swiss event only; the board after
the last game with the bye moved down; mail through the publish path, so the delivery count stays
one number; an unpublished round mails nobody.

**Changed along the way**: `publishPairings`' sending moved into a shared `deliverPairingMails`,
behaviour unchanged. The colour glyphs ○ ● left the confirmation message: in dark mode the filled disc
read as White.

**The next session must know**: the five untracked `scripts/__*.ts` of the live-boards staging pair
make `npm run typecheck` print errors for two files until the staging clean-up. CI never sees them.
