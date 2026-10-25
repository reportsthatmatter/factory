# Agent review: Mueller notes, ingest #85 and us-mueller-report #3 (2026-10-25)

Reviewer: Opus agent. I did not touch production: no merge, no `pnpm release`, no pin bump on main, no `pnpm ship` (only `--plan` with a scratch state file), no publish, deploy, reindex, seed or `wrangler d1`. I did not touch the shared checkouts (site, ingest, us-mueller-report); all work was in my own worktrees.

**Verdict: MERGE BOTH, then release ingest v0.31.0 and re-ingest Mueller.** The oracle change removes only false positives that I could find (sampled on page images, Mueller and Senate CIA), no other report's findings or text move, and the 112 new Mueller links I checked are all on the right note.

## Scope

- **ingest #85** (`muellerq-1025`): head **94c9aa8** at review start, **8874469** after my fixup (test comment only; `src/` and `dist/` untouched). Based on main `9d39af0` (v0.30.0), mergeable.
- **us-mueller-report #3** (`muellerq-1025`): head **d96b032**, no fixups. Based on `ef46b1e` (v0.29.0 pin); main is `a951183` (pin bump to v0.30.0 only). Trial merge onto main is clean (the PR does not touch `package.json`).
- **No site PR exists.** The ingest PR body says "see site PR"; there is none. The author's lesson `docs/design/lessons/2026-10-25-oracle-false-positives-first.md` is uncommitted in `~/src/reportsthatmatter/rtm-muellerq-1025`. It goes into the integrator's pin-bump PR (step 4).

## Trial

- **ingest** worktree `ingest-review85-1025` (branch `trial85-1025` = origin/main + `--no-ff` merge of #85 at 94c9aa8): `pnpm build` leaves `dist/` unchanged (`git status --porcelain -- dist` empty); `pnpm test` **948 passed**. Fixup worktree `ingest-review85fix-1025`: the oracle test file passes (18/18), build leaves dist clean.
- **site** worktree `rtm-review85-1025` (branch `trial85-1025` = origin/main, nothing to merge), `pnpm bootstrap`; report worktree `rtm-review85-1025-reports/us-mueller-report` at #3's head (and `trial3-1025` = main + #3, clean merge). Trial ingest linked with `pnpm ingest link ../ingest-review85-1025`, `RTM_REPORT_DIRS=/Users/rgrp/src/reportsthatmatter/rtm-review85-1025-reports`.
- **Re-ingest reproduces #3 byte for byte**: `pnpm ingest run us-mueller-report` with the trial ingest leaves the report worktree clean.
- **Corpus effect**: the author's `pnpm ingest try` of the branch against v0.30.0 over all 20 reports with main's Mueller `ingest.ts` (`factory/logs/2026-10-25/rtm-muellerq-1025-try-all.log`, run 06:20, after the 06:13 head): rendered text unchanged for 20/20, no quality signal moved, findings moved only in Mueller (markers-unlinked 468 to 260) and Senate CIA (523 to 520). I re-measured both reports myself with `verify --findings-json` (base pinned v0.30.0, then linked trial) and got the same numbers.
- **Full verify** on the trial (`RTM_REPORT_DIRS=… VERIFY_SHARED=1 ./scripts/verify.sh`), log `factory/logs/2026-10-25/rtm-review85-1025-verify.log`: typecheck, 1419 unit tests, fidelity, anchors, quality budgets, editorial and the browser checks pass. Three checks fail, all for the same expected reason, the Mueller move: `corpus check` (words 189,264 to 189,323; ids changed in `interactions-and-contacts-with-the-trump-campaign`), `aliases check` (2 published ids gone) and `ingest check` (no baseline). After `pnpm aliases generate us-mueller-report` + `pnpm prerender`, `aliases check` passes (1 alias, 1 unmatched). Those are the integrator's baseline steps, not defects.
- **Ratchet**: `pnpm quality ratchet --dry-run` on the trial: `us-mueller-report: bare-footnote-marker 54 → 43, bare-marker-after-quote 19 → 5, note-reference-sequence 128 → 105, rendered-ol-words-share 3 → 2`. Not committed: there is no site PR; it goes into the pin-bump PR (step 4).
- **Golden pages**, trial against base, whole corpus: every report's oracle-on-golden counts are the same except Mueller, markers-unlinked tp4/fn0 to tp1/fn1 (pooled 9/10 to 7/8 here; 6/7 in the author's log). This is the correction, not a loss: on p.47 the base oracle's 3 labels were digit fragments (`8`, `12`, `26`) that matched the golden count of 3 only by coincidence; the trial links 126 and 128, and the one true miss left (129, under a redaction bar) was never seen by the oracle in either version.
- **`pnpm ship --plan --state build/review-ship-state.json`**: plan printed, nothing run.

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| ingest #85 at 8874469 | MERGE | Oracle change: every removed finding I sampled is a false positive; no other report moves. `scanned` is gated end to end (code read: the default path is the old code; try: 20/20 texts unchanged without the option). dist current, 948 tests. |
| us-mueller-report #3 at d96b032 | MERGE (after #85 is released) | 112 new links, none removed or reordered; 28 checked on the page image, all right. Reproduces byte for byte. Needs the v0.31.0 pin, baseline, corpus accept and aliases (steps below). |

## Findings

**Check (1), oracle false positives.** `markers-unlinked` on Mueller: 317 findings removed, 109 added (the joined numbers that replace their fragments: `1`,`2`,`6`,`2` to `1262`). Sampled against page images: Vol I PDF pp.40, 58, 91, 147, 150, 169, 180 (notes-block dates such as `3/11/16`, `5/17/18 302`; note numbers in the note block; split body markers). All false positives. Senate CIA (`archive/CRPT-113srpt288.pdf`): p.28 `11` is the OCR's closing quote in note 15 (`"comfortable11`), p.679 `31` is note 531's number in the notes block read as `,31`, p.138 `1`,`1` to `11` is redaction junk. All false positives. Hidden real markers in another report: none today (zero findings moved in 18 reports). One theoretical risk, not seen: on a page whose notes are in their own face, a list of body-faced lines that open on consecutive raised numbers at one left would be taken as the notes run, and markers below it would not be counted. Not worth a bead until a report shows it.

**Check (2), the new Mueller links.** The PR body says 83; `full.md` gains **112** `[^N]` refs (105 Vol I, 7 Vol II). None removed, every new one has its definition, every one sits in numeric order between its neighbours. Checked on the page image: Vol I 97, 102, 104, 105, 126, 128, 945, 946, 947, 1006, 1008, 1262-1269, 1272-1274; Vol II 605, 607, 1014, 1022, 1023, 1024. Checked on the PDF text layer: 62, 153, 432, 874, 922, 1055, 1225. All right. The 18 new `markers-spurious` (31 to 49, ungraded, golden precision 0/15) are all among the new links (105, 126, 922, 1006, 1008, 1225, 605 among those I checked on the image): correct links the oracle does not expect, as the PR says.

**Check (3), opt-in.** Confirmed by code and by the try (above). `layoutMarkers()` without `scanned` takes the old path: `line.raised` unchanged and `anchorPattern(…, loose=false)`.

**Check (4), tests and dist.** Pass, as above.

**Fixup pushed** to `muellerq-1025` (8874469): the p.150 fixture test said "eleven markers, 940 to 950" but expects 941-950. Marker 940 ("Deripaska.940") is missing because the layout sets its raised run (top 151) as a line of its own instead of putting it on its 154 line. Both the linker and the oracle miss it, in main as well. I noted this on `reportsthatmatter-vs2x`; it may hide other OCR-page markers.

**Not fixed, for the integrator (no defect in the PRs):**

- #3 moves 2 published paragraph ids: `trump-97-kellyanne-conway-brad` becomes an alias of `trump-kellyanne-conway-brad-parscale`, and `president-104` is unmatched (its paragraph is the one-line "Mr. President!" fragment, now `mr-president`). This is Mueller's first `aliases.yaml`. The PR body's "text otherwise unchanged" leaves this out.
- There is no site PR. The lesson, the ratchet, the aliases and the corpus baseline all go into the pin-bump PR.

**What moves**: only us-mueller-report (112 notes linked, 59 words, 2 ids). Grade: D 12.28 to C 5.28 (`rtm-muellerq-1025-grade-after.log`); Senate CIA N 6.8 to 6.4 from the oracle alone. **D1**: one report republished, about 110 changed paragraphs reindexed, small.

## Integrator steps

1. Merge ingest #85 at **8874469**: `gh pr view 85 -R reportsthatmatter/ingest --json headRefOid` (must be `8874469…`), then `gh pr merge 85 -R reportsthatmatter/ingest --squash`.
2. Release: in the shared `~/src/reportsthatmatter/ingest`: `git pull --ff-only && pnpm release 0.31.0 --dry-run && pnpm release 0.31.0`.
3. Merge us-mueller-report #3 at **d96b032**: `gh pr merge 3 -R reportsthatmatter/us-mueller-report --squash`. It merges cleanly onto main `a951183`.
4. Pin bump, in a new site worktree from origin/main (not the shared checkout while another `pnpm ship` may be running): `pnpm bump-pin 0.31.0 --dry-run`, then `pnpm bump-pin 0.31.0 --shared`. In the same site branch:
   - `pnpm ingest aggregate us-mueller-report`
   - `pnpm ingest check`: only Mueller should move. It must equal #3's `full.md`; if it does not, stop.
   - `pnpm ingest baseline us-mueller-report`
   - `pnpm aliases generate us-mueller-report` (expect 1 alias, 1 unmatched as above), then `pnpm prerender`, `pnpm aliases check`
   - `pnpm corpus accept us-mueller-report`
   - `pnpm quality ratchet` (expect the 4 Mueller budgets above)
   - copy the lesson from `~/src/reportsthatmatter/rtm-muellerq-1025/docs/design/lessons/2026-10-25-oracle-false-positives-first.md`
   - `VERIFY_SHARED=1 ./scripts/verify.sh` must pass

   Commit all of it and open and merge the pin-bump PR.
5. `pnpm ship --reset`, then `pnpm ship --shared` (gates: `--ack baseline`/`corpus` for Mueller only). Afterwards `pnpm grade --record` should show Mueller C.
6. Beads: append the ship to `reportsthatmatter-vs2x`. Do not close it: 161 notes are still lost on the OCR pages.

## Beads

- `reportsthatmatter-vs2x`: verdict line appended, plus the p.150 marker-940 layout miss. No new bead.

## Lessons

- An oracle's precision on golden pages can look better than it is when digit fragments happen to match the golden count. The tp drop here was a correction.

## Getting better faster

- Slowed by: the PR body's "83" against the 112 in the file, and the missing site PR that the body referred to.
- Would have caught it: `pnpm ingest try` printing the count of `[^N]` refs added or removed per report, and `aliases check` in the try's output.
- Proposal: `ingest try` should report the ids each report loses (aliases dry run), so "text otherwise unchanged" claims are checked.
