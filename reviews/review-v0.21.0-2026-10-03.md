# Agent review of the v0.21.0 PRs (2026-10-03)

Reviewer: Opus agent, read-only. No PR is HELD. Several need an exact fix at merge time; three cross-PR problems only show up once everything is merged (listed under "Found only by the trial merge"). Site #182 and ingest #22 (Duelfer) are held and out of scope.

Neither repo has CI, so I trial-merged everything in scratch clones under `/private/tmp/claude-501/-Users-rgrp-src-reportsthatmatter/c2584b47-3b47-4b84-8911-b50684365c43/scratchpad/rv21/`:
- `ingest`, branch `int2`: #50, #51, #52, #53 (conflict resolved) and #54, squashed in that order, with `dist` rebuilt. Typecheck clean, 728/728 tests.
- `site2`: every site PR merged, with `@rtm/ingest` linked to `int2`. All 13 reports re-ingested from scratch clones of the report repos (`reports/`, Hillsborough at its PR #9), then aggregate, prerender, `aliases generate --all`, corpus check, quality check, anchors check, editorial, posts check and an offline marks check. Typecheck clean, 631/631 tests (after the help-import fix below).

## Verdicts

| PR | Verdict | Reason or exact fix |
|---|---|---|
| ingest #50 table page stamps (8x9n) | SHIP | `dist` matches its build. Only 9/11 moves (+16 paragraphs, table cuts and stamps). Oracle: paragraphs-merged 2209 to 2085, paragraphs-oversplit 95 to 140 (not gated), unlocated 377 to 208. Golden unchanged (5/6). Anchors: 9/11 markers-stacked 5 to 0. **Conflicts with #53** in `src/edition.ts` and `dist/edition.js` (see the hazards). |
| ingest #51 referee cache and confidence tiers (38s.11) | SHIP | `dist` matches its build. Join decisions are unchanged; only `reason` strings and the new `confidence` field differ. With #51 alone, no report's output or oracle moves. 14 new tests. |
| ingest #52 marker after a quote (4ef1) | SHIP-WITH-FIX | `dist/layout.js` is not rebuilt (the PR says so). After squashing, run `pnpm build` and commit `dist/layout.js`, or `pnpm release` refuses. **Its blast radius is wider than the PR says.** The layout parse feeds the oracle for every report, not just the six with layoutMarkers. The oracle moves in three reports that do not declare layoutMarkers: **Challenger** markers-unlinked 23 to 24 and **Jack Smith** 33 to 36, both **over budget, so `verify` fails**; Saville markers-spurious 56 to 31 and expected markers 1404 to 1429 (an improvement). Rendered text moves only for Litvinenko and Leveson, as claimed. The PR calls the Litvinenko "sections gone: appendix-10" a quirk of the try tool. It is not: after the re-ingest, the corpus check really folds that section into appendix-9 (sliver rule), so its URL moves. `aliases generate` records `appendix-10-list-of-inquiry-staff-and-counsel: appendix-9-list-of-witnesses`. |
| ingest #53 hybrid gap-fill (ivg.3) | SHIP-WITH-FIX | `dist` matches its build. Conflict with #50 (exact resolution in the hazards). Verified independently with #50, #51, #52 and #54 present: `ingest check` moves no other report because of #53 (Saville is byte-identical, `markdownSha` unchanged). 9/11 and Saville oracle counts and golden pages are identical. One informational change: #53 alone takes 9/11's oracle "unlocated paragraphs" from 377 to 409 (with #50: 208 to 240). Hillsborough at PR #9 reproduces the claims: golden 5/5, headings-missed 144, markers-unlinked 24. Its `baseline.json` and `full.md` match the integrated ingest byte for byte. |
| ingest #54 unmarked pages (d662) | SHIP | `check-dist` is clean (the hand-built `dist` equals `pnpm build`). It merges cleanly onto #50 and #53, the build leaves `dist` unchanged, and 728 tests pass. Moves exactly the seven expected reports (page markers only), plus Leveson paragraph 12884 to 12883 (the `l` id). No oracle or golden change except Leveson unlocated 4868 to 4867. Hillsborough as a hybrid does not move. |
| site #260 scheduled poster (y2t.4) | SHIP-WITH-FIX | **Safe: it cannot post or loop.** No secrets: dry run, no network request (run here: "nothing due until 2026-10-14", queue untouched). `POSTER_LIVE=true` without secrets: exits 1. A manual run defaults to a dry run. The triggers are only `schedule` and `workflow_dispatch`, and a push made with `GITHUB_TOKEN` starts no workflow, so the commit-back cannot loop. Other guards: one concurrency group, one post per UTC day, and a reconcile against the account's feed before posting. Fixes: (1) with #267 merged, `tests/help.test.ts` fails; add `import "./lib/help.mjs";` as the first import of `scripts/post-next.mjs`. (2) `pnpm-lock.yaml` conflicts with #265; regenerate it with `pnpm install` and commit, or the workflow's `pnpm install --frozen-lockfile` fails. (3) After the Hillsborough re-ingest, `pnpm posts --check` fails on `uk-hillsborough-panel:box-cctv-monitors-neither-chief:f3cb9a3b`, which would turn the daily run red. Run `pnpm posts --drop-stale` (a plain `pnpm posts` only reports it). |
| site #261 marks check (p4h6) | SHIP-WITH-FIX | **Read-only, confirmed in code.** `readOnly` refuses anything but a single `SELECT` (and any SQL file) before it reaches wrangler. The export holds no actor hashes and is cached in gitignored `build/`. `--no-marks` against the release: 570 references, none broken. Fixes: (1) it is based on an older `main` and **conflicts with `main`** in `docs/release-checklist.md`. Keep main's steps 4 and 5 (`--shared`), insert its step 7 (marks check), and renumber main's 7 and 8 as 8 and 9 (`VERIFY_SHARED=1`). (2) With #267, add `import "./lib/help.mjs";` before the `if` in `scripts/marks.mjs`. |
| site #262 marks follow aliases (j53o) | SHIP | Stacked. After #261 squashes: `git rebase --onto origin/main marks-check-p4h6 marks-alias-j53o`; for `lessons.md`, keep both sides. Follow-up, not blocking: `/reports/:id/marks` now parses `meta.json` on every request (Leveson 1.5 MB). The section route already does the same, so this doubles the parse per page view. |
| site #263 lessons (8x9n) | SHIP | lessons only. |
| site #264 anchors check (s24x) | SHIP | Behaviour as described. Its change to `aliases generate` causes the alias drop below; the fix is assigned to #267. |
| site #265 referee CLI (38s.11) | SHIP-WITH-FIX | Based on an older `main`; it conflicts in `scripts/ingest/cli.ts`, `scripts/ingest/README.md`, `lessons.md` and `pnpm-lock.yaml`. In `cli.ts`, keep both header lines and both command branches. Keep main's `named` filter and add `"referee"` to main's `needsPipeline` list (which has `"recheck"`). In the README, keep main's paragraphs and insert the referee paragraph before "Golden pages". Regenerate the lockfile. It imports referee exports, so **merge only after the v0.21.0 pin bump**. Follow-up: `pnpm ingest referee` writes `referee/pagebreaks.json` into the report repo without the shared-checkout guard. Default model `claude-haiku-4-5`. |
| site #266 bare-marker-after-quote (4ef1) | SHIP | Conflicts with #268 in the Hillsborough block of `quality-budget.yaml`: take `bare-footnote-marker: 13` and `bare-marker-after-quote: 0`. After the re-ingest, `quality ratchet` lowers Litvinenko 41 to 0, Leveson 4 to 2, and Leveson quote-share-parity 2 to 1. |
| site #267 aliases with the old pin (hxo4) | SHIP-WITH-FIX | Stacked on #264. After #264 squashes: `git rebase --onto origin/main anchors-check-s24x feat/aliases-old-pin`; for `lessons.md`, keep both sides. (1) The help-import test fails for `post-next` and `marks` (#260, #261); fix as above. (2) **Alias lines are dropped.** `generate` now starts from `--old-ref`'s `aliases.yaml` (#264), so it discards this PR's own committed additions. The release-time `pnpm aliases generate --all` (old ref = the deployed v0.20.0 commit) removed the Chilcot section alias `development-of-uk-strategy-and-options-911-to-early-january-2002` and `unmatched_sections: [notes-not-linked-in-the-text]` for Chilcot and Lehman. `pnpm aliases check` then fails both. Workaround: after generating, `git checkout HEAD -- reports/uk-chilcot-inquiry/aliases.yaml reports/us-lehman-examiner/aliases.yaml` (neither text moves in this release). Rule: after `generate`, a report whose text did not move must show no removed lines in `git diff`. File a bead: the base should be the committed (`HEAD`) file, not `--old-ref`'s. |
| site #268 Hillsborough hybrid (ivg.3) | SHIP-WITH-FIX | Its `full.md` equals the re-ingest at the integrated ingest, but take the re-ingest, not the PR's file. Fixes: (1) `quality-budget.yaml` conflict with #266, as above. (2) **Anchor budgets.** On the hybrid text, `pnpm ingest anchors --check` gives markers-wrong 15 (budget 0), markers-stacked 1 (0), blocks-wrong 86 (69). `--ratchet` only lowers, so it cannot fix this. Read `pnpm ingest anchors uk-hillsborough-panel`, then set those three with `# why: ivg.3`. Many look like the check's own misses (the Report summary repeats chapter text: pp.13-25 "N words before the start", and figure captions in edition order). The one-page offsets at pp.34, 74, 107, 128, 174, 316 and 366 need a look. (3) **REUSED ID** `south-yorkshire-police-response-2`. Nothing in editorial or the queue cites it. Check D1 marks with `pnpm marks check uk-hillsborough-panel`, then `pnpm aliases generate uk-hillsborough-panel --accept-reuse`. (4) `pnpm posts --drop-stale`, as above. Generate also adds 390 aliases and 19 published sections. |
| site #269 anchor budgets (d662) | SHIP-WITH-FIX | Stacked on #264. After #264 squashes: `git rebase --onto origin/main anchors-check-s24x gapfill-d662` (lessons: keep both sides). Its Hillsborough numbers describe the PDF build and are replaced as in #268. Every other report is within its budgets on the re-ingested text. Ratchet 9/11 markers-stacked 5 to 0. Its Leveson `l` line is regenerated by `generate` anyway (`unmatched: - l`). |
| uk-hillsborough-panel #9 (ivg.3) | SHIP | Pinned at v0.20.0 but uses `footnoteNumbers`: bump to v0.21.0 in the same merge. Re-ingest at the integrated ingest is byte-identical to the PR's `full.md` and baseline. |
| uk-litvinenko-inquiry #9 golden p.22 | SHIP (with the pin bump) | At v0.20.0 the new entry fails `verify` outright (notes [34,35], expected [33,34,35]; `xfail_only: [blocks]` does not cover it). At the integrated ingest it is a known failure on blocks only (`men`, the "Whistleblower" heading). Merge it with the v0.21.0 pin bump, not before. |

Out of scope, still in progress (created today): ingest #55, challenger-accident #9 and site #270 (Challenger vision adapter, jsqw). If ingest #55 joins v0.21.0, it is unreviewed here; Challenger also moves under #52 and #54.

## Found only by the trial merge

1. **#52 fails `verify` for Challenger and Jack Smith** through oracle budgets (markers-unlinked +1 and +3). Read `pnpm ingest verify <id> --findings`, then raise the budgets with `# why: 4ef1 (layout parse now joins a raised marker overlapping its line)`, or file a bead if the new unlinked markers are real bare numbers.
2. **`aliases generate` drops the Chilcot and Lehman lines that #267 committed** (#264's `--old-ref` base). Restore them as in #267's row.
3. **Help-import test** fails once #267 meets #260 and #261: two one-line imports.
4. **Hillsborough anchor budgets** cannot be ratcheted into passing; they need a reasoned raise.

## Merge order

1. **Ingest:**
   - Squash #50, #51 and #52.
   - Squash #53. Resolve its `src/edition.ts` hunk in `assembleEdition` like this, and keep the rest of #50's row code:
     ```ts
     const placed = (p: number | undefined) =>
       p === undefined ? {} : { at: { volume: pages[p].volume, pdfIndex: pages[p].pdfIndex, printed: typeof printedAt.get(p) === "number" ? (printedAt.get(p) as number) : null } };
     const p = placedAt.get(b);
     const source = block.source && "pdf" in block.source ? { source: "pdf" as const } : { source: "edition" as const };
     const common = { ...placed(p), ...source };
     ```
     The table row push becomes `asBlocks.push({ kind: "paragraph", text, ...placed(rowAt), ...source });`. Take either side of `dist/edition.js`; the build overwrites it.
   - Squash #54.
   - `pnpm build`, commit `dist` (`dist/layout.js` from #52, `dist/edition.js` from the resolution), and confirm `pnpm check-dist` is clean. Then `pnpm typecheck && pnpm test` (728 pass), `pnpm release 0.21.0 --dry-run`, `pnpm release 0.21.0`.
2. **Report repos:**
   - Bump the pin to v0.21.0 in all 13, and `pnpm -C <repo> install`.
   - Merge uk-hillsborough-panel #9 and uk-litvinenko-inquiry #9 with their bumps.
   - Re-ingest, read each diff, then `baseline`. Moves: Challenger, Columbia, Jack Smith, Litvinenko, Hillsborough, Leveson, 9/11, Deepwater, PSI. Chilcot, Saville, Lehman and Philip Morris are byte-identical (pin bump only).
3. **Site:**
   - #263 and #266.
   - #260, with the lockfile.
   - #261 (conflict with `main` resolved), then #262 rebased `--onto`.
   - #264, then #267 and #269, each rebased `--onto`. Add the help-import fix commit (or put it in whichever of #260, #261 or #267 lands last).
   - Pin bump to v0.21.0, then #265 (conflicts resolved, lockfile regenerated), then #268 (source parts only: editorial, PROCESSING, design note, budgets; take `full.md` from the re-ingest).
   - Then the integrator steps below. Then ship.
4. **Conflict hazards:**
   - `docs/design/lessons.md`: almost every site PR touches it; keep both sides.
   - `docs/release-checklist.md`: #261 against `main`, then #262.
   - `scripts/ingest/cli.ts` and `scripts/ingest/README.md`: #265 against #264 and `main`.
   - `pnpm-lock.yaml`: #260 against #265.
   - `reports/quality-budget.yaml`: #266, #268, #269.
   - `reports/anchor-budget.yaml`: #264, #267, #269, all stacked; take #269's, then the Hillsborough raise.
   - `src/edition.ts` and `dist/edition.js`: ingest #50 against #53.

## Integrator steps after the pin bump

1. `RTM_REPORT_DIRS=<worktrees> pnpm ingest run <id>` for the 9 moving reports (or `--shared`), then `pnpm ingest check`, read the diffs, and `pnpm ingest baseline <id> --shared`. Then `pnpm ingest aggregate --shared` and `pnpm prerender`.
2. `pnpm corpus check`, then `pnpm corpus accept`:
   - uk-hillsborough-panel: ids move in 18+ sections, about 20 new sections, and `chapter-2-the-moment-of` becomes `chapter-2-the-moment-of-1989`.
   - litvinenko-inquiry: appendix-10 folds into appendix-9.
   - uk-leveson-inquiry: `l` lost.
   - The others change in word count only.
3. `pnpm aliases generate --all`, once, against the final text. The old ref is the deployed v0.20.0 commit (`origin/main` at 2eda138e if nothing else deploys first); #267 renders the old side with v0.20.0.
   - Restore Chilcot's and Lehman's `aliases.yaml` from `HEAD`.
   - For the reused id: `pnpm marks check uk-hillsborough-panel`, then `pnpm aliases generate uk-hillsborough-panel --accept-reuse`.
   - `pnpm prerender`, then `pnpm aliases check` must be green.
4. `pnpm posts --drop-stale` (the Hillsborough item), then `pnpm posts --check`. `pnpm editorial` (passes in the trial).
5. `pnpm marks check --all --baseline <v0.20.0 prerender>`. Offline in the trial: 570 references, 0 broken. Production marks were not replayed.
6. Anchor budgets: `pnpm ingest anchors --all --ratchet` (9/11 stacked 5 to 0, plus whatever the 7 d662 reports improve). Then raise Hillsborough markers-wrong 15, markers-stacked 1 and blocks-wrong 86 with `# why: ivg.3` after reading them. Expect `--check` green except for those three.
7. Oracle budgets: raise Challenger markers-unlinked to 24 and Jack Smith to 36 with a reason (#52), after reading `--findings`. Hillsborough's come from #268 (headings-missed 144, markers-unlinked 24, note-off-page 1). Then `pnpm ingest verify` for all.
8. Quality ratchet: `pnpm quality check` passes on the trial. `pnpm quality ratchet` lowers Litvinenko bare-marker-after-quote 41 to 0, Leveson 4 to 2, and Leveson quote-share-parity 2 to 1. `--record` at acceptance.
9. Record the Hillsborough shadow score per 79ze (`docs/scores.json`). Then `VERIFY_SHARED=1 ./scripts/verify.sh`, deploy, and publish-report, reindex and verify-prod for the 9 moved reports.
10. Poster: nothing to switch on. The first item is due 2026-10-14. After merging, the manual workflow run (dry run) is the first real test of the workflow on a runner.
