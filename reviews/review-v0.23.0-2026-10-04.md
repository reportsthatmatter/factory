# Agent review of the v0.23.0 PRs (release batch B, 2026-10-04)

Reviewer: Opus agent. I did not touch production: no `wrangler d1 --remote`, no publish, no seed, no reindex, no deploy. Every D1 figure below is measured on a scratch local D1 or reasoned from #287's code and today's ledger.

**Verdict: ship v0.23.0 today, with the fixes below.** No PR is HELD except site #290 (January 6th), which must not be merged until its plate exists (z959): merging it fails `tests/plates.test.ts` on main. Everything else in its chain (ingest #66, report repo us-jan6-committee#1) ships. ingest #67 is low risk and ships too.

Scope as reviewed (heads at the time of the trial):

- **ingest:** #61 `resolve-passes-5xln` (b146950, includes my fixup), #62 `anchors-1004` (7cc8b76), #65 `ingest-folios-1004` (ea3c4dc, stacked on #62), #63 `pohorizon-passes` (e5e9133), #64 `noteorder-1004` (afcd787), #66 `edition-notes-after` (877dc86), #67 `sections-divider-h2` (92432c7). Excluded: #22 (Duelfer, parked).
- **report repos:** challenger-accident#11, jack-smith-report#10 and #11, uk-post-office-horizon-inquiry#1 (78afefa), us-911-commission#12, uk-litvinenko-inquiry#10, uk-hillsborough-panel#11, us-lehman-examiner#10, us-psi-financial-crisis#13, uk-leveson-inquiry#9 (base `master`), us-jan6-committee#1 (d8e37fd). New, opened by me: **us-deepwater-horizon#9** (golden p.17 xfail removal).
- **site:** #281 (try --base, findings diff, ingest link), #288 (folios CLI, based on #281), #280 (budgets; now carries all of this release's budget raises), #286 (note-order signal and derived files), #284 (Post Office Horizon Vol 1; head 77b7ca77 with my fixups), #290 (January 6th; head 1d105e42 with my fixup), and, added late at the coordinator's request, #289 (cards prune and ship `cards` step), #291 (strict ship flags), #292 (edge-cache key per deploy).
- **Deferred to v0.24.0:** Grenfell (ingest#68, uk-grenfell-tower-inquiry#1, site #293). It arrived when this review was nearly done; it would mean a fourth full re-ingest round, and #68 is stacked on #63, so it needs its own `--onto` after #63 squashes. Opt-in passes only, so low risk, but not reviewed here.

Neither repo has CI. I trial-merged everything in scratch worktrees, on top of v0.22.0 plus batch A:

- **ingest** `ingest-reviewB-1004`, pushed as **`reviewB-trial-1004`** (recipe, not for merging): #61, #62 squashed; #65 as its own commit (cherry-pick, the `--onto` equivalent); #63, #64 squashed with the resolutions below; #66, #67 squashed. `pnpm build` then `pnpm check-dist` clean, typecheck clean, **823/823 tests**.
- **site** `rtm-reviewB-1004`: first from `batchA-trial-1004` (pushed as `reviewB-trial-1004`, with #290), then **rebuilt on origin/main `c903482e`** (v0.22.0 and batch A live) with #281, #288, #286, #280, #284, #289, #291, #292 and without #290: pushed as **`reviewB2-trial-1004`**, the conflict recipe to use. All site PRs merged, `@rtm/ingest` linked with #281's `pnpm ingest link` into the site and all 15 report worktrees (`rtm-reviewB-1004-reports/`, every report PR merged; jack-smith's #10+#11 resolution pushed as jack-smith-report `reviewB-trial-1004`).
- **Shared-default attribution:** `pnpm ingest try ../ingest-reviewB-1004 --base v0.22.0` over the 13 live reports at their `main` (no opt-ins), so only shared defaults show.

## Verdicts

| PR | Verdict | Reason or exact fix |
|---|---|---|
| ingest #61 resolvePasses throws (5xln) | SHIP (reviewer fixup pushed) | **Found only by the trial merge:** `KNOWN_PAGE_PASSES` did not name the page passes of #62 (`parenFolios`, `pageHeadFolios`, `foliosInStep`), #63 (`pdfPageNumbers`) or #64 (`footnoteRestarts`, `sequencedNoteOpenings`). Merged together, `resolve-passes.test.ts` fails ("parenFolios: expected false to be true"), and Challenger, Jack Smith, Post Office, Litvinenko, Leveson, Lehman and Hillsborough would **throw at ingest**. Fixup `b146950` names all six (harmless before the others merge: the list only gates lookups). 780 tests on the branch alone. No current report declares an unknown pass: all 15 ingest under it. |
| ingest #62 folio passes (uw50, ssfk) | SHIP | Opt-in; only Challenger and Jack Smith move (by their report PRs). Anchors as claimed: Challenger markers-wrong 20 to 0, pages-unmarked 11 to 6, blocks-wrong 82 to 35; Jack Smith 2/10/49 to 0/0/0. Side effects now budgeted in #280 (see Budgets). |
| ingest #65 folioReport (vwqr) | SHIP-WITH-FIX | Stacked on #62. **A squash after #62 squashes conflicts** (`src/pipeline.ts`, `src/folios.ts`, dist): after #62 lands, `git rebase --onto origin/main anchors-1004 ingest-folios-1004`, retarget to main, then squash. Byte-identical output (new result field only). |
| ingest #63 Post Office passes + 2 shared defaults | SHIP | Control characters: on the 13 live reports only Leveson moves (32 lines lose a U+0007; 0 ids; the `try` diff says 6 paragraphs because several are list items of one). The lettered-subsection section rule (`[a-z]\. ` folds a body-less h2 forward) moves no live report (all 13 sections unchanged under `try`). Opt-ins only for the new report. |
| ingest #64 note-order fixes | SHIP-WITH-FIX | Conflicts with #62 and #63 (resolutions below). Shared defaults alone move Litvinenko (+16 sidenotes, 3 ids, `try`), PSI (+22), Leveson (+6), Hillsborough (+5), Lehman (+2): all five also have report PRs, so these are re-ingested anyway. Every other report byte-identical. Measurement changes: n891 raises Challenger oracle `markers-unlinked` 27 to 32 with no text change (budget in #280); qzix makes **Deepwater golden p.17 pass, so `verify` fails** on its now-stale xfail (fix: us-deepwater-horizon#9, merge after the pin bump); bt5d adds 7 suspects to jan6's `fidelity.md` (cosmetic, regenerated). |
| ingest #66 EditionNote.after + one-line space rule (jan6) | SHIP | Merges cleanly onto #64's `edition.ts` changes. Only Saville moves: 3 words ("Waterloo Place/ Strand" to "Place/Strand", "Road/ Infirmary", "and/ or"), as its HTML prints them; 0 ids, 0 paragraphs, prerender of Saville changes, search index 0 changed passages. Rebaseline Saville. |
| ingest #67 divider never swallows next h2 | SHIP | Rendering only, ids unaffected. Litvinenko 50 to 54 sections and Challenger 50 to 52, as claimed. Two of the six (`o-rings-and-putty`, `appendix-10-list-of-inquiry-staff-and-counsel`) were once published and aliased away; they are live again and `aliases generate` drops their section aliases. The new Challenger pages are memo subheads inside the Thiokol memo ("THIOKOL", "ELECTRICAL", "O-RINGS AND PUTTY"): small but real. Corpus check flags Litvinenko (sections) for accept. Columbia, Deepwater, Philip Morris and Chilcot prerender byte-identical. |
| site #281 try --base, findings diff, ingest link | SHIP | Used throughout this review: `ingest link`/`--restore`/`preflight` and `try --base v0.22.0` worked first time on 13 reports (about 20 minutes). `lessons.md` conflict only. |
| site #288 folios CLI | SHIP after #281 and the pin bump | `pnpm ingest folios jack-smith-vol1` prints runs and strays as described (needs #65 in the library). |
| site #280 budgets (uw50, ssfk) | SHIP-WITH-FIX (reviewer fixup pushed) | Fixup `ea827b88` adds every budget move the integrated trial needs (each read, each with a `# why:`): Jack Smith quality `severed-paragraph` 5 to 6 and `quote-share-parity` 1 to 2 (bead **5sf1**: the Blanche letter's letterhead footer now sits mid-paragraph once its "Page N" head is gone); Challenger quality `severed-paragraph` 42 to 43 (p.36, uw50); Challenger oracle `markers-unlinked` 27 to 32 (n891) and `note-off-page` 77 to 78 (uw50); 9/11 anchors `blocks-wrong` 0 to 4 (bead **smof**, see #12). Merge **after** #286 (both touch the Jack Smith quality block; they merge cleanly in that order). |
| site #286 note-order signal + derived files | SHIP | Its 7 `full.md` files reproduce byte for byte at the integrated ingest except Leveson (the #63 BEL removal) and Jack Smith (#10's folios added); the re-ingest regenerates both. |
| site #284 Post Office Horizon Vol 1 | SHIP AND PUBLISH (reviewer fixups pushed) | Fixups: `d4853a16` adds the registry fields batch A's #285 made mandatory (`common_name`, `also_known_as`, `issued_by`, `date_published` 2025-07-08, `repo`, OGL `license`); without them 6 `seo-head` tests fail. `77b7ca77` sets `published-ids.txt` to the 948 ids of the corrected text: it listed 959, including 11 ids its own corrections retired and that were never live, so `aliases check` failed; an `aliases.yaml` for them (`66623a6c`) does not survive ship's `aliases generate --all`, so it is removed again. Its `published-sections.txt` (18) matches the integrated renderer (#63 with #67 fold "a. Introduction" into its chapter). **After merging, run `pnpm cards` and commit the 4 `q-*.png` quote cards and `src/generated/cards.ts`** (the PR has only `default.png`; with #289 in, `tests/cards.test.ts` and ship's `cards` step fail until they are committed). Meets the bar (see "New reports"). |
| site #289 cards prune + ship `cards` step | SHIP | Retargeted onto main (#279 is merged): merges with a `lessons.md` conflict only. On the trial: `pnpm cards` 95 cards, `pnpm cards --check` "95 card(s) and the manifest match a fresh render", `pnpm posts --verify` up to date, its tests pass. Note for the ship: the new `cards` step stops if cards or the queue change, so commit #284's cards before running ship. |
| site #291 strict ship flags | SHIP before running ship | Flags it accepts: `--ack --allow-dirty --base --d1-limit --d1-read-limit --from --help --measure --old-ref --only --plan --redo --reset --root --shared --skip --state --status --verbose --yes`. Every command in the steps below parses under it. |
| site #292 edge-cache key per deploy | SHIP | `cacheKeyFor(url, deployId)` keys `?p=`/`?h=` pages on `CF_VERSION_METADATA.id` (`[version_metadata]` in `wrangler.toml`), and does not cache without it. A cache miss builds the page from ASSETS/R2; the only D1 read on that path is the content-version lookup the Worker already does, so a cold cache after deploy costs at most a few hundred extra reads. Its new verify-prod check (plain `?p=` version header equals the report page's) runs in ship's verify-prod. |
| site #290 January 6th | **HOLD the merge** (reviewer fixup pushed) | Fixup `1d105e42` adds the registry fields. But it has no plate, and `tests/plates.test.ts` requires one for every registry entry, so merging it turns main's `pnpm test` and `verify.sh` red (the only failure in my full local verify). Merge it, with the plate and cards, when z959 lands (a site-only follow-up; its publish then costs about 6.9k writes). Its `full.md` reproduces exactly at the integrated ingest; golden 5/5, quality/anchors/oracle within its budgets, editorial passes. |
| challenger-accident#11 | SHIP | Reproduces byte for byte. |
| jack-smith-report#10 and #11 | SHIP (they compose) | Conflict in `ingest.ts` (both add passes) and `baseline.json`. Resolution: one import line `import { layoutPageJoins, layoutMarkers, quoteListRunOns, pageBreakContinuations, pageHeadFolios, foliosInStep, pipeline, sequencedNoteOpenings } from "@rtm/ingest";` and both pass blocks (#10's `pageHeadFolios`, `foliosInStep`, then #11's `layoutMarkers`, `sequencedNoteOpenings`); `baseline.json`: either, the re-ingest rewrites it. Git's textual merge of `full.md` happens to equal the re-ingest. Effects are additive: words 61,689 -37 (#10) +42 (#11) = 61,694; paragraphs 428 to 417 (#10). Recipe: jack-smith-report branch `reviewB-trial-1004`. |
| uk-post-office-horizon-inquiry#1 | SHIP | Head `78afefa` (corrections, adjudicated) reproduces byte for byte. |
| us-911-commission#12 | SHIP-WITH-FIX | **Trade-off, not claimed by the PR:** `floats: "by-notes"` serves the box before the rejoined paragraph, and the box's next-page marker now precedes that paragraph's head, so 4 paragraphs are cited a page late (p.145 "Like his nephew Ramzi Yousef…" under 146; 177, 227, 260 likewise). Note order 5 to 0 is worth it, but bead **smof** (P2) and the anchor budget in #280. |
| uk-litvinenko-inquiry#10, uk-hillsborough-panel#11, us-lehman-examiner#10, us-psi-financial-crisis#13, uk-leveson-inquiry#9 | SHIP | All reproduce byte for byte (Leveson apart from the #63 BEL removal: rebaseline). |
| us-deepwater-horizon#9 (new, reviewer) | SHIP after the pin bump | https://github.com/reportsthatmatter/us-deepwater-horizon/pull/9 removes golden p.17's qzix xfail. At v0.22.0 the page still fails, so merge it after the bump. |
| us-jan6-committee#1 | SHIP | Merge freely (nothing is registered on the site until #290). |

## Found only by the trial merge

1. **#61 x #62/#63/#64:** a library that throws on unknown pass names, merged with three PRs that each add page passes. Each PR is green alone; together seven reports throw. Fixed on #61.
2. **#65 stacked squash** conflicts after #62's squash (same as #58/#59 at v0.22.0).
3. **#284/#290 x batch A (#285):** new registry entries lack the fields #285 made required. Fixed on both branches.
4. **#290 x `plates.test.ts`:** a "merge only, publish later" report still has to pass main's tests, and registering it is what fails. Hold.
5. **#64's qzix x Deepwater golden:** fixing the golden rule makes a stale xfail fail verify. us-deepwater-horizon#9.
6. **#64's n891 x Challenger oracle budget:** a measurement change moved a report nobody re-ingested.
7. **us-911-commission#12 anchors:** 4 blocks a page late; #286 carried no anchor budget for it, so `anchors --check` fails without #280's fixup.
8. **Leveson reused id** `2-1-information-commissioner-corporation`: #64/#9 took note text out of the paragraph, which now joins across p.1000/1001 (same paragraph, longer). The join exposes the running head mid-sentence: "grant-in-Chapter 1 | Introduction aid voted…" (bead **jr1t**). Nothing in `editorial/`, `docs/share-quotes.yaml` or the queue cites the id. Run `--accept-reuse` (step below).

Not blocking: `aliases generate` still prints the Challenger `MOVED-OUT ID and` false alarm (known, v0.22.0 review).

## Conflict resolutions (ingest)

- **#64 onto #61+#62+#63**, `src/clean.ts`, the options type: `options: { citationRunOver?: boolean; footnoteGap?: boolean; footnoteRestarts?: boolean; sequencedNoteOpenings?: boolean; romanFolios?: boolean; parenFolios?: boolean; pageHeadFolios?: PageHeadFolio; footnoteNumbers?: FootnoteNumbers } = {}`.
- `src/define.ts`, in `resolvePasses`: keep #64's two lines `footnoteRestarts: passes.some(…"footnoteRestarts")`, `sequencedNoteOpenings: passes.some(…"sequencedNoteOpenings")`, and **#63's** `footnoteNumbers: (passes.find((pass) => pass.name === "footnoteNumbers") as { numbers?: "period" | "tabbed" } | undefined)?.numbers,` plus `pdfPageNumbers: …`. Drop #64's `footnoteNumbers: … ? "period" : undefined` line.
- `src/pipeline.ts`: `const parsed = parseFootnotes(split.footnotes, split.index, resolved.footnoteNumbers ?? "bare", { sequenced: resolved.sequencedNoteOpenings }).map((note) => ({`.
- `dist/*` (in #63 and #64): take either side; `pnpm build` overwrites it. #66 and #67 merge with no conflict.
- After the last squash: `pnpm build`, commit `dist` if it changed (in the trial it did not), `pnpm check-dist` clean, `pnpm typecheck && pnpm test` = **823**.

**Site:** `docs/design/lessons.md` conflicts in every PR: keep both sides. `src/generated/cards.ts` (#284): keep both, then `pnpm cards` regenerates it. (#290's `reports/*.yaml` conflicts with #284: both append a report at the end; keep both blocks; append jan6's quality block after Post Office's, do not interleave.) `reports/corpus-baseline.json`: take main's, then `pnpm corpus accept` after reading.

## What moves (against v0.22.0 as published, `d72327fb`; ids lost = published ids now `unmatched`)

| report | by | words (rendered) | citable paragraphs | ids lost | aliases | sections | intended? |
|---|---|---:|---:|---:|---|---|---|
| jack-smith-vol1 | #62/#10, #64/#11 | 65,696 → 62,247 | 430 → 418 | 12 (`january-6-2025-page-N`: the letter's running heads) | +10 −1; section `twitter-search-warrant-litigation` aliased | – | yes: running heads gone; the word drop is sidenotes no longer rendered twice (repeated references 70 → 13) |
| uk-leveson-inquiry | #63 (BEL), #64/#9 | 1,084,646 → 1,084,793 | 12,750 → 12,704 | 57 (note text that was body: "1-http-www-levesoninquiry-org"…) | +16 −17, 15 retargeted; 1 reused (accept) | – | yes; 97 more notes |
| litvinenko-inquiry | #64/#10, #67 | 134,977 → 135,021 | 2,147 → 2,136 | 17 (cipher note text in the body) | +1 −7, 5 retargeted | +4 (1 resurrected) | yes |
| challenger-accident | #62/#11, #67 | 153,063 → 153,142 | 2,846 → 2,840 | 3 (`11`, `enclosure-111`, `35-activities-your-commission-conclusion`: folio debris) | +7 −2 | +2 (1 resurrected) | yes |
| us-psi-financial-crisis | #64/#13 | 327,057 → 327,041 | 2,404 → 2,408 | 2 | +2 −4 | – | yes (body restored from notes 216, 1399) |
| uk-hillsborough-panel | #64/#11 | 203,379 → 203,870 | 2,758 → 2,755 | 0 | +3 | – | yes (notes 24-25 restored) |
| us-911-commission | #64/#12 | 305,733 → 305,662 | same | 0 | 0 | – | yes, with smof |
| us-lehman-examiner | #64/#10 | 184,929 → 184,933 | same | 0 | 0 | – | yes |
| uk-saville-inquiry | #66 | 168,229 → 168,226 | same | 0 | 0 | – | yes |
| Columbia, Deepwater, Philip Morris, Chilcot | – | byte-identical text and prerender | | | | | – |
| uk-post-office-horizon-inquiry | new | 82,060 words | 948 | – | 11 never-live ids aliased | 18 | – |
| us-jan6-committee | new (held) | | 2,283 | – | – | 103 | – |

## Checks on the trial (15 reports, all fixes applied)

- `pnpm ingest check`: every report's PR `full.md` reproduces exactly except Leveson (BEL) and Saville (#66), both expected.
- `pnpm corpus check`: moved as in the table; accepted after reading.
- `pnpm quality check`: all 15 within budget after #280's fixup.
- `pnpm ingest anchors --check`: all within budget after the fixup (Litvinenko can ratchet blocks-wrong 2 to 1).
- `pnpm ingest verify`: golden passes everywhere once us-deepwater-horizon#9 is in (Hillsborough 7/7, Saville 5/5, jan6 5/5, Post Office 4/5 plus 1 known). Oracle within budget after the fixup.
- `pnpm editorial`: 15 files, every quote verbatim. `pnpm aliases check`: passes after `--accept-reuse` and the Post Office aliases. `pnpm posts --check`: passes. `pnpm cards`: 103 cards; it adds the Post Office `q-*` cards (commit them).
- `pnpm typecheck` clean; `pnpm test` 921/922 on the first trial (the plates test, #290 only); 937/937 on the rebased trial.
- **`./scripts/verify.sh` (local), first trial with #290: 380 passes, 1 failure (`vitest`: the jan6 plate).** **Rebased trial (main `c903482e` + every site PR but #290, branch `reviewB2-trial-1004`): `./scripts/verify.sh` "All checks passed.", 381 passes, 937/937 tests, including the browser and phone checks.**
- **A trap I fell into (bead `irfs`):** `CI=true pnpm install` in a linked site worktree silently puts `@rtm/ingest` back to the pin, and `pnpm ingest link` then refuses ("Already linked") because `.rtm-link.lock` survives. `preflight` showed `✓ site v0.22.0` among the reports' "linked override" lines. One whole derived-files and verify run used the pinned renderer with the trial's text, and failed in confusing ways. Run `pnpm ingest link --restore` and link again after any install.
- **Scorecard** (`pnpm scorecard --base d72327fb`): the ▲/▼ rows are mostly v0.22.0's own changes, because `quality-last.json` and `docs/scores.json` at `d72327fb` predate v0.22.0's record. The three flagged score regressions:
  - Columbia WER (held-out): Columbia's text is byte-identical here, so not this release.
  - Hillsborough shadow marker R 93.6% (held-out, −2.3 against the stale base): against v0.22.0 itself it is **93.0% → 93.6%**, so this release raises it.
  - 9/11 shadow marker P −0.1 pts: #64's shared marker rules on the shadow PDF. Small; noted, not beaded.
  - One process note for Rufus's held-out rule: #64's `sequencedNoteOpenings` took examples from Hillsborough, a held-out report. It is opt-in and moved the held-out shadow up, not down, but it is the kind of tuning `score-sets.yaml` warns about.

## New reports going public: what AGENTS.md says

No rule reserves a new report's publication for Rufus. These rules govern it:

- AGENTS.md "Working conventions": **"Rufus does not review PRs. An agent review (a separate reviewer agent) is the gate; then the integrator merges and ships."**
- AGENTS.md: "Ship introductions without review."
- `docs/report-pipeline.md` (linked from "Begin here"): "each stage ships on its own. Nothing waits for a later stage, and nothing waits for Rufus's review", and "Stage 1 alone is a good page".

**So this review is the gate.** Post Office Horizon Vol 1 **meets the bar and may be published in this ship**:
- Text: quality within budget; anchors 0 wrong; golden 4/5 plus 1 known (ud34).
- Score: held-out boundary F1 94.9%; adjudicated 2/30 against the tagged reference.
- Pages: plate, default card and PROCESSING.md are in place; the editorial is approved and verbatim. OGL v3.0.
- Hero image is stage 3 and waits (gqsy.2.2).

Be aware that once #284 merges, `pnpm ship`'s `status` step lists the report as unpublished and publishes it with the others; that is what this verdict allows. January 6th waits for its plate (#290 held), not for Rufus.

## D1 (free tier; today's ledger per the coordinator, `pnpm d1-usage`: 1.76M of 5M reads and 5.5k of 100k writes used, so about 3.24M reads and 94.5k writes left)

Layouts were recorded at batch A (`--record-layouts`). Publish set: the 9 moved reports plus the Post Office report. The 4 unmoved reports prerender byte-identical, so they stay `current`.

Measured writes (`scripts/measure-reindex.mjs` on a scratch local D1, old = `d72327fb` text, new = trial; ref `reviewB-d1measure-1004` in the site repo):

| report | passages | changed / new / gone | reindex row writes (incremental) |
|---|---:|---|---:|
| uk-leveson-inquiry | 12,704 | 120 / 11 / 57 | 925 |
| challenger-accident | 2,840 | 167 / 4 / 10 | 1,045 |
| jack-smith-vol1 | 418 | 111 / 9 / 21 | 757 |
| litvinenko-inquiry | 2,136 | 26 / 0 / 11 | 190 |
| uk-hillsborough-panel | 2,755 | 29 / 0 / 3 | 184 |
| us-psi-financial-crisis | 2,408 | 8 / 8 / 4 | 85 |
| us-lehman-examiner | 975 | 6 / 0 / 0 | 37 |
| us-911-commission | 2,438 | 5 / 0 / 0 | 31 |
| uk-saville-inquiry | 1,458 | 0 | 1 |
| **moved, total** | | | **3,255** |
| uk-post-office-horizon-inquiry (new) | 948 | 948 new | 2,845 |
| (us-jan6-committee, held) | 2,283 | | (6,850) |

**Writes: about 6.1k for the reindex, plus about 100 for publish/version rows and seeding 9 Post Office highlights. That is about 6.8k with ship's 10% margin, under 7% of a day.**

**Reads (estimated from #287's code: `estimatedReindexReads` = the report's stored rows through its layout, +3):**

| step | rows read |
|---|---:|
| moved reports' stored rows through layouts | ~28.2k (Leveson 12.75k is most of it) |
| the same again in `d1-estimate` (each `publish-report --dry-run` reads as the reindex does) | ~28.2k |
| Post Office, first index (no layout yet: one scan of the ~46k-row table), twice (dry run + real) | ~92k |
| verify-prod (`VERIFY_READS_PER_REPORT` 4k × 10) | ~40k |
| status, drift table, `seed-highlights` marks scan, a few post-ship searches | < 2k |
| **total** | **~190k (about 4% of 5M)** |

If layouts were somehow cleared (an old-checkout reindex), each report scans once: about 10 × 46k × 2 ≈ 0.9M, still inside today's ~3.24M left. A cold edge cache after the deploy (#292) adds at most a few hundred reads. **It fits today, with a wide margin.** Run `pnpm d1-usage` first; if ship cannot read usage, pass `--d1-limit <writes left> --d1-read-limit <reads left>` (both accepted by #291's parser).

## Integrator steps

1. **Ingest** (squash merges), in this order:
   1. #61 (head b146950).
   2. #62.
   3. Then `git rebase --onto origin/main anchors-1004 ingest-folios-1004`, retarget #65 to main, and squash it.
   4. #63.
   5. #64 with the resolutions above.
   6. #66, then #67.
   7. `pnpm build`; commit `dist` if it changed; `pnpm check-dist`; `pnpm typecheck && pnpm test` (823).
   8. `pnpm release 0.23.0 --dry-run`, then `pnpm release 0.23.0` (tags `v0.23.0` on the post-merge main commit).
2. **Report repos** (merge commits; take the base branch's `baseline.json`/`full.md` on conflict; ship re-ingests). Pull the shared checkouts `--ff-only` afterwards.
   - challenger-accident#11.
   - jack-smith-report#10, then #11 with the `ingest.ts` resolution above.
   - us-911-commission#12, uk-litvinenko-inquiry#10, uk-hillsborough-panel#11, us-lehman-examiner#10, us-psi-financial-crisis#13.
   - uk-leveson-inquiry#9 (base `master`).
   - uk-post-office-horizon-inquiry#1, us-jan6-committee#1.
   - us-deepwater-horizon#9 (needs the bump first, but ship's checks run after its pin-bump step, so merging it now is fine).
3. **Site, before ship** (merge commits; `lessons.md`: keep both, every PR conflicts there; recipe branch `reviewB2-trial-1004`):
   - #291 first, so every ship command below is parsed strictly. Then #292 and #289 (retarget #289 to main).
   - #281, then #288.
   - #286, then #280 (with my fixup).
   - #284 (`src/generated/cards.ts`: keep both, then regenerate). Straight after it, `pnpm cards` and `pnpm posts`, and commit the 4 Post Office `q-*.png`, `src/generated/cards.ts` and `marketing/queue.yaml`. #289's test and ship's `cards` step need them.
   - **Not #290.**
4. **Ship:** `pnpm ship --plan` first (#291 now rejects unknown flags). Then `pnpm ship --shared --old-ref c903482e`, the deployed commit; check it is still origin/main's head before the first batch-B merge, and use that commit if anything else landed. Export `VERIFY_SHARED=1` and either `CLOUDFLARE_API_TOKEN` or `--d1-limit <writes left>`. The pin bump goes to `v0.23.0` in the site and every report repo; ship does it, so do not bump by hand.
5. **Baseline gate:**
   - Expect 9 reports moved: Jack Smith, PSI, Challenger, Litvinenko, Leveson, 9/11, Hillsborough, Lehman, Saville.
   - Post Office and jan6 should reproduce.
   - Read the diffs, then `--ack baseline`.
6. **Aliases:** after the aliases step, run `pnpm aliases generate uk-leveson-inquiry --old-ref c903482e --accept-reuse`; nothing cites the id. Expect "ids lost" as in the table, and Litvinenko and Challenger to lose two section aliases (the sections are live again).
7. **Corpus gate:** accept the 9 plus Litvinenko's sections, then `--ack corpus`.
8. **Editorial and cards gates:** nothing should fail if step 3's cards and queue were committed; otherwise the `cards` step stops: commit and `--redo cards`.
9. **Checks:** the budgets are already in #280. Then:
   - `pnpm quality ratchet` and `pnpm ingest anchors litvinenko-inquiry --ratchet`.
   - `--ratchet-oracle` for the 7 reports verify lists: Jack Smith markers-unlinked 36 to 30 and note-off-page 71 to 11; PSI 26 to 2; Litvinenko 29 to 0; Leveson 142 to 47; Hillsborough 24 to 1; Lehman 22 to 19.
10. **Commit and continue:** commit the derived files per repo, then `pnpm ship` to `status` and `d1-estimate`. Expect 10 to publish and about 6.8k writes and about 190k reads.
11. **Production:** `pnpm ship --yes`.
12. **Record:**
    - Changelog: the new report, note order on 7 reports, folios on 2.
    - Close 5xln, uw50, ssfk, vwqr, gq4j, n7fb, kvxj, qsfc, kgpr, 4kfr, u00i, qzix, n891 and bt5d on ship, and gqsy.2's stage-1 bead.
    - Leave gqsy.3 open until #290 ships with its plate.

## Beads filed

- `reportsthatmatter-smof` (P2): 9/11 by-notes floats put the next page's marker ahead of 4 paragraph heads.
- `reportsthatmatter-5sf1` (P3): Jack Smith: the Blanche letterhead footer is severed into a paragraph.
- `reportsthatmatter-jr1t` (P3): Leveson running head mid-sentence at p.1000/1001.
- `reportsthatmatter-irfs` (P2): `pnpm install` undoes `ingest link`; the stale lock then blocks relinking.

## Getting better faster (reviewer retro)

- **What slowed me down:**
  - Two scope changes mid-trial (#66, #67, jan6) and two upstream pushes (#284 and the Post Office repo) meant three re-ingests of all reports.
  - `pnpm ingest worktrees` makes a new report's worktree from `main`, which has no `package.json`, so link and preflight skip it until a manual install after merging the PR branch.
  - A `git stash` in one worktree lands in the shared stash list of every worktree.
- **What would have caught these earlier:**
  - #61's own test would have caught finding 1 in each pass PR, if each had been rebased on #61 first. "Merge the library-wide gate first, then rebase the rest" is the rule.
  - A test that a registry entry passes the head/plate requirements before a report PR is opened (`pnpm pipeline status --check` could run `seo-head` and `plates` for the new id).
- **One proposal:** `pnpm ingest try` should accept report branches (`--report <id>=<branch>`). The shared-default run and the opt-in run would then be one command, and a reviewer could stop merging 11 report PRs into worktrees by hand.
