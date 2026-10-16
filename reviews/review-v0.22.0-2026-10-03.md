# Agent review of the v0.22.0 PRs (2026-10-03)

Reviewer: Opus agent, read-only. No PR is HELD. Most need one exact fix at merge time. Five problems only show up once everything is merged; they are listed under "Found only by the trial merge". Out of scope: Duelfer (site #182, ingest #22) and anything opened after the a8l and 38s.15 additions below.

Scope as reviewed:

- Ingest #55 to #60.
- Site #270 to #276.
- Report PRs: challenger-accident #9 and #10, us-deepwater-horizon #7 and #8, us-columbia-shuttle-accident #8, uk-hillsborough-panel #10, jack-smith-report #9, us-psi-financial-crisis #12, us-lehman-examiner #9, uk-leveson-inquiry #8.
- 38s.15 (site #275 and the six `adjudicated.yaml` PRs) and a8l (ingest #60, Hillsborough #10, site #276) were added mid-review at the coordinator's request.

Neither repo has CI, so everything was trial-merged in scratch clones under `/private/tmp/claude-501/-Users-rgrp-src-reportsthatmatter/c2584b47-3b47-4b84-8911-b50684365c43/scratchpad/rv22/`, on top of v0.21.0 (ingest `c04ee4f`, site `63ebf7de`):

- **`ingest`, branch `int22`:** #57 squashed, #58 and #59 cherry-picked (the equivalent of `--onto`), then #56, #55 and #60 squashed with the conflict resolutions below, and `dist` rebuilt. `check-dist` is clean, typecheck is clean and 776/776 tests pass.
- **`site`, branch `int22`:** every site PR merged, with `@rtm/ingest` linked to `int22`. Typecheck is clean. 668/669 tests pass; the failure is #273's help import (fixed below).
- **`reports/`:** all 13 report repos at their v0.21.0 heads with every report PR merged.
- **Variant builds** (`ing-v021`, `ing-57`, `ing-56`, `ing-55`) for attribution:
  - The v0.21.0 re-ingest reproduces every committed `full.md` byte for byte.
  - The production drift table matches locally for the three unmoved reports (PSI, 9/11 and Saville are `current`).

## Verdicts

| PR | Verdict | Reason or exact fix |
|---|---|---|
| ingest #57 page-break look-past (y7ix, hfrd) | SHIP | **Moves 9 reports,** exactly the set it claims: Challenger, Columbia, Jack Smith, Litvinenko, Chilcot, Leveson, Deepwater, Lehman and Philip Morris. PSI, Saville, 9/11 and Hillsborough are byte-identical. Rules-only referee eval with `--strict-judge` reproduces its numbers: dev 3/80, held-out 12/194. **One wrong join, Deepwater pp.83-84** (bead it, not blocking): "calculation of the worst-case discharge scenario…" (p.84, note 158-3) is joined onto "…drilling of the Macondo well.*" in the NEPA paragraph. Its true head is two paragraphs later ("Oil Pollution Act… contractual agreements, a"), which is left dangling. The paragraph is now out of reading order, and Deepwater's `note-reference-sequence` reads 3 instead of #56's 1 (ch.3 notes 158 before 155-157). I scanned every #57 join that skips a body-like block in all 9 reports; the rest skip captions, credits and sidebars, as intended. |
| ingest #58 month filter (3ezs) | SHIP-WITH-FIX | Stacked on #57. **Squashing it after #57 has squashed applies #57 twice.** In the trial, a plain squash of #59 duplicated `NUMBERED_PARAGRAPH`, `OPENS_LOWER`, `words` and a function in `src/paragraphs.ts` with no conflict reported, and 40 test files failed. After #57 squashes: `git rebase --onto origin/main pagebreak-upstream-y7ix linker-month-block-3ezs`, retarget to main, then squash. Effect as claimed: Jack Smith p.167 `predecessor.[^10]`. |
| ingest #59 photoCredits (sbnn) | SHIP-WITH-FIX | Same stack fix as #58: `git rebase --onto origin/main pagebreak-upstream-y7ix figure-credits-sbnn` (after #58, rebase onto the new main). Deepwater furniture-paragraph goes back to 25. |
| ingest #56 layoutEndnotes (b89, izw, kgz8) | SHIP-WITH-FIX | **`dist/` is not committed** (12 files, including the new `layout-endnotes.*`), so `pnpm release` refuses. After squashing, run `pnpm build` and commit `dist`. Moves Deepwater and Columbia only; the PRs' `full.md` files equal the #56-only re-ingest byte for byte. **#56 and #57 do not interact:** #57's joins in both reports are the same with and without #56, and the 1,494 and 305 linked markers are unchanged. Three side effects the PRs do not mention: (1) **Deepwater loses 30 page anchors** (`#page-313` to `#page-355`, the Endnotes appendix pages: page markers 364 to 334) and Columbia loses `#page-120`. Aliases do not cover page anchors (bead). (2) `aliases generate` aliases the old `endnotes` section to `chapter-six-…`, but the heading and preamble now sit in **chapter ten** (fix in the integrator steps). (3) The Deepwater highlight id `frequency-unannounced-inspections-plummeted-108` moved (its bare "108" is now a linked marker), so `pnpm editorial`, `pnpm marks check` and `pnpm posts --check` fail (fix below). |
| ingest #55 visionStructure (jsqw) | SHIP-WITH-FIX | Needs the rebase onto v0.21.0. Exact resolution below; `dist` is rebuilt by the build. On v0.21.0 alone it typechecks and passes 743 tests. Only Challenger moves. Its `hybrid.md` (178 vision pages, same reasons) is identical to the PR's. 9/11, Saville and Hillsborough (cleanEdition) are byte-identical, so the union with #53's `source` changes nothing. Two budget changes in #270 are needed (see the #270 row). |
| ingest #60 typographicHeadings (a8l) | SHIP-WITH-FIX | Conflicts with #55 and #56 in `src/define.ts`, `src/passes.ts` and `src/pipeline.ts`. Keep both sides everywhere, except `ResolvedPasses.layoutMarkers`: keep #56's `scope: "page" \| "document" \| "chapter"` and drop #60's two-scope line. Rebuild `dist`. **Verified:** only Hillsborough moves. 9/11 and Saville (`fillGaps` users) are byte-identical, and `ingest check` passes for all 13 against the pre-#60 baselines. Hillsborough's re-ingest equals both hillsborough#10's and #276's `full.md`. Held-out shadow F1 is 86.8 to 91.1, as claimed. |
| site #270 Challenger provenance and budgets (jsqw) | SHIP-WITH-FIX | Conflicts with main in the Challenger blocks of `quality-budget.yaml` and `oracle-budget.yaml`. #270 was measured on v0.20.0, so two budgets fail at the integrated ingest. Take #270's values, plus:<br>(1) Quality `bare-marker-after-quote: 19  # why: reportsthatmatter-jsqw (the text linker no longer runs on vision blocks; markers after a closing quote it used to link, often wrongly, e.g. '[^2] 6' for note 26 on p.98, stay bare)`. Main's line is 2; #270 has no line for it. Measured: v0.21.0 gives 2, #55 alone gives 19.<br>(2) Keep main's `note-reference-sequence` line; ratchet takes it from 134 to 112.<br>(3) Oracle `markers-unlinked: 27` (not #270's 25), why `jsqw + 4ef1` (#55 alone on v0.21.0 gives 27).<br>Editorial passes. `cli.ts` writes `reference/vision/hybrid.{md,json}` as described; `hybrid.json` differs from the PR's by #57's joins (16 lines). |
| site #271, #272 (lessons) | SHIP | Lessons only. `lessons.md`: keep both sides. |
| site #273 `pnpm ship` (0c6i) | SHIP-WITH-FIX | Use it for this release, with the cautions in "pnpm ship". Fixes:<br>(1) `tests/help.test.ts` fails: add `import "./lib/help.mjs";` as the first import of `scripts/ship.ts`.<br>(2) **`verify-prod` runs `./scripts/verify.sh` without `VERIFY_SHARED=1`,** so its aggregate refuses the shared checkouts (yesterday's lesson). Either add `VERIFY_SHARED: "1"` to the verify-prod item's `env` in `scripts/lib/ship.ts`, or export `VERIFY_SHARED=1` before `pnpm ship --yes`. |
| site #274 note-reference-sequence (6hbt) | SHIP | `lessons.md` conflict only. Counts after the re-ingest: Challenger 112, Columbia 7, Deepwater 3 (#57's wrong join, not 1). Ratchet them. |
| site #275 mini-references, referee eval (38s.15) | SHIP | Merges cleanly onto main and onto #272, #273 and #274. Its test passes, and scorecard still builds. At the integrated ingest (rules only):<br>• Default judge: dev 3/82, held-out 17/229, PDF-only dev 9/86, PDF-only held-out 10/86.<br>• `--strict-judge`: dev 3/80, held-out 12/194. Like for like with #57's numbers, so no held-out drop.<br>The eval needs `us-duelfer-report` as a sibling checkout, because the held-out set lists it. |
| site #276 Hillsborough headings (a8l) | SHIP | **Merge after the pin bump.** `full.md`, `corpus-baseline.json` and `aliases.yaml` reproduce exactly: `aliases generate --all --old-ref 63ebf7de` leaves its committed `aliases.yaml` and `published-ids.txt` with 0 changed lines, so the generate bug does not bite. The matcher change is covered by its test. `lessons.md`: keep both sides. |
| challenger-accident #9 | SHIP | Merge conflict on `baseline.json`: take main's; the re-ingest regenerates it. Golden pages 2/7 pass, as claimed. |
| challenger-accident #10, us-deepwater-horizon #8, jack-smith-report #9, us-psi-financial-crisis #12, us-lehman-examiner #9, uk-leveson-inquiry #8 | SHIP | Each adds only `reference/adjudicated.yaml`, with no conflict with #9 or #7 in the same repos. No report text moves. |
| us-deepwater-horizon #7 | SHIP-WITH-FIX | `baseline.json` conflict: take main's. **Golden p.17 fails `verify` at the integrated ingest** (#56 meets #57): #57 joins the p.18 continuation onto "Outside in the Gulf…", so its notes 2-3 sit on p.17's second block. The check allows a next page's markers only on a page's last block (qzix). Set p.17 to `xfail: reportsthatmatter-qzix` and `xfail_only: [markers]`. Verified: blocks, must_contain and must_not_contain now pass, and golden turns green. |
| us-columbia-shuttle-accident #8 | SHIP | Default branch `master`. Conflicts on `baseline.json` and `full.md`: take master's, then re-ingest. **Held-out WER 0.44% to 0.66% is the metric, not a drop:** with the 305 `[^N-C]` markers turned back into bare numbers, Columbia scores WER 0.4%. Boundary F1 and joins rise; adjudicated wrong 6/29 to 5/29. Record the WER with that note. Quality `quote-share-parity` 3 to 4 fails (raise below). |
| uk-hillsborough-panel #10 | SHIP | `full.md` and `baseline.json` reproduce exactly at the integrated ingest. Golden 7/7. |

## Found only by the trial merge

1. **Stacked squash duplicates code** (#58 and #59 after #57): no conflict is reported, but nothing compiles. Rebase `--onto` first.
2. **Budgets #270 could not see**: Challenger `bare-marker-after-quote` 19 and oracle `markers-unlinked` 27.
3. **Deepwater golden p.17** fails only with #56 and #57 together.
4. **Deepwater editorial, mark and queue item** `frequency-unannounced-inspections-plummeted-108`: the id moved under #56.
5. **Deepwater pp.83-84 wrong join** (#57). It also costs 2 in Deepwater's `note-reference-sequence`.

Also seen, not blocking:

- **`aliases generate` false alarm:** it prints `MOVED-OUT ID and` for Challenger, because `citers()` matches a paragraph id that is the plain word "and" anywhere in `editorial/challenger-accident.yaml` and `share-quotes.yaml`. Ignore it; worth a bead.
- **9/11 adapter number:** the 9/11 edition-adapter "join, all rows" falls 1.3 pts. It is an agreement figure, not a score, and 9/11's text is byte-identical.

## Conflict resolutions

**Ingest #55 onto `main` (with or without #56):**

- `src/paragraphs.ts`: one field, `source?: "edition" | "pdf" | "vision";`, with one doc comment covering both passes.
- `src/pipeline.ts` imports: `import { assembleEdition, fillGaps, fillPrintedGaps, type EditionReport, type PrintedPage } from "./edition";` followed by `import { VisionHybrid, type VisionReport } from "./vision/hybrid";`.
- `src/pipeline.ts`, after #56, the text linker:
  ```ts
  // A labelled note ("3-5", `layoutEndnotes`) is not what a bare [^3] would open.
  const defined = new Set(notes.filter((note) => !note.label).map((note) => note.number));
  const known = pipelineKnown ? new Set([...pipelineKnown].filter((n) => defined.has(n))) : defined;
  body = hybrid ? linkOutsideVision(body, outBlocks, (text) => linkInlineMarkers(text, known)) : linkInlineMarkers(body, known);
  ```
- `dist/*`: take either side; the build overwrites it.

**Ingest #60:** keep both sides in `define.ts` (imports and `ResolvedPasses`, with #56's three-scope `layoutMarkers`), `passes.ts` (the `Pass` union) and `pipeline.ts` (the `IngestResult` fields and the return spread).

**Site:**

- `docs/design/lessons.md`: #270 to #276 all touch it; keep both sides.
- `reports/quality-budget.yaml` and `reports/oracle-budget.yaml`: the Challenger block, as in the #270 row.

**Reports:** take the base branch's `baseline.json` and `full.md`; the re-ingest replaces them.

## Merge order

1. **Ingest:**
   - Squash #57.
   - `git rebase --onto origin/main pagebreak-upstream-y7ix linker-month-block-3ezs`, squash #58. Then the same for `figure-credits-sbnn` and squash #59.
   - Squash #56.
   - Rebase #55 onto main with the resolution above, then squash.
   - #60 with its resolution.
   - `pnpm build`, commit `dist`, then `pnpm check-dist` clean, `pnpm typecheck && pnpm test` (776), `pnpm release 0.22.0 --dry-run`, `pnpm release 0.22.0`.
2. **Report repos** (merge commits; derived files from the base branch; do not bump pins by hand, `pnpm ship` does that):
   - challenger-accident #9, #10.
   - us-deepwater-horizon #7 (plus the golden p.17 fix), #8.
   - us-columbia-shuttle-accident #8 (master).
   - uk-hillsborough-panel #10.
   - jack-smith-report #9, us-psi-financial-crisis #12, us-lehman-examiner #9, uk-leveson-inquiry #8.
   - Then pull the shared checkouts with `--ff-only`.
3. **Site:**
   - #271, #272, #274, #275, then #273 with the help-import fix, plus `VERIFY_SHARED` in verify-prod.
   - Then the pin-bump PR to v0.22.0.
   - Then #270 (resolved and raised as above) and #276.
   - Plus a small PR with the Deepwater editorial fix and the Columbia budget raise, or include them in the derived-files PR.

## What moves (ids lost = published ids that are now `unmatched`, against the deployed 63ebf7de)

| report | change | paragraphs (corpus) | ids lost | new aliases | sections |
|---|---|---:|---:|---:|---|
| challenger-accident | #55, #57 | 3,221 → 2,846 | 382 | 148 | `terrestrial-environment-climatic-criteria` and `space-shuttle-program-thermal-interfaces-design-data-book` split out of `thiokol` |
| us-deepwater-horizon | #56, #57, #59 | 2,303 → 1,729 | 522 | 124 | `endnotes` folds into chapter ten (alias to fix); `notes-not-linked-in-the-text` unmatched; one new subsection |
| columbia-accident | #56, #57 | 3,358 → 2,910 | 366 | 95 | 8 `endnotes-for-chapter-N` aliased to each chapter's last section; `notes-not-linked-in-the-text` unmatched; `11-genesis-of-the-space-transportation-system` new |
| uk-hillsborough-panel | #60 | 2,768 → 2,758 | 20 | 65 | as in #276 (already committed) |
| uk-leveson-inquiry | #57 | 12,758 → 12,750 | 0 | 10 | |
| us-v-philip-morris | #57 | 5,897 → 5,893 | 0 | 4 | |
| us-lehman-examiner | #57 | 978 → 975 | 0 | 3 | |
| jack-smith-vol1 | #57, #58 | 432 → 430 | 1 (`page-9-added-reporting-requirement`) | 2 | |
| litvinenko-inquiry | #57 | 2,148 → 2,147 | 0 | 1 | |
| uk-chilcot-inquiry | #57 | 951 → 950 | 0 | 1 | |
| PSI, Saville, 9/11 | none | | | | byte-identical; production already `current` |

**`aliases generate` dropped no lines.** Every removed alias key is now `unmatched` (its target vanished with the old note paragraphs: 8 Challenger, 9 Columbia, 40 Deepwater) or a live id again (2 Deepwater, and Leveson `appendix-4` and `part-g-chapter-3`). No `published-ids.txt` line was removed, and reports whose text did not move show 0 changed lines. `pnpm aliases check` passes.

## Budgets

**Raises (hand, with `# why:`):**

- Challenger, quality `bare-marker-after-quote: 19`: jsqw, as above.
- Challenger, oracle `markers-unlinked: 27`: jsqw + 4ef1.
- Columbia, quality `quote-share-parity: 4`: `# why: reportsthatmatter-izw (the ENDNOTES pages' note text left the body, so quotations are 9.6% of blocks on even pages and 13.1% on odd; no quotation was added or lost)`.

**Ratchets (`pnpm quality ratchet`, dry run read):**

- Challenger: `note-reference-sequence` 134 to 112.
- Columbia: severed-into-quote 59 to 54, severed-paragraph 74 to 70, bare-footnote-marker 179 to 2, bare-marker-after-quote 6 to 0, note-marker-unpaired 13 to 0, note-reference-sequence 20 to 7, note-text-in-body 29 to 7, furniture-paragraph 75 to 61, contents-entry-without-heading 73 to 71, heading-function-word 3 to 2.
- Deepwater: severed-into-quote 9 to 6, bare-footnote-marker 1,021 to 1, bare-marker-after-quote 193 to 0, note-marker-unpaired 416 to 0, note-reference-sequence 756 to 3, note-text-in-body 269 to 8, rendered-ol-words-share 4 to 1, numbered-prose-list 1 to 0, quote-share-parity 2 to 1.

**Oracle (`--ratchet-oracle`):**

- Columbia: markers-unlinked 297 to 3, paragraphs-oversplit 533 to 277, quotes-spurious 290 to 235.
- Deepwater: markers-unlinked 1,445 to 2.
- Leveson: oversplit 2,089 to 2,081.
- Philip Morris: oversplit 956 to 952.

**Anchors (`--ratchet`):**

- Jack Smith: blocks-wrong 53 to 49.
- Challenger: markers-wrong 23 to 20, blocks-wrong 83 to 82.
- Leveson: blocks-wrong 7 to 5.
- Columbia: markers-wrong 1 to 0.
- Deepwater: blocks-wrong 5 to 0.

`anchors --check` passes for all 13 before ratcheting.

**Trial results after the fixes:**

- Passing: `ingest verify` (all 13), `quality check` (after the three raises), `corpus check` (after accepting the 9 changed reports; Hillsborough's baseline comes with #276), editorial, `marks check --all` (732 references replayed against the trial prerender, none stops anchoring), `posts --check` and `aliases check`.
- **Score, dev:** no recorded value falls. Saville and 9/11 shadows are +0.0 to +0.4; Philip Morris joins +0.3.
- **Score, held-out:** no drop except Columbia WER, which is explained above. Columbia F1 is +0.2 and joins +1.0. Hillsborough shadow F1 is +4.4. Chilcot is up; its large marker deltas are against an old `scores.json` value. Duelfer has no reference, so `pnpm score --holdout` exits 1 on it, as on main.

## D1 estimate (`publish-report <id> --dry-run --base https://reportsthatmatter.org`, against the trial prerender)

| report | row writes |
|---|---:|
| us-deepwater-horizon | 6,644 |
| challenger-accident | 3,983 |
| columbia-accident | 2,918 |
| uk-hillsborough-panel | 428 |
| uk-leveson-inquiry | 194 |
| jack-smith-vol1 | 68 |
| us-lehman-examiner | 41 |
| us-v-philip-morris | 38 |
| uk-chilcot-inquiry | 11 |
| litvinenko-inquiry | 11 |
| **total, 10 drifted reports** | **14,346** (15,781 with ship's 10% margin), plus a few rows for `seed-highlights` |

**Recommendation: ship tonight.** With about 29k row writes left, this leaves about 13k of headroom. Two conditions:

1. Nobody else publishes before you.
2. `pnpm ship` must see the real usage: export `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID`, or pass `--d1-limit 29000`. Without either it compares against the full 100,000 and would pass even a release that does not fit.

If the gates are not through by about 23:00 UTC, or `--preflight` shows less than 18k left, wait for 00:00 UTC. The reindex is resumable, so a quota stop mid-reindex costs only stale search until the reset.

## `pnpm ship` (#273): safe for this release, with these cautions

Read in code and tested by `--plan` in the trial site.

**Production is gated correctly:**

- Publish, deploy, reindex, seed and verify-prod are `kind: "prod"`. Without `--yes` the run stops before publish (`awaiting-yes`, exit 3), and `--yes` is not saved in the state.
- One `--yes` runs all five in a row, so give it only after reading `--plan`, the `status` drift table and the `d1-estimate` output.

**Watch these:**

1. **Typos run the release.** Unknown flags are ignored, so `pnpm ship --pln` runs from step 1:
   - It bumps pins and runs `pnpm install` in every report repo, the shared checkouts included, even without `--shared`.
   - The re-ingest then refuses without `--shared`, leaving dirty `package.json` and lockfiles.
   - Type `--plan` carefully. Follow-up: reject unknown flags, and make pin-bump honour `--shared`.
2. **Pass `--old-ref 63ebf7de` on the first run.** The default resolves `origin/main` when the run starts, which will be after the PRs have merged, not the deployed commit. The trial shows the explicit ref reproduces #276's aliases exactly.
3. **D1:** see the D1 estimate section (token or `--d1-limit 29000`).
4. **`VERIFY_SHARED=1`** for verify-prod (fix 2 in its row), or it fails after publishing; then `pnpm ship --redo verify-prod`.
5. **Guard:** every report repo must be on main and clean. Merge the report PRs first (default branch is `master` for Columbia and Leveson) and pull with `--ff-only`. With other agents running, check `git status` in the shared checkouts first.
6. **Hillsborough** comes from #276, not the re-ingest. Ship's aggregate will not list it as "changed", but the `status` step lists it as drifted, so it is published and reindexed. Its corpus and aliases are already committed.
7. **Editorial gate:** `--ack editorial` runs `pnpm posts --drop-stale`, which re-adds the Deepwater queue item at the end (scheduled 2026-12-08 instead of 2026-11-16). Set `scheduled: 2026-11-16` back by hand if wanted.

## Integrator steps

1. **Ingest release** as in the merge order.
2. **Merge the report PRs:**
   - Merge the report PRs, plus Deepwater golden p.17: `xfail: reportsthatmatter-qzix`, `xfail_only: [markers]`.
   - Then merge the site PRs up to and including the pin bump, then #270 and #276.
3. **Start ship:** `pnpm ship --plan`, then `pnpm ship --shared --old-ref 63ebf7de` (with `CLOUDFLARE_API_TOKEN`, `CLOUDFLARE_ACCOUNT_ID` and `VERIFY_SHARED=1` exported).
4. **At the baseline gate:**
   - Expect 9 reports: Challenger, Columbia, Jack Smith, Litvinenko, Chilcot, Leveson, Deepwater, Lehman and Philip Morris.
   - Read the diffs, including Deepwater p.83/84 (known; bead it).
   - Then `pnpm ship --ack baseline`.
5. **After the aliases step:** set Deepwater's `sections:` entry to `endnotes: chapter-ten-american-energy-policy-and-the-future-of-offshore-drilling`. The re-ingest pages show that is where the heading went; a later `generate` will undo it, so bead the matcher.
6. **At the corpus gate:** accept the 9 changed reports (ids lost as in the table), then `pnpm ship --ack corpus`.
7. **At the editorial gate:**
   - In `editorial/us-deepwater-horizon.yaml`, set highlights[10] to `paragraph: frequency-unannounced-inspections-plummeted-although`.
   - Then `pnpm ship --ack editorial`, which runs `posts --drop-stale`.
   - `marks check --all` then passes.
8. **At the checks stop:** add the three budget raises, then re-run ship.
9. **Commit and pull:**
   - Commit derived files per repo (report repos: `full.md`, `baseline.json`, `fidelity.md`, Challenger's `reference/vision/hybrid.*`). Site: `full.md`, aliases, `published-*`, corpus baseline, budgets, editorial, queue. Merge them, then pull with `--ff-only`.
   - Then `pnpm ship` passes `committed` and `status` (10 to publish) and `d1-estimate` (about 14.3k).
10. **Production:** `pnpm ship --yes`. If a commit returns 500 (yesterday's lesson), re-run `pnpm ship --yes`; it resumes.
11. **Record:**
    - `record` runs `quality ratchet --record` and `scorecard --record`.
    - Note the Columbia WER artefact and the Hillsborough shadow 91.1 in the changelog.
    - File beads for: the Deepwater wrong join, Deepwater and Columbia page anchors, the section-alias target, the `citers("and")` false positive, ship's unknown flags and pin-bump without `--shared`.
