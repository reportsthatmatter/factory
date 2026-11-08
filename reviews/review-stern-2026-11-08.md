# Review: the Stern Review's Executive Summary (uk-stern-review, gqsy.9), 2026-11-08

Reviewer: Claude (Opus 5.5) agent. I did not touch production: no merge, deploy, publish, reindex, seed, `--preflight`, `publish-report --dry-run --yes` (it reads production D1) or `ship --shared`. I pushed no fixups.

**Verdict: MERGE both (report repo #1 first, then site #421 at ab23766e). No must-fix items. Five small follow-ups below.**

## Scope

| PR | Branch | Head at start | Head reviewed |
| --- | --- | --- | --- |
| [reportsthatmatter/uk-stern-review#1](https://github.com/reportsthatmatter/uk-stern-review/pull/1) | first-ingest-1108 | deaa5137 | deaa5137 |
| [reportsthatmatter/reportsthatmatter#421](https://github.com/reportsthatmatter/reportsthatmatter/pull/421) | stern-review-1108 | 1c3b925e | ab23766e (adds the hero, gqsy.9.1) |

## Trial

- Worktree `~/src/reportsthatmatter/rtm-review421-1108`, branch `trial421-1108` from origin/main 790f8a1f; merged 1c3b925e, then ab23766e, then origin/main 4c13a6d6 (#420, merged during the review). All merges were clean. `RTM_REPORT_DIRS=~/src/reportsthatmatter/rtm-stern-1108-reports` (report branch at deaa5137).
- `VERIFY_SHARED=1 ./scripts/verify.sh` on main 790f8a1f plus 1c3b925e: **exit 0**, "All checks passed". Log: `factory/logs/2026-11-08/rtm-review421-1108-verify.log`. It includes the uk-stern-review smoke checks (landing, /full, /marks, a 404 section, search, sidenotes). `git status --short` was clean afterwards.
- On the final trial (main 4c13a6d6 + ab23766e), I ran prerender, `editorial --check`, typecheck, test (1512 passed, 1 skipped), `quality check` (23 within budget), `corpus check` (23 unchanged), `aliases check` (stern: 210 ids, 0 aliases) and `quality ratchet --dry-run` ("Nothing to lower"). **All exit 0.** Logs: `rtm-review421-1108-checks.log` (at ab23766e on 790f8a1f) and `rtm-review421-1108-checks-main4c13.log`. I did not re-run the full verify after the hero commit and #420. The delta is one editorial block, two webp files and the Challenger text, and every check on it passes.
- Ship rehearsal: `pnpm publish-report uk-stern-review --dry-run --offline` gives 3 objects, 0.2 MB, hash 61ca9387971006f1, and an estimated 1 D1 row write (the reindex is not estimated offline). `pnpm ship --plan --state build/review-ship-state.json` ran clean. Log: `rtm-review421-1108-ship.log`.
- Product: in local `wrangler dev`, the landing page serves the hero (srcset 1200/2400, focus 50% 55%, alt text, credit "Photo: Dun.can, CC BY 2.0"), the meta description, and a `?p=` link → 200.
- #419 (cia-torture, still open) does **not** merge cleanly onto current main: there is a conflict in `reports/quality-budget.yaml` (the us-senate-cia-torture-report block, #418's note-definition-unreferenced line against #419's note-reference-sequence). This is not caused by #421, but the integrator will meet it if #419 ships in the same run.

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| uk-stern-review#1 @ deaa5137 | **MERGE** | The licence chain is sound. Each pass is justified and cites its page. The golden pages match the page images. The PDF matches its recorded SHA-256. |
| reportsthatmatter#421 @ ab23766e | **MERGE** (after #1) | The trial merge onto current main is green. No other report moves. Quotes are verbatim and the facts are sourced. The test edits are a fix. The hero's rights are confirmed. |

## Checks asked for

- **Licence (OGL).** The licence statement is sound. LICENSE, the README, datapackage.json (`OGL-UK-3.0`) and the gqsy.9 notes all give the same chain: Crown copyright (Crown servants inside HM Treasury, CDPA s.163) → HM Treasury's Click-Use regime of the time → The National Archives' statement that Click-Use material moves to the OGL → the UK Government Web Archive's own reuse statement (OGL). The residual risks are named: the PDF prints no copyright line, third-party figure data is excluded, and the CUP edition is not used. The registry's `license` matches. Markup is CC BY 4.0 and code is MIT, which is the house split.
- **ingest.ts.** There are four report-local passes, and each names its PDF page and the defect it fixes. Each one throws when its anchor is missing, so a changed text layer cannot silently drop text. `figureLabels` covers pp.4, 5, 12 and 15; `figureTitleBreak` covers p.5; `noteMarkers` sets 4 markers and explains why `layoutMarkers` was refused, because it read "CO2e" as `CO[^2]e`; `keyFindings` uses the bold-italic face. The library passes are commented with their pages: `quoteInset(14)` is justified by the indents of the lists on pp.6, 8 and 21-22, and `typographicHeadings` by the single heading on p.1. There is no library change and no pin bump (both repos pin v0.33.0). The 27 key findings in full.md match PROCESSING.md's count; the 28th `***` is the printed divider on p.ii.
- **Golden pages.** I rendered PDF p.5 and p.16 at 80 dpi with pdftoppm and read them against golden.yaml. On p.5, the Figure 2 title is its own block, the caption ends "...based on current scientific literature." and carries notes 2, 3 and 4, and the footnotes are 2-4. On p.16 there are 10 blocks: two bold-italic findings, and the last paragraph runs over the page from "Preliminary calculations..."; starts and ends match line for line. full.md agrees: "IPCC 2001[^2]", "study[^3]", "studies[^4]", "co-benefits" (correction c-0002). The two xfail pages, pp.3-4, are the known lb9l defect: Figure 1's title is joined onto the paragraph above.
- **Editorial.** `pnpm editorial --check` passes (7 findings, 5 quotes on the page, 12 highlights, a hero). I grepped each claim against full.md: "at least 5%, now and forever", "between 5 and 20%… upper part of this range", "should not be endowed with a precision and certainty…", "obligation to support them in adapting", and "Leaving out any one of these elements will significantly increase the costs". All match. Each background fact (July 2005, Head of the GES, former World Bank chief economist, a dozen countries, CUP in January 2007, Nordhaus 2007 in JEL, ~2% in June 2008, COP31 in Antalya 9-20 Nov 2026) has its source in the yaml header comment. The causal link to the Climate Change Act is rightly left out.
- **Nothing else moves.** The PR's `reports/` changes are limited to `reports/uk-stern-review/` and new rows in the budgets, manifest, pipeline and registry. `corpus check` shows 23 reports unchanged. Generated files: `marks.ts` re-sorts the uk-thirlwall-inquiry entry and drops a stray blank line, with no value change. `cards.ts` and `picks.ts` only gain stern entries. `marketing/queue.yaml` is untouched (empty diff).
- **Test edits.** Both edits change `meta.sections[1].slug` to `(meta.sections[1] ?? meta.sections[0]).slug`. Every report with two or more sections tests exactly what it did before. A single-section report now tests its only section, where before it threw on `undefined`. This fixes an unrealistic assumption and does not weaken the tests.
- **Imagery.** The plate is Figure 1, the Review's own chart ("Prepared by Stern Review, from data drawn from WRI CAIT"). It is recorded as Crown/OGL in the 2026-09-12 sources.yaml. The hero is Commons `1_Horse_Guards_Road,_Westminster_(22761348308).jpg`, by Dun.can under CC BY 2.0. I checked the Commons API: 3932×2469, licence CC BY 2.0, artist Dun.can. Credit and link are on the page.

## Findings, by severity (none blocking; none fixed on the branch)

1. **Hero credit year, low.** The credit says "2016", taken from the EXIF date (2016-11-12 18:24). That date is implausible: the plane trees are in full leaf, and it would be dark at 18:24 in November. Flickr photo id 22761348308 is also of a late-2015 upload, which is before the EXIF date. Suggest dropping the year from the credit, or the sources.yaml "photographed 12 November 2016" note should say "per the camera's clock".
2. **Figure wording, low.** The report repo's LICENSE ("No figure or image is reproduced here") and PROCESSING.md ("the figures are not reproduced here") are true of the text. The site's landing plate, however, shows a crop of Figure 1. The reuse is sound (the Review's own chart, OGL). Suggest adding a clause to PROCESSING.md, e.g. "the landing page's image is a crop of Figure 1", in a later edit.
3. **Budget key, low.** The stern block in `reports/quality-budget.yaml` lacks `note-definition-unreferenced`, which #418 added to every report after this branch was cut. The stern count is 0, check and ratchet pass, and nothing is required. The integrator may add `note-definition-unreferenced: 0` for uniformity.
4. **Dated copy, low.** `why_it_matters` ("as COP31 meets") and the background ("is published here as… COP31… meets") will read stale after 20 November 2026. Suggest a follow-up edit after COP31 to change this to past tense.
5. **Ship order, process.** uk-stern-review has never been published. The report-publish skill says to deploy first (`docs/report-preparation.md` §7 step 3), but `pnpm ship --plan` orders publish (17) before deploy (18). The integrator steps below deploy by hand first.

## What moves

- New report uk-stern-review: 1 section, 210 paragraph ids, 12,342 words, 5 footnotes. There is no reference edition, so scoring is waived. Golden is 6/8 with 2 xfail (lb9l). The oracle budget allows 25 `markers-unlinked`, all of them CO2 subscripts (oyr5).
- No other report's text, ids or served hash moves.
- **D1 estimate:** offline, 3 objects and 1 row write before the reindex. The reindex writes about 210 paragraph rows plus seeding about 12 highlights, a few hundred writes in all, far below the daily quota. The integrator's `d1-estimate` step gives the exact figure.

## Integrator steps

1. Confirm the heads have not moved: `gh pr view 1 -R reportsthatmatter/uk-stern-review --json headRefOid` should be deaa5137, and `gh pr view 421 -R reportsthatmatter/reportsthatmatter --json headRefOid` should be ab23766e. If either has moved, re-review the delta.
2. Merge the report repo first: `gh pr merge 1 -R reportsthatmatter/uk-stern-review --merge`, then `git -C ~/src/reportsthatmatter/uk-stern-review checkout main && git -C ~/src/reportsthatmatter/uk-stern-review pull --ff-only`. The shared checkout must be on main and clean for `ship`.
3. Merge the site PR: `gh pr merge 421 -R reportsthatmatter/reportsthatmatter --merge`, then `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only`. It merges cleanly onto main 4c13a6d6. Optionally add `note-definition-unreferenced: 0` to the stern block of `reports/quality-budget.yaml` (finding 3).
4. If #419 ships in the same run: its `reports/quality-budget.yaml` conflicts with main (us-senate-cia-torture-report block). Keep main's `note-definition-unreferenced: 344 …` line and #419's `note-reference-sequence: 287 …` line, then run `pnpm quality check`.
5. From the shared checkout: `pnpm bootstrap` (or `pnpm prerender`), then `pnpm ship --plan`. Check that `pnpm ship --status` shows no other integrator mid-release.
6. Deploy first, because the report has never been published: `./scripts/deploy-cloudflare.sh`.
7. `pnpm ship --shared --yes`. It publishes uk-stern-review with `--no-reindex`, deploys again (harmless), reindexes, seeds highlights (`pnpm editorial && pnpm seed-highlights --remote --yes`) and runs production verify. If it stops at a gate, read it, then resume with `pnpm ship`.
8. Check the exit gate: `curl -sD- -o /dev/null https://reportsthatmatter.org/reports/uk-stern-review/full | grep -i x-rtm-content-version` should print a hash, and `pnpm publish-report --all --status --base https://reportsthatmatter.org` should show uk-stern-review `current`.
9. In the record PR, set `reports/pipeline.yaml`'s uk-stern-review row to `reached: publish`, `state: waiting`, and run `pnpm vitest run tests/pipeline-status.test.ts` before committing.
10. Beads: close gqsy.9.1 (the hero shipped in ab23766e) with `bd close reportsthatmatter-gqsy.9.1 --reason "Live: <hash>"`. Close gqsy.9 once its remaining children are beaded elsewhere; gqsy.9.2 (the full report) stays open. lb9l, oyr5 and 6j6q stay open. Then `bd dolt push`.

## Beads

- I filed no new beads. Findings 1, 2 and 4 are small editorial edits for the stern agent or a later pass. Finding 5 (the ship plan's order for a never-published report) is worth a `stream:tooling` bead if it bites.
- Noted the verdict on gqsy.9.

## Lessons

- A single-section report broke two tests that indexed `sections[1]`. Any test that indexes a fixed section should fall back to the last available section. The PR already fixes the two that existed.

## Getting better faster

- **What slowed me:** the PR head moved during the review (the hero), and main moved twice (#418 and #420). I re-merged and re-ran checks twice, and the 7-minute verify was not repeated on the final trial.
- **What would have helped:** a `pnpm review-trial <pr…>` that rebuilds the trial at the current heads and re-runs the fast checks (editorial, typecheck, test, quality, corpus, aliases), leaving the full verify for when `reports/` or ingest move.
- **Proposal:** make `pnpm ship` detect a report with no served version and order deploy before publish, so the integrator steps need no manual step 6.
