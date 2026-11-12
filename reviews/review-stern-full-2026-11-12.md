# Agent review of the Stern Review full report, batch 1: uk-stern-review#2 and site #433 (2026-11-12)

Reviewer: Opus agent. I did not touch production: no merge, no release, no `pnpm ship` beyond `--plan`, no publish, deploy, reindex, seed or `wrangler d1`. The one production read is `pnpm publish-report uk-stern-review --dry-run --yes` (reads D1, writes nothing).

**Verdict: MERGE AND SHIP (after the reviewer fixups now on both branches).** No must-fix remains. Merge uk-stern-review#2 then site #433, then `pnpm ship`. Chapter 9 missing from the contents (owre) is acceptable for this ship; see below.

## Scope

- **reportsthatmatter/uk-stern-review#2** (`stern-full-1112`): **2a32693** at start, **82ef47c** after my fixup. Based on current report main `d43ce9d` (v0.34.0 pin, no bump).
- **reportsthatmatter/reportsthatmatter#433** (`stern-full-1112`): **1027e67e** at start, **1b59fdb4** after my fixup. Based on `f2e39f9b`; main has since moved to `47ae45fe` (#432 Columbia, #428/#437 Hillsborough record). It merges cleanly onto `47ae45fe` (auto-merges in the three budget files and `src/generated/editorial.ts`, no conflict).
- Also read: draft **#427** (`stern-posts-1112`, **2fdb488e**), stacked on top to check its five card highlights.

## Trial

- Site worktree `~/src/reportsthatmatter/rtm-review433-1112`, branch `trial433b-1112` = origin/main `47ae45fe` + `--no-ff` merge of #433 at `1b59fdb4` (`07c97c67`). Report worktree `~/src/reportsthatmatter/rtm-review433-1112-reports/uk-stern-review` (from `pnpm ingest worktrees uk-stern-review`), at #2's head.
- **Re-ingest reproduces.** `pnpm ingest run uk-stern-review` writes `full.md` identical to the committed one (report tree clean), and `pnpm ingest aggregate uk-stern-review` leaves the site clean. 149 pages, 156 notes all resolved, 60 headings, 94.4% of source words kept.
- **Checks on the final trial**, all exit 0: `pnpm typecheck`, `pnpm quality check` (stern within budget), `pnpm corpus check`, `pnpm aliases check` (uk-stern-review: 1,092 recorded ids, **0 aliases, 0 unmatched**), `pnpm editorial --check`, `pnpm posts --check`, `pnpm cards --check`, `pnpm ingest check`, `pnpm ingest verify uk-stern-review` (golden 14/16 + 2 xfail lb9l). `pnpm quality ratchet --dry-run`: nothing to lower. `pnpm test`: all pass once `build/editorial-highlights.json` is fresh (my trial had a stale copy from the #427 stack; 60/60 cards+poster re-run green).
- **verify.sh** (`RTM_REPORT_DIRS=… VERIFY_SHARED=1`), before fixups: `~/src/reportsthatmatter/factory/logs/2026-11-12/rtm-review433-1112-verify.log`. Four failures, none from this PR: corpus, aliases and quality fail only on **jack-smith-vol1** (the shared `jack-smith-report` checkout is at `bcad53b`, behind site main's #430 output: peer noise, not Hillsborough as the author saw), and two `tests/poster.test.ts` timeouts under load (51/51 alone). uk-stern-review passes every section. **Final heads: `rtm-review433-1112-verify-final.log`, "All checks passed."** (by then the shared jack-smith-report had pulled its #14), and `git status --short reports/` clean.
- **Ship rehearsal:** `pnpm ship --plan --state build/review-ship-state.json`: 0 of 23 repos need a pin bump; the guard stops only because the trial is not on main. With both PRs merged the repo text equals the site copy, so the **baseline and corpus gates should not fire** for Stern.
- **D1:** `pnpm publish-report uk-stern-review --dry-run --yes`: 14 objects, 1.5 MB; 1,092 paragraphs, **882 new, 1 changed** (the ES "long-run" paragraph), 209 unchanged; **~2,654 row writes**, 218 reads. Negligible.

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| uk-stern-review#2 @ 82ef47c | MERGE | Reproduces; licence chain as unit 1; budgets justified (below); fixups pushed. |
| site #433 @ 1b59fdb4 | MERGE AND SHIP | ES ids and editorial/marks anchor; readable at B-or-better; fixups pushed. |
| #427 (draft, not in scope to merge) | unaffected | Its 5 card quotes resolve against the new text (`editorial --check`, `posts --check`, `cards --check` green on the 433+427 stack); no conflict. |

## Claims checked

- **ES ids do not move.** `published-ids.txt` and `published-sections.txt` are additions only (+882, +11); `aliases check` 0 aliases. ES text diff (old vs new section) is exactly one line: "long- run" to "long-run" (p.xxvii); the notes block moves to the end. True.
- **c-0004 "co-operation"**: kept as printed; reasoned in PROCESSING. Fine.
- **Registry title** now "Stern Review: The Economics of Climate Change". This re-renders the default card, which the PR left stale (`pnpm cards --check` failed): fixed (below). Quote cards use the common name, so #427's cards are unaffected.
- **Oracle markers-unlinked 25 to 248 is justified.** I checked all 248 against the PDF text layer: 242 are the subscript 2 of CO2/CO2e/GtCO2 (two printed "C02e" with a zero, Ch13 p.286); 5 are glued runs ("89", "28", "232", "1112") or note 25 in a dropped table, and each glued run is linked correctly in `full.md` (`wealth.[^8][^9]`, `GDP[^11][^12]`, `$15/tCO2[^8]`, `GtCO2[^32]`). The one real miss: Ch10 p.243 "only one model2" is a raised 2 (a repeat reference to note 2) served as text. Minor; noted on oyr5. The budget's "243 subscripts" is off by one in its wording only.
- **Quality budgets** (note-definition-unreferenced 2, note-reference-sequence 2, rendered-ol 1, numbered-prose-list 1, furniture-paragraph 5, heading-repeated 1) and **anchors blocks-wrong 1**: each reason checked (Ch10 note 4 is cited only as "Active revenue recycling4" in Table 10.1; "Key Messages" box titles; "References" per chapter). Justified, not quietening.
- **Licence chain**: every new PDF is HM Treasury, Crown copyright, from the same `stern_review_report.htm` page (UKGWA) via Wayback captures; `datapackage.json` digests match the files; OGL v3.0 as unit 1. The Jan 2007 reissue choice is documented in README with a word-diff rationale.
- **Reading pages, Chapters 6 and 10** (full.md and the prerendered fragments). Figure title, caption and Source line sit together with no axis-label fragments between them (Figures 6.1-6.6, 10.1, Table 10.1). Two defects found and fixed (below). Remaining, already beaded: tables served as title + source only, so Table 6.1 (the "5-20%" numbers) is absent, and the reading guide says so (6uqx); Box 6.3's equations are served garbled, e.g. "W = ∑ N (t ) ln C (t )e −δt + ( )e −δT (6)", and equation (1) is missing (6uqx, noted); one list item over a page break stands as a paragraph (95vk). Readable; B-or-better holds.
- **Chapter 9 not in the contents (owre): acceptable to ship.** The contents run Part III (p.168) then Chapter 10 (p.239); Chapter 9's 28 pages are inside the Part III section page with its own h3 and 9.x headings, so nothing is lost or unreachable, search finds it, and the reading guide's Part III entry says "followed in this edition by Chapter 9". The cost is deferred: when owre lands, Chapter 9's paragraphs move section and need section aliases (the mechanism exists). Fix owre before batch 2 adds more Part dividers.

## Fixups pushed

1. **Stray `***` on two Chapter 10 key findings** (uk-stern-review `82ef47c`). Ch10 p.247 stacks two bold-italic findings ("Consequently, the average expected cost ..." / "Potential co-benefits need to be considered."); `keyFindings` treated them as one run across the paragraph break, so readers saw literal `***` at the start of one paragraph and end of the next. The pass now starts a new run at a gap over 1.6 line heights. No quality signal, oracle count or golden page caught it; found by grepping the rendered HTML for `*`.
2. **Six line-end hyphen joins** (same commit, corrections c-0025 to c-0030): "Muir- Wood", "South- East Asia" (a Ch6 key finding), "Meta- Analysis", "non- CO2" (twice), "Low- CO2". Paragraph ids unchanged; words 76,021 to 76,015; `baseline.json` and PROCESSING.md (thirty corrections) updated.
3. **Site** (`1b59fdb4`): aggregate of the above, `pnpm corpus accept uk-stern-review` (words only, no id moved), `assets/cards/uk-stern-review/default.png` re-rendered for the new title, lesson `docs/design/lessons/2026-11-12-stern-review-stacked-findings-and-title-card.md`.

## Must-fix

None outstanding.

## Integrator steps

1. `gh pr merge 2 --repo reportsthatmatter/uk-stern-review --squash` (head 82ef47c). Check `gh pr view 2 --repo reportsthatmatter/uk-stern-review --json headRefOid` first; if it moved past 82ef47c, re-read the new commits.
2. `gh pr merge 433 --repo reportsthatmatter/reportsthatmatter --squash` (head 1b59fdb4). It merges onto main `47ae45fe` without conflict. If main has moved again and `reports/*-budget.yaml` or `src/generated/editorial.ts` conflict: take both sides for the budget lines (Stern's lines only), regenerate `src/generated/editorial.ts` with `pnpm editorial`.
3. Update the shared checkouts: `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only`, `git -C ~/src/reportsthatmatter/uk-stern-review pull --ff-only`; `unset RTM_REPORT_DIRS RTM_REPO_ROOT`.
4. From the site's main: `pnpm ship --shared`. Expect no pin-bump, no baseline or corpus gate for Stern (repo text equals the site copy). If `editorial` stops on a stale queue item, read it, then `pnpm ship --ack editorial`. `cards` should change nothing.
5. `d1-estimate` then `pnpm ship --yes`: publishes uk-stern-review only (~2,654 D1 writes), deploys, reindexes it (882 new paragraphs), seeds highlights, verify-prod.
6. `record`: commit `reports/quality-last.json`, `verify-last.json`, `docs/scores.json`, grades in a PR; changelog entry for the full-report batch.
7. In `reports/pipeline.yaml`, move the batch-1 row to `reached: publish` / done as the release-record step does, and correct its evidence "grade A 0.54" to the recorded grade (the PR body says 0.60).
8. Beads: `bd update reportsthatmatter-gqsy.9.2 --append-notes "batch 1 live <date>"` (keep open for later batches). Leave 6uqx, 95vk, owre, oyr5 open. #427 is a separate decision for Rufus (draft posts); it stacks cleanly on this.

## Beads noted

- reportsthatmatter-gqsy.9.2: verdict and this file.
- reportsthatmatter-oyr5: the one non-subscript unlinked marker ("only one model2", Ch10 p.243).
- reportsthatmatter-6uqx: Box 6.3 equations are served, but garbled and with (1) missing, rather than not served.

## Lessons

On the site branch: a bold-face run can span two findings (split on vertical gap); grep rendered HTML for literal emphasis markers; a registry title change re-renders the default card, and `verify.sh` does not run `pnpm cards --check`.

## Getting better faster

- What slowed me: switching branches in the trial worktree while `verify.sh` ran there, and a stale `build/editorial-highlights.json` from a stacked trial that failed `tests/cards.test.ts`. Run stacks in a second worktree.
- What would have caught the stray `***`: a quality signal counting literal `*` or `_` runs at the start or end of rendered paragraphs. Proposal: add it as `rendered-emphasis-literal` (budget 0 everywhere).
- Add `pnpm cards --check` to `verify.sh` so a title change cannot leave a stale default card.
