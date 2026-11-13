# Agent review of Stern batch 3b (Chapter 2 technical annex, the Postscript and its Technical Annex, Annexes 7.b-7.g): uk-stern-review#6, site #455 (2026-11-13)

Reviewer: Claude (Opus) agent. I did not touch production: no merge, release, deploy, publish, seed or ship; no production D1 reads (`marks check --no-marks`; `publish-report --dry-run --offline` only; no `--yes`, no `d1-estimate`). I did not touch the shared checkouts (batch 3a was shipping).

**Verdict: MERGE AND SHIP both (uk-stern-review#6 → #455 → `pnpm ship --shared`) at the heads below, after one reviewer fixup (the anchors unit test did not test the change). No must-fix left. No ingest release or pin bump: already on v0.37.0.**

## Scope

- Report: [reportsthatmatter/uk-stern-review#6](https://github.com/reportsthatmatter/uk-stern-review/pull/6), branch `stern-b3b-1113`: head `6fde7b6` at start and final (no reviewer commit). One commit on `origin/main` `63676b6` (3a).
- Site: [reportsthatmatter/reportsthatmatter#455](https://github.com/reportsthatmatter/reportsthatmatter/pull/455), branch `stern-b3b-1113`: head at start `d4077839`, final `c5172448` (one reviewer commit). One commit on `origin/main` `40e32f68` (3a merged); both branches contain main.

## What the PRs do

Nine volumes, 597 → 655 pp, 1,237 → 1,355 notes, 38 → 47 sections: the Chapter 2 technical annex (2A, pp.41-54, the 2008 reissue) inserted after Chapter 2 as the table of contents sets it; then, after the contents' last entry, the Postscript, its Technical Annex and the web-only Annexes 7.b-7.g, which print no folios and are numbered by PDF page (served `%%page 1#5%%` …). Report-local passes `appendixFolios`, `appendixHeadings`, `appendixMarkers` (markers the text layer lifts onto their own line), list items in 7.c/7.e/7.g; corrections c-0097..c-0100 keep four line-end "socio-economic" from being joined once 2A's "socioeconomic" enters the hyphen vocabulary. Shared check change: `PDF_NUMBERED_VOLUMES` in `scripts/ingest/anchors.ts` (Stern only, by file name) and an optional `pdfNumbered` set on `checkAnchors`. Reading guide +3 entries (2A, Postscript, Technical Annex). Budget raises below.

## Trial

- Site trial `~/src/reportsthatmatter/rtm-review455-1113` (branch `trial455-1113` from `origin/main` `40e32f68`, `pnpm bootstrap`, `git merge --no-ff origin/stern-b3b-1113`, later the fixup); report trial `~/src/reportsthatmatter/rtm-review455-1113-reports/uk-stern-review` (`pnpm ingest worktrees uk-stern-review --branch review455-1113`, merged `origin/stern-b3b-1113`). Fixup worktree `~/src/reportsthatmatter/rtm-review455fix-1113` (branch `review455fix-1113`). No conflicts.
- Re-ingest (`pnpm ingest run uk-stern-review`, v0.37.0) reproduces the committed `full.md` byte for byte; the site copy equals the report repo's; both trees clean after (log `logs/2026-11-13/rtm-review455-1113-run.log`).
- Earlier text: every line of main's `full.md` (bar the pages/footnotes/corrections counts) is in the branch's. Ids, from main's and the branch's prerendered HTML: all 4,383 paragraph, 201 list and 597 page-anchor ids of main exist with identical text (sidenote checkbox counters `sn-N-M` aside, which renumber every batch); every main id keeps its section except `page-55` (finding 3). Main's 38 sections are an in-order subsequence of the branch's 47 (2A between Ch 2 and Ch 3). Ledgers only add lines (+429 published ids, +9 sections, +438 reserved); aliases 15 → 15, 0 unmatched.
- Gates at `d4077839`/`6fde7b6` (log `-gates.log`): `ingest check`, `corpus check`, `quality check`, `aliases check`, `editorial`, `editorial --check`, `posts --check` (5 Stern posts queued, all resolve), `cards --check`, `marks check uk-stern-review --no-marks`: all pass. `VERIFY_SHARED=1 RTM_REPORT_DIRS=… ./scripts/verify.sh`: All checks passed (`-verify.log`).
- At the final head `c5172448`: `verify.sh` All checks passed, git status clean (`-verify-final.log`); `pnpm quality ratchet --dry-run`: nothing to lower; `pnpm ship --plan` normal; `publish-report uk-stern-review --dry-run --offline`: 49 objects, 6.9 MB.
- `pnpm ingest verify uk-stern-review` (`-iverify.log`): golden 22/24 + 2 known (lb9l); oracle markers-unlinked 726 (= budget). Grade (`pnpm grade uk-stern-review --verify-log …-iverify.log`): **A, 0.91 per 10 pp, 655 pp** (P ~0.7, H ~0.0, N ~0.2, G ~0.0, I 0.0), as claimed. `pnpm ingest anchors uk-stern-review`: 655 markers, wrong 2 (the two known: `%%page 1%%` Part I, `308#2`), unmarked 0.

## Verdicts

| PR | verdict | reason |
|---|---|---|
| uk-stern-review#6 `6fde7b6` | MERGE | reproduces; earlier text and ids unchanged; sources and digests verified |
| reportsthatmatter#455 `c5172448` | MERGE AND SHIP | all gates pass on the trial merge; budget raises read against the pages |

## Findings, by severity

1. **Fixed (site `c5172448`): the new anchors unit test passed without the change.** With main's `src/lib/anchors.ts` it still passed: a single volume numbered from 1 is right under the `pdf-volume` scheme anyway, so `pdfNumbered` was never exercised. Rewritten with two volumes (the first folioed 10-12, the second unfolioed with note numbers at the page foot) and a second assertion that the undeclared reading fails; it now fails on main's code and passes on the PR's.
2. **Minor, not fixed (recommend, not block): the no-folio list is report data living in shared code.** The change itself is narrow: `checkAnchors` takes an optional set, defaulting to empty, so every other report runs the same code path as before (verify.sh's anchors budgets pass for all 23). But `PDF_NUMBERED_VOLUMES` duplicates what the report already declares in its `ingest.ts` (`APPENDIX_HEADS`, `appendixFolios`), and the next report that needs it edits shared code. Better: the report declares it (an export from its `ingest.ts`, or a field in `reports/manifest.yaml`) and `runAnchors` reads it. Bead filed (below).
3. **Note: `pageOf` does not return null for these pages; they cite as "p. N".** The renderer drops the `#n` qualifier from `data-page` (`@rtm/ingest` `markdown.js`: `PAGE_MARKER` captures the label only), so Postscript paragraphs carry `data-page="7"` and the reading guide cites the Postscript entry "p. 7", the Technical Annex "p. 1", 2A "p. 41". This is sensible: it matches the margin marker the reader sees beside the paragraph (also "7") and the guide entry names the section; it is the Ch 5/11/23 reissues' existing behaviour. But the PR's claim and `pageOf`'s docstring in `src/lib/editorial.ts` ("a volume-qualified '12#2' is null") are wrong: no `data-page` ever carries `#`. The margin markers label PDF pages "Printed page 1" (aria-label) for volumes that print none; harmless. Noted in the bead with finding 2.
4. **Note: `page-55` moves from Chapter 2's section to 2A's.** `%%page 55%%` stands before the Part II heading, so the page anchor belongs to the section before it, as on main (where that was Chapter 2). Printed p.55 is Part II's introduction, so neither is right, but nothing a reader links to changes text. Pre-existing pattern.
5. **Note: three unreferenced-note list ids shift** (`note-4-11`, `note-9-3`, `note-10-3` → `-12`, `-4`, `-4`), because their suffix counts note groups and 2A adds one before them. They are not in `published-ids.txt` (renderer-internal); the four notes are tvtu's. Appended to tvtu.
6. **Note: the PR's pipeline row says "one next-passage fingerprint updated"**, but `reserved-ids.txt` only adds lines. Cosmetic.

## Choices checked

- **Annex 7.g source.** Wayback CDX for `hm-treasury.gov.uk/d/annex7g_agriculture.pdf`: 2008-10-23 digest `ON7UAJA5…`, then `AMENK6NM…` from 2008-11-13 to 2009-12 (16 captures). I fetched the 2008-10-23 capture: its first page is "Annex 7.f Emissions from the land-use change and forestry sector" (a different printing from the archived 7.f, whose Wayback digest is `3GQFAPXN…`, but 7.f's text). The archived 7.g's SHA-1 in base32 is `AMENK6NMYE25DDKWV4M3QGTT7ZVGPSIH`, the 2008-11-13 capture, and its first page is the agriculture annex. Right choice, correctly recorded.
- **Digests.** All 46 resources in `datapackage.json` match their file's bytes and SHA-256; the nine new ones match `ingest.ts`'s `VOLUMES`.
- **Technical Annex's nine uncited notes.** Its PDF p.1 foot prints notes 1-9 (comments "via remarks at seminars", "Discussed in Section 1.4", … an ft.com link), then 10, which restates note 1 for the annex's own first sentence. Served as notes without markers; honest.
- **2A after Chapter 2.** Sections Ch 2 (p.23), 2A (p.41), Ch 3 (p.56) in order; Ch 2 and Ch 3 paragraph ids, texts and sections unchanged; reserved ids add only.
- **Reading guide.** The three new quotations checked against the PDFs: 2A p.41 "The 'consequentialist' and 'welfarist' approach…" (PDF has curly quotes; straightened, as the served text), Postscript "The broad conclusion of our analysis…", Technical Annex "Our estimates of damage…": verbatim. `editorial --check` passes.
- **Spot reads.** The three paragraphs above and their neighbours read against `pdftotext` of 2A, the Postscript and the Technical Annex: same words, marker `[^10]` placed where the annex prints it.

## Budget raises, each read against the page

- **markers-unlinked 601 → 726 (+125)**: sampled the "subscript run into a note" cases: `tCO29`, `tCO210` (7.b), `tCO210`, `tCO216` (7.c), `tCO21`, `tCO29` (7.e), `tCO217` (7.g) in the PDF text; served `tCO2[^9]` etc. with the notes present. Rest are CO2/CH4/N2O subscripts, as listed. Justified.
- **heading-marker 0 → 2**: 2A p.43 prints "2A.2 Intertemporal appraisals and discounting10"; Annex 7.c p.2 "Historical and projected business as usual trends6". Both served `…[^n]`. Justified.
- **heading-repeated 1 → 4**: the annexes print "Now", "Drivers of emissions growth", "Prospects for cutting emissions" as section heads in several annexes (`APPENDIX_HEADS`, read off the pages). Justified.
- **note-definition-unreferenced 4 → 13, note-reference-sequence 4 → 5**: the Technical Annex's notes 1-9 (above). Justified.
- **printed-page-reversal 3 → 11**: eight unfolioed volumes each restart at 1. Justified.

## What moves

- Only uk-stern-review. Its text: additions only (nine volumes; four "socio-economic" held as served). No id lost or changed; `page-55` changes section (finding 4). No other report's text, ids, quality or anchor counts move (`ingest check`, `corpus check`, `quality check` clean for all 23).
- **D1 estimate**: not measured (no production reads); reasoned from 3a (~1,300 ids, a few thousand writes): ~430 new ids → about a thousand writes. `d1-estimate` at ship.

## Integrator steps

1. Confirm heads: `gh pr view 6 --repo reportsthatmatter/uk-stern-review --json headRefOid` = `6fde7b6…`; `gh pr view 455 --repo reportsthatmatter/reportsthatmatter --json headRefOid` = `c5172448…`. If either moved, re-check at least that `pnpm ingest run uk-stern-review` reproduces, `pnpm aliases check`, `pnpm corpus check`.
2. `gh pr merge 6 --repo reportsthatmatter/uk-stern-review --squash`; `git -C ~/src/reportsthatmatter/uk-stern-review pull --ff-only`. No pin change (v0.37.0).
3. `gh pr merge 455 --repo reportsthatmatter/reportsthatmatter --squash`; `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only`.
4. From the site's main: `pnpm ship --plan`, then `pnpm ship --shared`. Expect: no pin bump; re-ingest with all 23 texts unchanged against the site copy; `aliases generate --all` touches only the nine new sections' timestamps in `reports/uk-stern-review/reserved-ids.txt`.
5. `d1-estimate` (expect about a thousand writes), then `pnpm ship --yes`. It should publish **only uk-stern-review**; if it lists another report, stop and read why. Deploy, reindex uk-stern-review, seed (nothing new), verify-prod. Check live: `/reports/uk-stern-review` contents lists 2A after Chapter 2 and, after Chapter 27, the Postscript, its Technical Annex and Annexes 7.b-7.g; `?p=broad-conclusion-our-analysis-urgent` lands in the Postscript; `?p=consequentialist-welfarist-approach-assessment-policy` in 2A; the reading guide shows the three new entries.
6. `record`: commit `reports/quality-last.json`, `verify-last.json`, `docs/scores.json`, grades (expect A 0.91, 655 pp) and the reserved-ids timestamps in a PR; changelog entry for Stern batch 3b (the full Review now online); advance the batch 3b row in `reports/pipeline.yaml` (`reached: evaluate, state: active`) to live with the content hash and this review's path, as for 3a.
7. Beads: close **reportsthatmatter-gqsy.9.2** on ship (this completes the web edition), with "batch 3b live <date>, review factory/reviews/review-stern-b3b-2026-11-13.md". tvtu and the new bead (below) stay open.
8. Clean up: `git -C ~/src/reportsthatmatter/reportsthatmatter worktree remove ~/src/reportsthatmatter/rtm-review455-1113` and `… ~/src/reportsthatmatter/rtm-review455fix-1113` (branches `trial455-1113`, `review455fix-1113`); `git -C ~/src/reportsthatmatter/uk-stern-review worktree remove ~/src/reportsthatmatter/rtm-review455-1113-reports/uk-stern-review` (branch `review455-1113`).

## Beads filed or noted

- Filed **reportsthatmatter-jta2** (P3, stream:quality, level:mechanical, discovered-from gqsy.9.2): move `PDF_NUMBERED_VOLUMES` from `scripts/ingest/anchors.ts` into the report's declaration, and fix `pageOf`'s docstring (no `data-page` carries `#n`).
- Noted on **reportsthatmatter-gqsy.9.2** (verdict, fixup) and **reportsthatmatter-tvtu** (the orphan-note list ids shift when a volume is inserted before them).

## Lessons

- A unit test for an opt-in must also assert the result without the opt-in; this one passed on the old code because another scheme already gave the right answer for its one-volume fixture.

## Getting better faster

- Slowed by: no tool to diff two prerenders' ids (I rebuilt main's Stern HTML in the trial by swapping `full.md` and wrote a 30-line comparison); the `sn-N-M` checkbox counters make every paragraph with a note look changed.
- Proposal: `pnpm ids diff <id> --base origin/main` that prerenders main's `full.md` for one report and reports lost ids, changed text (sidenote counters normalised) and changed sections; the reviewer's "no published id changes meaning" check becomes one command.
