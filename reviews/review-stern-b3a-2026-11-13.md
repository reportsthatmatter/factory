# Agent review of Stern batch 3a (Parts V and VI: Part V introduction, Chapters 18-20, Part VI introduction, Chapters 21-27): uk-stern-review#5, site #454 (2026-11-13)

Reviewer: Claude (Opus) agent. I did not touch production: no merge, release, deploy, publish, seed or ship; no production D1 reads (`marks check --no-marks`; no `publish-report --dry-run --yes`, no `d1-estimate`).

**Verdict: MERGE AND SHIP both (uk-stern-review#5 → #454 → `pnpm ship --shared`) at the heads below, after one reviewer fixup (Chapter 25 note 4). No must-fix left. No ingest release or pin bump: both already on v0.37.0.**

## Scope

- Report: [reportsthatmatter/uk-stern-review#5](https://github.com/reportsthatmatter/uk-stern-review/pull/5), branch `stern-b3-1113`: head at start `1e8aeb4`, final `2f4093a` (two reviewer commits).
- Site: [reportsthatmatter/reportsthatmatter#454](https://github.com/reportsthatmatter/reportsthatmatter/pull/454), branch `stern-b3-1113`: head at start `cf4a6ec0`, final `d766a558` (one reviewer commit). Base `origin/main` `3bb8b828`; both branches contain it.
- Batch 3b (`origin/stern-b3b-1113`, stacked on `1e8aeb4`) is out of scope and untouched; it now needs a rebase onto `2f4093a` (see integrator step 1).

## What the PRs do

Twelve volumes after Chapter 17 (Part V introduction, Chapters 18-20, Part VI introduction, Chapters 21-27), 425 → 597 pp, 857 → 1,237 notes, 26 → 38 sections, 4,393 paragraph ids (+1,293 body) and ledgers +1,343 ids, +12 sections. New report-local passes: `floatingMarkers` (two hand-checked note markers), a Chapter 26 `ordinalNote` fix (note 9's "25th"), 13 more figure/table regions, Chapter 24 and 22 added to `layoutPieces`, Chapter 20's `#33339a` and Chapter 26's `#000080` heading faces, Chapter 26 section 26.5 joined and served without its printed full stop. Reading guide +12 entries. Budget raises below.

## Trial

- Site trial `~/src/reportsthatmatter/rtm-review454-1113` (branch `trial454-1113` from `origin/main`, `pnpm bootstrap`, `git merge --no-ff origin/stern-b3-1113`); report trial `~/src/reportsthatmatter/rtm-review454-1113-reports/uk-stern-review` (`pnpm ingest worktrees uk-stern-review --branch review454-1113`, merged `origin/stern-b3-1113`); fixup worktrees `~/src/reportsthatmatter/rtm-review454-1113-fix/uk-stern-review` (report, branch `review-fix-b3-1113`) and `~/src/reportsthatmatter/rtm-review454fix-1113` (site, branch `review454fix-1113`). No conflicts.
- Re-ingest (`pnpm ingest run uk-stern-review`, v0.37.0) reproduces the committed `full.md` byte for byte at `1e8aeb4` and, after the fixup, at `2f4093a`; the site copy equals the report repo's. Log `logs/2026-11-13/rtm-review454-1113-run.log`, `-fixrun.log`.
- Earlier text: every line of main's `full.md` is in the branch's (only the front matter's pages/footnotes/corrections counts differ; main's notes block now follows the new chapters). Ids: all 3,100 rendered ids of main exist on the branch with identical text, 0 lost, 0 changed; main's 26 sections are a prefix of the branch's 38; aliases unchanged; ledgers only add lines. Four unreferenced table notes change *section* only (finding 3).
- Gates at `1e8aeb4`/`cf4a6ec0` (log `-gates.log`): `ingest check`, `corpus check`, `quality check`, `aliases check`, `editorial --check`, `posts --check` (5 Stern posts queued, all resolve), `cards --check` (passes once `pnpm editorial` has written `build/editorial-highlights.json`; on a fresh bootstrap it fails without it), `marks check uk-stern-review --no-marks`: all pass. `VERIFY_SHARED=1 RTM_REPORT_DIRS=… ./scripts/verify.sh`: All checks passed (`-verify.log`).
- After the fixup (log `-gates-fix.log`): `ingest check` and `corpus check` moved only by the intended change (words 303,072 → 303,075; markdownSha; no id listed), accepted with `pnpm ingest baseline uk-stern-review` and `pnpm corpus accept uk-stern-review`; all other gates pass; oracle counts unchanged (`-iverify-fix.log`); golden 20/22 + 2 known (lb9l). Final `verify.sh` at `2f4093a`/`d766a558`: All checks passed, git status clean (`-verify-final.log`).
- Grade (`pnpm grade uk-stern-review --verify-log …-iverify.log`): **A, 0.86 per 10 pp** (W –, P ~0.7 A, H ~0.0 A, N ~0.1 A, G ~0.1 A, I 0.0 A), as claimed. Plain `pnpm grade` reads the stale `reports/verify-last.json` and says 0.24: use `--verify-log`.

## Budget raises, each read against the page

- **markers-unlinked 514 → 601 (+87)**: the 87 new findings are all in volumes 27-37. Box 18.2 (Ch 18 PDF p.6, printed 409): 36, every one the subscript of T0/T1/A0/A1 (pdftotext bbox: smaller face, baseline below the word). Sampled the 48 formula/unit subscripts on Ch 19 p.8 (km², raised), Ch 21 p.9, Ch 22 p.13, Ch 24 p.17 (SF6), Ch 25 p.3, Ch 26 p.12: all CO2/SF6 subscripts or a unit exponent. "CO21" Ch 25 p.537: the text layer prints "21" as one token after "Gt CO"; served `Gt CO2[^1]` with note 1 present and linked. Table 21.1 "15" (Ch 21 p.7): notes 13-14 are on p.5 and 16-17 on p.8; no page prints note 15. "reputation1" Ch 21 p.463: raised 1, the page's notes are 33-35. Justified.
- **furniture-paragraph 17 → 27**: 27 "Key Messages" paragraphs, ten in Chapters 18-27, each the printed title of the chapter's head box. Justified.
- **printed-page-reversal 2 → 3**: Chapter 23's PDF is a 25-page reissue printing folios 1-25 (checked p.1, 2, 25); served `%%page 1#4%%` … `25#2%%` between 490 and 516, the Ch 5 / Ch 11 precedent. Justified.
- **note-reference-sequence 3 → 4**: Chapter 21's run 14 → 16 (note 15 only in Table 21.1, whose rows are not served, and printed on no page). Justified.

## Choices checked

- **Chapter 26 section 26.5 without its full stop**: printed "…resettling refugees." (p.566) is the only section heading in the report with one; serving it without matches every other heading and is recorded in PROCESSING.md. Sound.
- **`#33339a` and `#000080` heading faces**: named in the comment above `NAVY` (Chapter 20's reissue sets title and sections in a lighter navy; Chapter 26 sets 26.5 in its page-foot navy). Section headings served vs PDF lines: Ch 18 4/4, 19 7/7, 20 7/7, 21 5/5, 22 9/9, 23 8/8, 24 8/8, 25 5/5, 26 6/6, 27 5/5. The widened number regex (`\d{1,2}(?:\.\d{1,2})?`) and new faces leave every earlier volume byte-identical. Sound.
- **`floatingMarkers`**: declared only in this report's `ingest.ts`, filtered to `(volume of file, pdfIndex)` pairs (Ch 20 PDF p.6, Ch 26 PDF p.5), looks for its marker only within two lines above the anchor, and throws if either anchor is missing; it cannot fire on another page or report. Served `agenda for governments.[^15]` and `transfer of technology.[^9] There`, with notes 15 and 9 printed on those pages. Narrow.
- **Text retained**: every PDF prose line of 9+ words in Chapters 18-27 is in `full.md` except Table 21.1's rows (known, 6uqx), one Table 25 heading row and two line-end-hyphen normalisation misses. Log `-dropped.txt`.

## Reading guide

All 12 new excerpts are in the paragraph they name, in the section they name, and verbatim in the PDF text (curly quotes/dashes normalised). The "why" lines match the chapters' contents (spot-read).

## Spot-read (`pnpm paragraphs uk-stern-review`)

Chapter 18 (pp.404-410), Chapter 22 (pp.468-474) and Chapter 25 (pp.537-541) read cleanly, headings, boxes and figures in place, with two exceptions: Chapter 25 note 4's run-over (finding 1, fixed) and Box 18.1's two paragraphs merged across its first diagram (finding 2).

## Verdicts

| PR | verdict | reason |
|---|---|---|
| uk-stern-review#5 @ `2f4093a` | MERGE first | Reproduces; earlier text and ids unchanged; budgets justified on the page; one defect fixed. |
| reportsthatmatter#454 @ `d766a558` | MERGE AND SHIP after #5 | All gates and `verify.sh` pass on the trial; grade A 0.86. |

## Findings, by severity

1. **Fixed (medium, reader-visible): Chapter 25 note 4 truncated, its tail served in the body.** Note 4 (p.537) ends "…estimates emissions from land" at the page foot and runs over to two lines at the head of p.538's notes; those lines ("use change within the range equivalent to 2.2 to 9.9 GtCO2,, … Baumert KA et al. (2005).") were served glued to Figure 25.1's source line. Fixed with the existing mechanism (as Chapter 9 note 17, Chapter 11 note 5): a `NOTE_TAILS` entry (Ch 25 PDF p.2, checked against the following "5") and correction `c-0200` (numbered out of sequence to avoid batch 3b's c-0097…c-0105). Report `12b5e88` (ingest.ts, corrections.yaml, full.md, fidelity.md, baseline.json) and `2f4093a` (PROCESSING.md: 97 corrections, three run-over notes); site `d766a558` (full.md, PROCESSING.md, corpus baseline words +3). No id moved; oracle counts unchanged. Found by a scan for new notes ending mid-sentence: only this one of 53 is truncated (the rest are web addresses or end as printed).
2. **Not fixed (low): Box 18.1 (p.408), "In the short run – and given a change in E …" is merged into the paragraph before it** across the first diagram the figure region drops; the second paragraph after the second diagram is split correctly. Text intact; part of the paragraphs-merged estimate already in the grade. Noted on gqsy.9.2; fix in batch 3b or later if the author wants (would add one id).
3. **Not fixed (low, pre-existing): four unreferenced table notes route to the last section.** `note-4-11`, `note-9-3`, `note-10-3`, `note-25-7` (glued table notes from earlier batches) move in `paragraphToSection` from Chapter 17 to Chapter 27, and batch 3b will move them again. Text and ids unchanged. Bead **reportsthatmatter-tvtu** (P3).
4. **Tooling (low): `pnpm grade <id>` without `--verify-log` reads the stale `reports/verify-last.json`** (0.24 instead of 0.86 here), and `pnpm cards --check` fails on a fresh bootstrap until `pnpm editorial` has run. Noted for the integrator; no bead (known behaviour, the release `record` step refreshes `verify-last.json`).

## What moves

- uk-stern-review only: +172 pp, +12 sections, +1,343 ids; no id lost or changed meaning; four note ids change section (finding 3). No other report moves (`corpus check`: 23 unchanged). Editorial: +12 reading-guide entries, `src/generated/editorial.ts` current.

## Integrator steps

1. Confirm heads: `gh pr view 5 --repo reportsthatmatter/uk-stern-review --json headRefOid` = `2f4093a…`; `gh pr view 454 --repo reportsthatmatter/reportsthatmatter --json headRefOid` = `d766a558…`. If either moved, re-check at least `pnpm ingest run uk-stern-review` reproduces, `pnpm aliases check`, `pnpm corpus check`. Tell the batch 3b author to rebase `stern-b3b-1113` onto `2f4093a` (conflicts expected only in `corrections.yaml` tail, `ingest.ts` `NOTE_TAILS` neighbourhood, `PROCESSING.md` line 22, `baseline.json`, `full.md`/`fidelity.md` — regenerate the last three).
2. `gh pr merge 5 --repo reportsthatmatter/uk-stern-review --squash`; `git -C ~/src/reportsthatmatter/uk-stern-review pull --ff-only`. No pin change (already v0.37.0).
3. `gh pr merge 454 --repo reportsthatmatter/reportsthatmatter --squash`; `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only`.
4. From the site's main: `pnpm ship --plan`, then `pnpm ship --shared`. Expect: no pin bump; re-ingest with all 23 texts unchanged against the site copy; aggregate no change; `aliases generate --all` touches only the twelve new sections' timestamps in `reports/uk-stern-review/reserved-ids.txt`.
5. `d1-estimate` (expect a few thousand writes: ~1,300 new ids), then `pnpm ship --yes`. It should publish **only uk-stern-review**; if it lists another report, stop and read why. Deploy, reindex uk-stern-review, seed (nothing new), verify-prod. Check live: `/reports/uk-stern-review` contents lists Part V, Chapters 18-20, Part VI, Chapters 21-27; `?p=adaptation-crucial-deal-unavoidable-impacts` lands in Chapter 18; Chapter 25 note 4 ends "…Baumert KA et al. (2005)." and Figure 25.1's source line ends "(2005)".
6. `record`: commit `reports/quality-last.json`, `verify-last.json`, `docs/scores.json`, grades (expect A 0.86, 597 pp) and the reserved-ids timestamps in a PR; changelog entry for Stern batch 3a; advance the batch 3a row in `reports/pipeline.yaml` (`reached: evaluate, state: active` now) to live with the content hash and this review's path, as for batch 2b.
7. Beads: append "batch 3a live <date>, review factory/reviews/review-stern-b3a-2026-11-13.md" to reportsthatmatter-gqsy.9.2 and keep it open (Postscript and annexes remain, batch 3b). tvtu stays open.
8. Clean up: `git -C ~/src/reportsthatmatter/reportsthatmatter worktree remove ~/src/reportsthatmatter/rtm-review454-1113` and `… rtm-review454fix-1113` (branches `trial454-1113`, `review454fix-1113`); `git -C ~/src/reportsthatmatter/uk-stern-review worktree remove` the two report worktrees under `rtm-review454-1113-reports/` and `rtm-review454-1113-fix/` (branches `review454-1113`, `review-fix-b3-1113`).

## Beads filed or noted

- Filed **reportsthatmatter-tvtu** (P3, stream:product, level:judgement, discovered-from gqsy.9.2): unreferenced table notes route to the last section.
- Noted on **reportsthatmatter-gqsy.9.2**: verdict, the Chapter 25 fixup, the Box 18.1 merge.

## Lessons

- A note that runs over a page and lands at the head of the next page's notes can glue onto a figure's "Source:" line in the body; the existing scans (golden pages, oracle) did not see it. A cheap check that found it: list notes whose text ends mid-sentence (lowercase word, no punctuation) that are not web addresses.

## Getting better faster

- Slowed by: `pnpm grade` silently reading a stale verify log, and `cards --check` needing a prior `pnpm editorial`.
- What would have caught finding 1 earlier: a quality signal "note ends mid-sentence and the next page's body opens with a lowercase run under a Source/caption line".
- Proposal: add `note-truncated` to `pnpm quality` (note text ending in a lowercase word with no terminal punctuation and not a URL), budgeted per report like the others.
