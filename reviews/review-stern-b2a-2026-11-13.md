# Agent review of Stern batch 2a and the Part-divider fix (owre): ingest#95, uk-stern-review#3, site #446 (2026-11-13)

Reviewer: Opus agent. I did not touch production: no merge, release, tag, pin bump, `pnpm ship` beyond `--plan`, publish, deploy, reindex, seed or `wrangler d1 --remote`. `publish-report --dry-run` ran offline and against my local D1 only; the D1 cost below is reasoned, not read from production.

**Verdict: MERGE AND SHIP all three, in the order below (ingest#95 → release v0.36.0 → uk-stern-review#3 → pin bump folded into #446 → #446 → `pnpm ship --shared`). No must-fix.**

## Scope

- **reportsthatmatter/ingest#95** (`owre-part-divider`): **c046630** at start and end. Based on v0.35.0 (`b29e9c5`).
- **reportsthatmatter/uk-stern-review#3** (`stern-b2-1113`): **198e538** at start and end. Based on report main `e294e0c` (v0.35.0 pin, no bump of its own).
- **reportsthatmatter/reportsthatmatter#446** (`stern-b2-1113`): **eb36df72** at start, **f121d3ac** after my one fixup (a lesson file). Merges onto main `3b9cce6b` with no conflict.
- Also stacked: draft **#427** (`stern-posts-1112`, **2fdb488e**) on top of #446, to check its five card quotes.

## Trial

- Ingest worktree `~/src/reportsthatmatter/ingest-review95-1113` (branch `trial95-1113` = v0.35.0 + #95): `pnpm build` leaves `dist/` clean; `pnpm test` 974/974.
- Site worktree `~/src/reportsthatmatter/rtm-review446-1113` (branch `trial446-1113` = main `3b9cce6b` + #446 + my fixup), ingest linked to the trial ingest (`pnpm ingest link ../ingest-review95-1113`, the state after the pin bump). Report worktree `~/src/reportsthatmatter/rtm-review446-1113-reports/uk-stern-review` (#3 merged). A second site worktree `rtm-review446s-1113` holds the #446 + #427 stack.
- **Corpus effect of #95, all 23 reports** (`pnpm ingest try ../ingest-review95-1113` from main, log `factory/logs/2026-11-13/rtm-review446-1113-ingest-try.log`): rendered text unchanged for all 23; no quality signal moved; no oracle/anchor finding appeared or vanished; no score decision flipped.
- **Section lists, measured myself** (every report's `meta.json`, main prerender against the trial prerender; script `factory/scratch/cmp.py`): 18 reports unchanged, 5 gain sections, **none loses a section slug, no paragraph id disappears**:

| Report | New sections | Paragraphs that change section |
|---|---|---|
| uk-stern-review | 9 Identifying the Costs of Mitigation (+ batch 2a's six chapters) | 193 Part III → Ch 9; plus the duplicate-id shift below |
| us-deepwater-horizon | Chapters One, Four, Eight | 328 / 421 / 357 from Parts I / II / III |
| uk-hillsborough-panel | Chapter 1: 1981-1989; Protocol on Disclosure of Information | 285 from Part 2; 35 from Appendix 1 |
| uk-grenfell-tower-inquiry | Chapter 4: The development of the regulatory regime | 148 from Part 2 |
| litvinenko-inquiry | Chapter 2 of Parts 4, 5, 6, 8 | 125 / 58 / 30 / 21 |

  The rendered Part pages left behind are sensible: each is the Part heading plus its own introduction (Litvinenko Part 5 is the shortest: heading, "Chapter 1: Introduction", one paragraph). Litvinenko's section alias `chapter-2-the-2006-russian-laws → part-5-…` is rightly dropped: that slug is a live page again. `?p=` links route by paragraph, so moved paragraphs still land. (The ingest PR body says "17 unchanged, 6 gain" over a table of five; the true count is 18 and 5.)
- **Stern re-ingest reproduces**: `pnpm ingest run uk-stern-review` in the report worktree leaves it clean (145,918 words, 92.9% of source). `pnpm ingest verify uk-stern-review`: golden 16/18 + 2 known (lb9l).
- **Checks on the trial**, all exit 0: `pnpm typecheck`, `pnpm quality check`, `pnpm corpus check`, `pnpm aliases check` (uk-stern-review 2,184 recorded ids, 0 aliases, 0 unmatched), `pnpm editorial --check`, `pnpm posts --check`, `pnpm cards --check` (133 match; one earlier run in the stack worktree reported 29 stale cards under load from the parallel `ingest try`, and passed on re-run in both worktrees), `pnpm lessons check`. `pnpm aliases generate --all` on the trial: 0 new aliases, 0 reused, writes nothing. `pnpm quality ratchet --dry-run`: nothing to lower.
- **verify.sh** (`RTM_REPORT_DIRS=… VERIFY_SHARED=1`): **"All checks passed."** (1,514 unit tests), `git status --short reports/` clean. Log: `~/src/reportsthatmatter/factory/logs/2026-11-13/rtm-review446-1113-verify.log`.
- **Ship rehearsal**: `pnpm ship --plan --state build/review-ship-state.json`: every repo's text equals the site copy; the guard stops only because the trial is not main. 0 repos need a bump in the trial because #95 is still versioned 0.35.0; after the release all 23 report repos and the site take the bump.

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| ingest#95 @ c046630 | MERGE, release v0.36.0 | Text and quality unchanged corpus-wide; 5 reports gain the chapter sections they were missing; no slug or id lost. |
| uk-stern-review#3 @ 198e538 | MERGE | Reproduces; golden 16/18 + 2 known; figure-region drops reviewed (all table cells and chart text); budgets justified. |
| site #446 @ f121d3ac | MERGE AND SHIP (with the pin bump) | All checks green on the linked trial; Deepwater guide repoint correct; findings below are accepted and beaded. |
| #427 (draft, not to merge now) | unaffected | Stacked on #446: `editorial`, `posts --check`, `cards --check` green; its 5 card paragraphs (`benefits-strong-early-action-climate`, `climate-change-presents-unique-challenge`, `important-corollary-there-high-price`, `impacts-climate-change-are-not`, `there-still-time-avoid-worst`) resolve and stay in the Executive Summary. |

## Findings, by severity

No must-fix. Accepted for this ship, each with a bead:

1. **Duplicate-text ids now land in a different chapter: 16, not 14, and 4 name a different reference** (bead reportsthatmatter-c99s; efso noted). Inserting Chapters 1, 3-5, 7, 8 before live chapters shifts every `-N` suffix. Verified on the dev server: `?p=key-messages` now redirects to Chapter 1 (was Chapter 2); `key-messages-2..-5` each move one chapter earlier; 11 reference-list ids move. Same text for the Key Messages titles and 7 references, but **four name a different work**: `tol-r-s-j-2002` (Ch 6 "part II: dynamic estimates" → Ch 3 "Part I: Benchmark estimates"), `intergovernmental-panel-climate-change-2001` (WG I "Scientific Basis" → Synthesis Report), `-2001-2` (WG III "Mitigation" → Synthesis Report), `-2000` ("Land-use…" → "Emissions Scenarios"). `aliases generate` reports 0 reused because references share most of their words. Aliases cannot help (a live id is never aliased, decision 0012); the only real fix is reserved ids (efso, a `needs-user` decision). **Why acceptable now:** nothing cites any of the 16 (editorial, share quotes, queue, #427; author's marks check 12/12 and 39/39), marks are keyed by paragraph and exact text, and these ids have been live about a day. **Not acceptable next time:** the next Stern batch (Chapters 11, 12) shifts Chapter 13's ids the same way; decide efso before it.
2. **Chapter 5 folios 1-21** (bead reportsthatmatter-6u5h). Anchors are unambiguous (`page-5-2`; `?p=page-5-2` lands in Chapter 5), and the contents show Chapter 5 at "p. 1" between 92 and 143, explained in PROCESSING. But every human-readable citation (passage panel, card footer, highlights, editorial meta) says "p. 5", the same as Chapter 1's p. 5. The roman i-ix repeat already has this shape. Acceptable as printed and documented; the fix is to name the section when a page label repeats.
3. **Oracle markers-unlinked 248 → 472**: justified. Of 472, 452 are "2", 10 "3", 5 "6", 5 glued runs. I spot-checked 8 pages against `pdftotext`: CO2/CO2e, km2, x2, SF6, m3, as claimed. Note-marker misses would show as `note-definition-unreferenced`, which is budgeted only for the four table-cited notes. Cost: the signal is now blind for Stern; oyr5 (do not count chemical subscripts) would bring it to about 6 (noted on oyr5).
4. **Annex 7A is not a heading**: it sits as a paragraph inside Chapter 7's "References" subsection, with no contents entry; and Chapter 3's Key Messages split "Buenos" / "Aires" across two paragraphs (bead reportsthatmatter-z4dy; the literal-bullet runs are 95vk/3nbi).
5. Other budgets (furniture 11, unreferenced notes 4, sequence 3, heading-long 3, printed-page-reversal 1, anchors markers-wrong 1): each reason read and matches the text.

**Reading Chapters 1, 3, 8 rendered** (dev server and full.md): Chapter 1 reads cleanly (key findings in bold italic, figure title/caption/source together, notes linked). Chapter 8 is clean apart from Box 8.1's table column headers standing as four one-line paragraphs (table not served, 6uqx). Chapter 3's Key Messages are the weakest stretch (bullets run together, 95vk), then fine from 3.1 on. B-or-better holds.

## Fixups pushed

1. Site #446 `f121d3ac`: lesson `docs/design/lessons/2026-11-13-reuse-check-blind-to-reference-lists.md` (the id-reuse check is blind to reference lists; c99s). No code or data change.

## What moves on ship

- Text: uk-stern-review only (+1,092 paragraphs, Chapters 1, 3-5, 7, 8). Every other report's text is byte-identical under v0.36.0.
- Sections: the five reports in the table above; no slug lost, no id lost, no new alias.
- Republish: **uk-stern-review, us-deepwater-horizon, uk-hillsborough-panel, uk-grenfell-tower-inquiry, litvinenko-inquiry** (fragments change). Offline dry runs: 21 / 39 / 42 / 17 / 70 objects.
- **D1, reasoned**: about 3,300 writes for 1,092 new Stern search rows plus the section title of roughly 2,000 moved paragraphs (Deepwater 1,106, Hillsborough 320, Litvinenko 234, Stern 209, Grenfell 148) rewritten in the search index: on the order of 10-15k row writes in all. Ship's `d1-estimate` reads the real figure.

## Integrator steps

1. Confirm heads: `gh pr view 95 --repo reportsthatmatter/ingest --json headRefOid` = c046630; `gh pr view 3 --repo reportsthatmatter/uk-stern-review --json headRefOid` = 198e538; `gh pr view 446 --repo reportsthatmatter/reportsthatmatter --json headRefOid` = f121d3ac. Re-read any new commits.
2. `gh pr merge 95 --repo reportsthatmatter/ingest --squash`; `git -C ~/src/reportsthatmatter/ingest pull --ff-only`; from `~/src/reportsthatmatter/ingest`: `pnpm release 0.36.0`.
3. `gh pr merge 3 --repo reportsthatmatter/uk-stern-review --squash`; `git -C ~/src/reportsthatmatter/uk-stern-review pull --ff-only`. (It does not touch `package.json`, so the bump below lands cleanly on top.)
4. Pin bump, folded into #446 so main never sits on v0.36.0 without the accepted corpus baselines (or, before it, on #446 without v0.36.0: either half alone fails `corpus check` and `editorial --check`):
   - `git -C ~/src/reportsthatmatter/reportsthatmatter worktree add ~/src/reportsthatmatter/rtm-ship446-1113 -b ship446-1113 origin/stern-b2-1113`, `cd` there, `pnpm bootstrap`, `unset RTM_REPORT_DIRS RTM_REPO_ROOT`.
   - `pnpm bump-pin 0.36.0 --dry-run --shared`, then `pnpm bump-pin 0.36.0 --shared`. It commits and pushes `package.json` + `pnpm-lock.yaml` in **all 23 report repos** on their default branch (`main`; `master` for columbia-accident and uk-leveson-inquiry). Any repo it skips (not on its default branch, or dirty): read why, fix, re-run.
   - Commit the site's `package.json` and `pnpm-lock.yaml` ("Bump @rtm/ingest to v0.36.0"), `git push origin HEAD:stern-b2-1113`, wait for CI, then `gh pr merge 446 --repo reportsthatmatter/reportsthatmatter --squash`.
   - Fallback, if `bump-pin` refuses a non-main site branch: open the pin-bump PR from main as #446's body describes, merge it, then merge #446 straight after. Main fails `corpus check` and `editorial --check` in between.
5. `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only`; from the site's main: `pnpm ship --plan`, then `pnpm ship --shared`. Expect: pin-bump step a no-op; re-ingest with all 23 texts unchanged (**no baseline gate**); `aliases generate --all` 0 new, Litvinenko's section alias stays dropped; **no corpus gate** (the five baselines are accepted in #446). If the corpus gate stops anyway, read it: only section lists of the five reports above should move, then `pnpm ship --ack corpus`. No editorial or cards change expected.
6. `d1-estimate` (expect about 10-15k writes), then `pnpm ship --yes`. It should publish exactly the five reports listed under "What moves"; if it lists another, stop and read why. Then deploy, reindex those five, seed (nothing new), verify-prod.
7. `record`: commit `reports/quality-last.json`, `verify-last.json`, `docs/scores.json` and grades in a PR; changelog entry for Stern batch 2a and for the five reports' new chapter sections (owre). In `reports/pipeline.yaml`, add or advance the batch 2a row as the release-record step does (grade A 0.87 per the PR).
8. Beads: close **reportsthatmatter-owre** (done-when met: Stern Part III and Chapter 9 are separate; no slug lost, so no section aliases needed; the other reports' moves read here). Append "batch 2a live <date>" to reportsthatmatter-gqsy.9.2 and keep it open. Leave open: 6uqx, 95vk, 3nbi, oyr5, efso, c99s, 6u5h, z4dy. #427 stays a draft for Rufus; it stacks cleanly.

## Beads filed or noted

- Filed: reportsthatmatter-c99s (reuse check blind to reference lists), reportsthatmatter-6u5h (repeated printed page cited as plain "p. N"), reportsthatmatter-z4dy (Annex 7A not a heading; "Buenos / Aires").
- Noted: gqsy.9.2 (verdict), owre (corpus measurement), efso (16 shifted ids; decide before Chapters 11-12), oyr5 (472 budget breakdown).

## Lessons

On #446: the id-reuse check is blind to reference lists; diff `paragraphToSection` per report to see which published ids changed chapter.

## Getting better faster

- What slowed me: `pnpm ingest try` over 23 reports took about 40 minutes and does not report section moves, so I diffed the section lists with my own script against a second trial worktree. Running checks in that second worktree while `try` ran gave one transient `cards --check` failure.
- What would have caught finding 1 directly: `aliases generate` printing every published id whose section changed, with old and new text side by side. That is what c99s asks for.
- Proposal: `pnpm ingest try` prints a per-report section diff (slugs gained or lost, and paragraphs moved between sections), as the 2026-11-13 lesson from the author also asks. It is a small addition over the `meta.json` files `try` already renders.
