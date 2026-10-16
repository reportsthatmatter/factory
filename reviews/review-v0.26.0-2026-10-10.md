# Agent review of the v0.26.0 release (batches F, G, H; I deferred), 2026-10-10

Reviewer: Opus agent. I made no merge, release, deploy, publish or seed, and no D1 query of any kind. The D1 ledger's last entry is still my 2026-10-10T00:08Z read from the v0.25.0 review. The marks replay below used that export file through `--marks-file`.

Base: `@rtm/ingest` v0.25.0 (tag on `fa353e4`) and site main `13ec5de0` (v0.25.0 pin bump, #310). The v0.25.0 ship is the integrator's, still in progress; its publish, seed and record steps are not assumed done here.

**Verdict: SHIP v0.26.0 with F, G and H.** Batch I (the CIA report) has no PRs yet (`us-senate-cia-torture-report` has none open); it goes in a later release.

## Verdicts

| Batch | PR | Head to merge | Verdict | Note |
|---|---|---|---|---|
| H | ingest #73 Leveson passes | **61acaa6** | MERGE first | Reviewer merged main (v0.25.0) in; `dist/` conflicts only, rebuilt. CI was green before the merge; 867/867 locally |
| F | ingest #76 burn-down passes | **ab65f4a** | MERGE second | Reviewer merged main in and resolved the real conflicts with #74: `typographic-headings.ts` keeps both `quotedRemainder` and `skipRunIns`; `KNOWN_PAGE_PASSES` takes the union. 869/869 |
| G | ingest #75 Mueller passes | **a829227** | MERGE third | Reviewer merged main in (`KNOWN_PAGE_PASSES` union). 892/892. It also moves Litvinenko (finding 1) |
| F | us-columbia-shuttle-accident#9 (base `master`) | 846572f4 | MERGE | |
| F | challenger-accident#12 | fecb735e | MERGE | |
| F | us-deepwater-horizon#10 | 1ab1f00a | MERGE | Its baseline regenerates at ship (#73's note link, finding 2) |
| G | us-mueller-report#1 (new public repo) | 88fa28c5 | MERGE | Pins v0.24.0; ship bumps it |
| H | uk-leveson-inquiry#10 (base `master`) | b252fcfe | MERGE | |
| F | site #306 | **20135d22** | MERGE | Reviewer fixup: regenerated `src/generated/editorial.ts` (Challenger cites `1-design-field-joint-unsatisfactory` and `concurrence-…-confirms`; the generated file still named the old ids) |
| G | site #307 Mueller | **32f77d31** | MERGE | Reviewer fixup: decision 0019 → **0020** (`0020-redactions.md`, README row, `reports/pipeline.yaml` evidence); note on bead oh89 |
| H | site #309 Leveson | **afe21f4b** | MERGE | Reviewer lessons appended |

## Trial

- **Ingest trial:** `~/src/reportsthatmatter/ingest-review026-1009`, branch `review026-trial-1010`. It is v0.25.0 (`fa353e4`) with #73, #76 and #75 squashed in, conflicts resolved as above and `dist/` rebuilt.
  - `typecheck` is clean and **910/910 tests** pass.
  - The released v0.25.0 `src/` and `dist/` are byte-identical to my v0.25.0 trial.
- **Site trial:** `~/src/reportsthatmatter/rtm-review026-1010`, branch `review026-trial-1010`. It is origin/main `13ec5de0` plus #306, #307 and #309, linked to the ingest trial.
  - Conflicts were appends in `lessons.md`, the decisions README rows (0015–0020) and `reports/corpus-baseline.json` (a derived file; see step 6).
- **Report worktrees:** `~/src/reportsthatmatter/rtm-review026-1010-reports/*` holds all 17 reports, each with its v0.25.0 and v0.26.0 PR branches merged in.

### What moves: exactly the union of the claims, plus one unclaimed report

`pnpm ingest check` was run over all 17 reports against the v0.25.0 baselines. `markdownSha` is a text hash, so a pass means byte-identical text.

- **Unchanged (11):** Jack Smith, PSI, 9/11, Philip Morris, Hillsborough, Saville, Chilcot, Lehman, Post Office, Jan6 and Grenfell are byte-identical.
- **Matching their PRs' committed baselines:** Challenger, Columbia, Leveson and Mueller re-ingest byte for byte.
- **Moved without a PR baseline:**
  - **Deepwater**, by #73 (claimed: "Deepwater baseline regenerates").
  - **Litvinenko**, by #75 (**not claimed**).
- **Attribution.** I relinked to v0.25.0+#73, then to +#76, and re-ran. Deepwater's move is #73 alone; Litvinenko's needs #75.
- **Site copies.** Every report's `full.md` equals the site trial's copy once Litvinenko and Deepwater are aggregated.

| Report | Batch | full.md bytes (main → trial) | Lines | Ids / sections |
|---|---|---|---|---|
| challenger-accident | F | 842,992 → 842,942 | +164 −164 | `hyphenFragments` joins (115), `divisionLabels`; aliases +53 lines |
| columbia-accident | F | 1,077,798 → 1,079,029 | +370 −412 | hyphen joins (60), labels, speaker turns, subheads; aliases +313 lines |
| us-deepwater-horizon | F + H | 1,190,839 → 1,190,852 | +8 −6 | Chapter 9's A–G list (a3y; two section headings become list items, aliased); note 57's marker linked by #73 |
| uk-leveson-inquiry | H | 6,989,322 → 6,988,452 | +434 −652 | running heads and small-caps garble gone, "N.N Second" openers split; aliases +377 lines |
| litvinenko-inquiry | G (#75), unclaimed | 792,096 → 792,081 | +5 −15 | 5 statute items "(ii) …of the Act" / "> and having had regard…" rejoined; ids unchanged |
| us-mueller-report | G | new: 1,196,795 bytes | +8,912 | 102 sections; 272 `[Redacted: …]` markers |

**Text preserved.** Litvinenko loses or gains no word.

- **Leveson's losses** are running heads ("Chapter", "THE", "PARt" ×6). Its gains are the small-caps words fixed ("Part", "press").
- **Challenger and Columbia** change only by hyphen joins and OCR garble moving with them.

### Checks on the trial (all with the v0.26.0 ingest linked)

- `typecheck` is clean.
- `test`: **1084 passed, 1 skipped**.
- `editorial`: 17 files.
- `quality check`: all within budget.
- `aliases check` passes.
- `ingest anchors --check` passes.
- `ingest crosscheck` passes.
- `ingest recheck` over every touched pass (`thumbIndexNotes`, `hyphenFragments`, `divisionLabels`, `speakerTurns`, `typographicHeadings`, `letteredItems`, `contentsOutline`, `foiaRedactions`, `asteriskBreaks`, `layoutRunOvers`) matched every correction once.
- `posts --verify` and `cards --check` pass; Mueller's 5 cards match a fresh render.
- `pipeline status` exits 0.
- **`pnpm corpus check` flags 5 reports, word counts only:** Challenger −5, Leveson −85, Columbia −1, Deepwater −30 and Mueller −10.
  - **Why:** the PRs computed their baselines with the v0.24.0 renderer, so #72's autolink fix re-applies, plus Deepwater's list.
  - **Needed:** `pnpm corpus accept` for those 5 (step 6).
- **`pnpm quality ratchet` lowers 4 budgets.** These are the unclaimed improvements:
  - `litvinenko-inquiry`: severed-into-quote 72 → 67.
  - `us-deepwater-horizon`: bare-footnote-marker 1 → 0, note-reference-sequence 3 → 2, rendered-ol-words-share 1 → 0.
  - They belong in the ship's ratchet commit (step 8).
- **Marks, links and editorial.** I replayed them offline against the v0.25.0-era export (`--marks-file`; no D1 read).
  - Every Leveson, Columbia, Challenger, Deepwater and Litvinenko mark, queue link and editorial reference anchors.
  - Mueller's 4 links and 28 editorial references anchor.
  - The one stop is the v0.25.0 Philip Morris editor highlight that v0.25.0's seed deletes. It is not a v0.26.0 change.

### Adversarial samples of the new passes

- **`hyphenFragments` (#76).** In the reports that declare it, I listed every "X- Y" that became "XY": 60 in Columbia and 115 in Challenger.
  - Almost all are typesetter breaks ("indel- ible", "Hamp- ton", "furi- ously").
  - **2 are real compounds closed up wrongly:** Challenger "life- threatening" → "lifethreatening", and Columbia "multiplexer- demultiplexer" → "multiplexerdemultiplexer". They were broken before too ("life- threatening" with a stray space), so this is no worse.
  - **Opted into Lehman and PSI as a trial:** Lehman joined 2. PSI joined 4, **3 of them wrongly**: "hardedged", "resourceconstrained", "singlename".
  - **Verdict:** the pass is fine where it is declared (about 1–2% wrong), and wrong for reports with compound modifiers. Filed `reportsthatmatter-9sgb` (keep a compound's own hyphen, plus tests) and a line in `lessons.md`.
- **#73's default-path changes.** `QUANTITY_WORD_FOLLOWS` became case-sensitive, and `mergeAcrossPages` will not glue an "N.N Capital" opener after an unfinished paragraph.
  - Across all 17 reports they moved only Deepwater: one note marker became a linked note. That is neutral to positive, and the guard sits correctly beside #70's numbered-body join.
- **#75's default-path changes.**
  - A `FOOTNOTE_INLINE` lookahead for its redaction marker.
  - `numberIn` ignores "6(e)".
  - Roman-numbered pages are skipped in furniture recovery.
  - `OUTLINE_LABEL` accepts lower-case romans.
  - `LEADER_TAIL` accepts ellipsis leaders.
  - The `letteredItems` single-space form.
  - **Only that last one moved anything: Litvinenko,** the one other report declaring `letteredItems`. Its 5 changes are correct joins of statute items cut at a line.
- **Mueller.** Checked against `docs/report-introductions.md`:
  - `why_it_matters`: 55 words.
  - `background`: 266 words in 3 paragraphs, with the background facts' sources in the yaml header.
  - 8 findings, attributed, 4 with quotations that stand alone.
  - 6 reading-guide sections and 11 highlights, 4 of them `card: true`.
  - Facts checked:
    - FBI investigation July 2016; appointment 17 May 2017; report to Barr 22 March 2019.
    - Barr's summary 24 March; Mueller's 27 March letter; released 18 April 2019.
    - Walton's "lack of candor", March 2020; the clemency dates.
  - **`PROCESSING.md` states the scanned-page limitation honestly:**
    - It lists the 74 OCR'd pages of Volume I by printed page.
    - It shows OCR errors served as read ("Trnmp").
    - It says 225 of Volume I's notes on those pages are left in the body, with their ranges.
    - It says some redactions leave no marker where the OCR missed the box label.
    - It says the appendices are not included.
  - Redactions are shown as "[Redacted: (b) (7)(A)]" with the codes the box prints. The record of that choice is decision 0020, `proposed`.
  - The repo `reportsthatmatter/us-mueller-report` is public, with default branch `main`.
- **Rendered pages.**
  - Columbia: 21 `<h4>Findings</h4>`, 8 Recommendation and 16 Recommendations headings, as `divisionLabels` intends.
  - Mueller: 102 section pages, with redaction markers inline in the paragraphs.
  - Deepwater: Chapter 9's A–G now render as a list.

## Findings

1. **Medium (fixed by process): #75 moves Litvinenko, unclaimed.** It widened `letteredItems` to read "(a) Text" with one space before hanging lines. Litvinenko declares `letteredItems` too.
   - The result is right: 5 statute items rejoined, and severed-into-quote 72 → 67.
   - **But:** Litvinenko's repo has no PR, so its `full.md` and `baseline.json` are written at the ship's baseline gate, and its ratchet goes in the ship's ratchet commit. Litvinenko is also a mini-reference in `score-sets.yaml`, so re-score it after the ship.
   - **Lesson:** a pass change needs `pnpm ingest recheck --passes <every pass it touches>`.
2. **Low (expected): Deepwater's baseline moves** by #73 (note 57 linked). Same handling as Litvinenko.
3. **Low: `hyphenFragments` closes up real compounds** (2 in the declaring reports; 3 of 4 in a PSI trial). Bead `reportsthatmatter-9sgb`.
4. **Fixed: the three ingest PRs could not squash-merge onto v0.25.0** (conflicts with #74 and in `dist/`). I merged main into each; see the merge recipe below for the pairwise conflicts that remain. Bead `reportsthatmatter-wwmg`: `KNOWN_PAGE_PASSES` one name per line.
5. **Fixed:** #306's generated editorial was stale, and #307's decision number collided (0019, now 0020).

## Ingest merge recipe

Each ingest PR merges cleanly onto main **today**. After the first squash, the next one conflicts in one place in `src/define.ts`, plus generated `dist/index.*`. Before each `gh pr merge`, in an ingest worktree:

```bash
git fetch origin
git checkout -B tmp origin/<branch>
git merge origin/main
# Resolve src/define.ts KNOWN_PAGE_PASSES: take the union of both sides' names.
# For dist/*: take either side; the build rewrites it.
pnpm build
git add -A
git commit
pnpm check-dist && pnpm typecheck && pnpm test
git push origin tmp:<branch>
# then wait for CI and squash-merge
```

- **Reference.** The final `KNOWN_PAGE_PASSES` must equal the one in the ingest trial (`~/src/reportsthatmatter/ingest-review026-1009/src/define.ts`): it adds `thumbIndexNotes`, `divisionLabels`, `hyphenFragments`, `speakerTurns`, `foiaRedactions` and `layoutRunOvers` to v0.25.0's list.
- **Approval.** A head that differs from the reviewed one only by such a merge commit and a rebuilt `dist/` is approved.

## Integrator steps (following `.claude/skills/integrate-and-ship/SKILL.md` and `docs/release-checklist.md`)

0. **Prepare.**
   - Finish v0.25.0 first, including its seed, record and bead closes. `pnpm ship --status` must show v0.25.0 done.
   - `git -C <each shared checkout> pull --ff-only` and `status` clean. Columbia and Leveson use `master`; `us-mueller-report` is new, so clone it as a sibling: `git clone git@github.com:reportsthatmatter/us-mueller-report ../us-mueller-report`.
   - `unset RTM_REPORT_DIRS RTM_REPO_ROOT`.
   - `pnpm wrangler d1 list` must show `reportsthatmatter-marks`.
   - `pnpm d1-usage` must show analytics numbers.
1. **Merge the ingest PRs** (squash), in this order, using the recipe above for the 2nd and 3rd: **#73 (61acaa6) → #76 (ab65f4a) → #75 (a829227)**.
2. **Release:** in the shared `ingest/` on main, `pnpm release 0.26.0 --dry-run`, then `pnpm release 0.26.0`. It is minor: new opt-in passes, and #73's two default-path guards.
3. **Merge the report PRs** (squash), straight after step 2 and before step 5. They import passes that v0.25.0 lacks.
   - **F:** us-columbia-shuttle-accident#9 (`master`), challenger-accident#12, us-deepwater-horizon#10.
   - **G:** us-mueller-report#1.
   - **H:** uk-leveson-inquiry#10 (`master`).
4. **Merge the site PRs** (squash), in this order: **#306 (20135d22) → #309 (afe21f4b) → #307 (32f77d31)**.
   - `lessons.md`: keep every section.
   - `docs/decisions/README.md`: rows 0015–0020 in order.
   - `reports/corpus-baseline.json` and `src/generated/*`: take main's, then regenerate in step 6.
5. **Bump the pin**, in a site branch `pin-v0.26.0`:
   - `pnpm bump-pin 0.26.0 --dry-run`, then `pnpm bump-pin 0.26.0 --shared`. That covers 17 repos, Mueller included; it pins v0.24.0 today.
   - Check each lockfile names v0.26.0's commit.
   - Commit and push each report repo's `package.json` and `pnpm-lock.yaml`.
6. **In the same site branch:**
   - `pnpm bootstrap`, then `pnpm ingest aggregate --shared litvinenko-inquiry us-deepwater-horizon`. Those are the two reports without a PR text.
   - `pnpm prerender`, then `pnpm aliases check` (expect clean), then `pnpm corpus check`. Expect exactly Challenger, Leveson, Columbia, Deepwater and Mueller, with words only.
   - Read them, then `pnpm corpus accept challenger-accident uk-leveson-inquiry columbia-accident us-deepwater-horizon us-mueller-report`.
   - `pnpm editorial`, `pnpm cards --check` and `pnpm posts --verify` must change nothing.
   - Open the pin-bump PR and merge it.
7. **Plan the ship:** `pnpm ship --reset`, then `pnpm ship --plan`. The guard should list no problem.
8. **`pnpm ship --shared`.**
   - **Baseline gate.** Only **Litvinenko and Deepwater** may differ from their committed baselines; every other repo carries its own. Read both against the table above, then `--ack baseline`.
   - **Commit the new text.** Commit and push `full.md` and `baseline.json` in `uk-litvinenko-inquiry` and `us-deepwater-horizon`.
   - **Ratchet.** It lowers the 4 budgets listed above; commit `reports/quality-budget.yaml` in a site PR and resume.
   - **Corpus and editorial gates:** clean after step 6.
9. **D1 estimate.** 6 reports to publish: Challenger, Columbia, Deepwater, Leveson, Litvinenko, and Mueller (first publish).
   - **Writes.** Mueller's first index is the largest: about 3,000 paragraphs, about 6 row writes each, so roughly 20,000. The changed paragraphs of Leveson (about 500), Columbia and Challenger add a few thousand more. **Expect roughly 25,000–30,000 writes**, under the 100,000 a day.
   - **Reads:** through the recorded layouts.
   - **It must fit;** otherwise wait for 00:00 UTC.
10. **`pnpm ship --shared --yes`:** publish (`--no-reindex`), deploy, reindex, seed, `verify-prod` and record. Mueller is served from the deploy until its publish step, and the ship deploys first.
11. **The seed is required:** `pnpm seed-highlights --remote` (ship's seed step). It writes Mueller's 11 editor highlights.
12. **After the ship:**
    - `pnpm publish-report --all --status --base https://reportsthatmatter.org` must show all 17 current.
    - `pnpm marks check --all --refresh` (one read; must be clean).
    - `pnpm score` for litvinenko-inquiry, then commit `docs/scores.json`.
    - `pnpm quality ratchet --record`, then commit `reports/quality-last.json` and `reports/verify-last.json`.
    - `reports/pipeline.yaml`: Mueller `reached: publish, state: waiting`; run `pnpm vitest run tests/pipeline-status.test.ts` before committing.
13. **Beads.** Close what each PR names as fixed (`--reason "Shipped in v0.26.0 / site <sha>"`):
    - **H:** 1iz4, jr1t, 1ptg, qai, wjbs.
    - **F:** a3y and the Columbia/Challenger backlog beads #306 names.
    - **G:** gqsy.5 and ga0k (Mueller).
    - **Leave open:** decision oh89 (0020, proposed), 9sgb, wwmg, js2r, cs92.
    - Then `bd dolt push`.
14. **Changelog** (AGENTS.md "Changelog"): Mueller is a new report, so add an entry with screenshots, and run the `report-announce` drafts per the skill. Then `pnpm worktrees prune` (plan only) between sessions.

Reviewer worktrees to drop after the ship:

- `rtm-review026-1010` and its `-reports/` (run `pnpm ingest link --restore` there first)
- `ingest-review026-1009`, `ingest-scratch73-1010`
- `rtm-review306-1010`, `rtm-review307-1010`, `rtm-review309-1010`

## Batch I (CIA)

There are no PRs yet. When the CIA agent opens its repo, ingest and site PRs, review them against v0.26.0 as a separate trial: new-report checks, decision number 0021 or later, and the corpus union.
