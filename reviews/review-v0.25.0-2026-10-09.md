# Agent review of the v0.25.0 release (batches A–E, 2026-10-09/10)

Reviewer: Opus agent. I made no merge, release, deploy, publish, seed or D1 write.

**One production read, by mistake:** `pnpm marks check --all --candidate assets/generated --offline`.

- `--offline` is not a flag. With no cache present, the script read the production `mark` table once: one grouped SELECT, **738 rows read, 0 written**, ledger `2026-10-10T00:08:38Z`.
- That is negligible against the 5M daily read limit. It is filed as `reportsthatmatter-cs92`: scripts that can reach production must reject unknown flags.
- Every later marks replay used that export through `--marks-file`.

**Verdict: SHIP v0.25.0.** Merge everything below at the listed heads, in the order given, then follow the integrator steps.

## Verdicts

| Batch | PR | Head | Verdict | Note |
|---|---|---|---|---|
| — | ingest #69 (CI) | merged `d208919` | done | Already on ingest main |
| A | ingest #70 numbered-body page joins (N1/N2) | **d4471a1d** | MERGE | Reviewer fixup: `dist/` rebuilt. It had a `// TEMP` `RTM_PB_TRACE` debug line that `src/` lacks, so `check-dist` failed. CI passes |
| B | ingest #71 floats, letter foot, `unfinished()` | **af198740** | MERGE | Its `dist/` is current: the trial rebuild changes nothing. No CI run, because the PR predates #69 |
| D | ingest #74 burn-down passes | **015a860c** | MERGE | Reviewer fixup: `dist/` added. The branch had committed only `src/`, so a pin to the tag would have shipped none of its passes. CI passes, 844/844 |
| E | ingest #72 autolinks | **d9540cb3** | MERGE | Its `dist/` is current; no CI run (predates #69) |
| A | uk-post-office-horizon-inquiry#2 | 564e69b9 | MERGE | |
| A | uk-grenfell-tower-inquiry#2 | fcbc81fd | MERGE | |
| A | uk-chilcot-inquiry#11 | f2a3b1a9 | MERGE | |
| A | uk-saville-inquiry#9 | fdcb510c | MERGE | PDF shadow only |
| A | uk-hillsborough-panel#12 | 44973ace | MERGE | PDF shadow only |
| A | uk-litvinenko-inquiry#11 | ce77e64d | MERGE | Adjudication only |
| B | us-jan6-committee#3 | fea0ce9b | MERGE | Carries no `full.md` or baseline: ship writes them (step 8) |
| B | jack-smith-report#12 | db53e2b5 | MERGE | Same as jan6#3 |
| D | us-lehman-examiner#11 | 3c8b3cb0 | MERGE | |
| D | us-psi-financial-crisis#14 | 1dd2c439 | MERGE | |
| D | us-v-philip-morris#10 | 3f43eddd | MERGE | |
| A | site #300 | **73108d09** | MERGE | |
| B | site #303 | **fb658f5f** | MERGE | Keeps decisions 0015/0016 |
| C | site #301 launch audit | **b6c5c10f** | MERGE | Reviewer fixup: decisions renumbered 0015/0016 → 0017/0018 |
| D | site #304 | **4d691542** | MERGE | Reviewer fixup: replacement Philip Morris highlight; generated editorial regenerated |
| E | site #305 | **b52cb374** | MERGE | Reviewer fixups: decision 0017 → 0019; reviewer lessons |

## How I tested

- **Ingest trial:** `~/src/reportsthatmatter/ingest-review025-1009`.
  - It is ingest main v0.24.0, with #70, #71, #74 and #72 squashed in that order. There were no conflicts.
  - #74 touches neither `pagebreaks.ts` nor `edition.ts`, as it claimed; the only file shared between PRs is `define.ts`/`passes.ts` with #70, in different hunks. #72 touches only `markdown.ts`.
  - `pnpm build` leaves `dist/` clean. `pnpm typecheck` is clean and **859/859 tests** pass.
- **Site trial:** `~/src/reportsthatmatter/rtm-review025-1009`.
  - It is origin/main `e89ed333` (#299 and #308 included), merged with #300, #303, #301, #304 and #305.
  - It is linked to the ingest trial with `pnpm ingest link`.
  - The only conflicts were appends to `docs/design/lessons.md` and rows in `docs/decisions/README.md`.
- **Report worktrees:** `~/src/reportsthatmatter/rtm-review025-1009-reports/*` holds all 16 reports, each with its report PR merged in.
- **Corpus moves are exactly the union of the claims.**
  - `pnpm ingest check` passes for all 16 reports against their baselines. `markdownSha` is a text hash, so every report re-ingests byte for byte. The baselines are each report PR's own; for Jack Smith, 9/11 and Jan6, they are ones I wrote at the A+B stage.
  - Each report's `full.md` equals the site trial's `reports/<id>/full.md`.
  - Adding #74 then #72 moved nothing outside batch D. So #74's default-path `layout.ts` fix (a raised marker inside a fragment's run of spaces) is neutral beyond Philip Morris.
- **The rest of the suite passes on the trial.**
  - `pnpm typecheck` is clean, and `pnpm test` gives **1068 passed, 1 skipped**, after merging #308.
  - `quality check`: all 16 within budget.
  - `aliases check` passes.
  - `editorial`: 16 files.
  - `ingest anchors --check` and `ingest crosscheck` pass.
  - `ingest recheck` over every changed pass matched every correction exactly once.
  - `posts --verify`: the queue is up to date.
  - `cards --check`: 106 cards match.
  - `pnpm quality ratchet` says "Nothing to lower: every budget is at its current count". The ratchet is already in the PRs.
- **Marks, links and editorial replayed** (`marks check --all --marks-file <export>`). The results are under "Other findings".

## What moves (expected at the ship's baseline gate)

| Report | Batch | full.md bytes | Lines | Ids |
|---|---|---|---|---|
| uk-post-office-horizon-inquiry | A | 501,243 → 501,237 | +6 −18 | 6 joins; 6 aliases |
| uk-grenfell-tower-inquiry | A | 733,718 → 733,716 | +2 −6 | 2 joins; aliases |
| uk-chilcot-inquiry | A | 379,896 → 379,895 | +1 −3 | 1 join; 1 alias |
| jack-smith-vol1 | B | 402,238 → 401,097 | +6 −28 | letterhead footer gone; 2 paragraphs rejoined; aliases |
| us-911-commission | B | 1,938,296 (same size) | +8 −8 | 4 paragraphs reordered past box floats (page order, decision 0016); no id changes |
| us-jan6-committee | B | 2,535,820 → 2,536,017 | +184 −160 | italic captions; 12 more paragraphs (curly-quote endings); appendix parts one level deeper; 5 section aliases |
| us-lehman-examiner | D | 1,174,944 → 1,175,026 | +191 −227 | 31 new ids; aliases |
| us-psi-financial-crisis | D | 2,059,109 → 2,059,523 | +157 −189 | ~100 subheads; 32 new ids; aliases |
| us-v-philip-morris | D | 3,188,607 → 3,189,250 | +152 −196 | notes 7–58 defined; aliases |

**Unchanged text (7):** Challenger, Columbia, Leveson, Deepwater, Hillsborough, Saville and Litvinenko. Saville and Hillsborough move only in the PDF shadow.

**#72 changes only the rendered HTML.** `pnpm corpus check` flags rendered word counts: Challenger −5, Columbia −1, Leveson −86 and Jack Smith +1. No id moves. These four need `pnpm corpus accept`, and their publish hashes change.

**Reports to publish (12):** the 9 above, plus Challenger, Columbia and Leveson. Jack Smith is in both lists.

## Checks on the new behaviour

- **N1 and N2 page joins (#70) are correct.**
  - **Post Office.** In each of its 6 joins, the page before ends on a short last line in justified text, so they look like paragraph breaks. But the PDF's own structure tree (`reference/blocks.jsonl`, InDesign styles) has 3.71 as **one** paragraph across pp.25–26, so these are forced page breaks inside one paragraph. The joins are right, and they match the report's adjudication.
  - **Chilcot and Grenfell.** Chilcot 780 and Grenfell 7.44 read the same way on the page images.
  - **Reports N1 was not built on.** I opted Litvinenko and Leveson into `numberedBody` in the trial.
    - Litvinenko: 0 joins.
    - Leveson: 2 joins, 5.83 and 5.158. Each rejoins a quotation's second half, across the page, to its first half. That first half was already wrongly run into its paragraph before this change. They are not false joins.
    - Neither report opts in with this release.
  - **Not re-measured:** I did not re-derive the dev and held-out score figures.
- **Floats (#71) and Jan6 (jan6#3).**
  - On the rendered pages, captions are `<em>`, and caption ids are unchanged (Jan6 `aliases: {}`).
  - Appendices 1 and 3 are now one section each, with five section aliases. Levels move by one, with no flattening at level 6.
  - The anchor budget is lower: Jan6 markers-wrong 39 → 10 and blocks-wrong 30 → 3; 9/11 blocks-wrong 4 → 0.
  - The 9/11 note-reference-sequence budget rises 0 → 4, with a why. Decision 0016 is `proposed`, and the cost of page order is recorded there.
- **Jack Smith letter foot (jack-smith#12, #71).** The Blanche letterhead footer is gone from the body and from notes 8 and 21, and the paragraphs it had split are rejoined.
- **Batch D text.**
  - Comparing word multisets before and after, Philip Morris loses or gains no word.
  - Lehman's net change is five hyphenation tokens: "quarterend" ×2 → "quarter‐ end", "mortgage backed" → "mortgagebacked", "email" → "e‐ mail". These are new page-join hyphen artefacts: **Low, follow-up**.
  - PSI has one: "subprime" → "sub- prime".
- **#72 links.** Rendered with the trial ingest against #74's renderer, on all 16 reports:
  - **Challenger:** 11 → 0 links, all junk (`two.ls`, `sp.ee`, `q.cn`).
  - **Columbia:** 7 → 5 (`cu.in`).
  - **Leveson:** 416 → 182.
    - The `/wpcontent/` hrefs become `/wp-content/`, and the hyphen-wrapped transcripts are joined into full `.pdf` URLs.
    - The truncated links were dropped, along with the `http://org.uk/...` links, which came from a host wrapped at a dot ("levesoninquiry. org.uk") and were broken before too.
  - **Jack Smith:** `perma.cc/HD2R-` → `perma.cc/HD2R-6U8Y`.
  - **The other 12 reports:** unchanged.
  - **No working link was lost.** As a follow-up (Low), #72 could join a host wrapped at a dot.

## Other findings

1. **Philip Morris lost an editor's highlight.** #304 found that `indeed-day-none-defendant-cigarette` is really note 13's text, and dropped it from the yaml.
   - **Replacement (reviewer, `4d691542`):** `defendants-internal-research-reflects-their` (p.1539), verbatim: "Defendants' internal research reflects their understanding that nicotine is the most important chemical delivered by cigarettes because it is what compels smokers to smoke."
   - The nicotine finding now cites it too, and the file has 13 highlights again.
   - **The marks replay flags one mark that would stop anchoring:** the old highlight's row, exact text, 1 "reader". It is the editor's row. `pnpm seed-highlights --remote` deletes editor rows that no file asks for, so it goes in step 11. The post-ship marks check must then be clean.
   - Four 9/11 and Deepwater queue links still anchor, but more weakly (their context moved).
2. **Decision numbers for the release.**
   - **Final:** 0015 captions (#303), 0016 page order (#303), 0017 about copy (#301), 0018 phone nav (#301), 0019 `/full` previews (#305). Mueller's record takes 0020 at its merge.
   - **Why #303 keeps its numbers:** ingest #71's README and the 9/11 budget comment cite decision 0016, so renumbering #301 instead touches no ingest file.
   - **Renames pushed:**
     - #301: `0017-about-copy-no-commentary.md`, `0018-phone-header-navigation.md`, the titles, the decision references in `docs/research/2026-10-09-launch-audit.md`, and the README rows.
     - #305: `0019-full-page-for-link-previews.md`, its README row, the `tests/full-head.test.ts` comment and the line in `lessons.md`.
   - Notes appended to beads pbkk, nupj and yvfr.
   - **Guard:** `reportsthatmatter-js2r` (`level:specced`, handoff): a test that decision numbers are unique, plus the convention that a number is taken when the PR opens.
3. **Low: `tests/cards.test.ts` can read stale data.** It trusts a cached `build/editorial-highlights.json`. In a worktree bootstrapped before #305 merged, it reports #305's two cards as orphans until `pnpm editorial` reruns. This is pre-existing and recorded as a lesson.
4. **Report PRs on the old pin.** The A, B and D report PRs import passes that v0.24.0 lacks (`holdNoteSequence`, `footnoteResets`, `noteFaceRunOver`) while pinned to v0.24.0. **Each repo's main fails to ingest until its pin is bumped.** Merge them only after `pnpm release 0.25.0`, and bump the pins straight after.

## Integrator steps (following `.claude/skills/integrate-and-ship/SKILL.md` and `docs/release-checklist.md`)

0. **Prepare.**
   - `git -C <each shared checkout> pull --ff-only` and `status` clean (site, ingest, all 16 report repos; Leveson and Columbia are on `master`).
   - `unset RTM_REPORT_DIRS RTM_REPO_ROOT`.
   - `pnpm wrangler d1 list` must show `reportsthatmatter-marks`.
   - `pnpm d1-usage` must show "analytics:" with numbers, not "not available".
1. **Merge the ingest PRs**, squash, at the heads above, in this order: **#70 → #71 → #74 → #72**. Check `gh pr view <n> --json headRefOid` first.
2. **Release:** in the shared `~/src/reportsthatmatter/ingest` on main, `pnpm release 0.25.0 --dry-run`, then `pnpm release 0.25.0`. It is minor: new opt-in passes, and #71's default float rule, which moves 9/11 and Jan6.
3. **Merge the report PRs** (squash, heads above), immediately before step 5:
   - **A:** PO#2, Grenfell#2, Chilcot#11, Saville#9, Hillsborough#12, Litvinenko#11.
   - **B:** jan6#3, jack-smith#12.
   - **D:** lehman#11, psi#14, pm#10.
   - Within a batch the order does not matter; there is no cross-repo dependency.
4. **Merge the site PRs** (squash), in this order: **#300 → #303 → #301 → #304 → #305**.
   - `docs/design/lessons.md`: keep every section.
   - `docs/decisions/README.md`: keep every row, in the order 0015–0019.
   - Derived files (`reports/corpus-baseline.json`, `src/generated/*`, `marketing/queue.yaml`): take main's version and regenerate (steps 6 and 8). In my trial they merged cleanly in this order.
5. **Bump the pin**, in a site branch `pin-v0.25.0`:
   - `pnpm bump-pin 0.25.0 --dry-run`, then `pnpm bump-pin 0.25.0 --shared`.
   - Check that each `pnpm-lock.yaml` names v0.25.0's commit.
   - Commit and push each report repo's `package.json` and `pnpm-lock.yaml` to its main.
6. **In the same site branch:**
   - `pnpm bootstrap`, then `pnpm prerender`, then `pnpm corpus check`. Expect only Challenger, Columbia, Leveson and Jack Smith, with word counts only.
   - Read them, then `pnpm corpus accept challenger-accident columbia-accident uk-leveson-inquiry jack-smith-vol1`.
   - `pnpm editorial`, `pnpm cards --check` and `pnpm posts --verify` must change nothing.
   - Open the pin-bump PR and merge it.
7. **Plan the ship:** `pnpm ship --reset`, then `pnpm ship --plan`. The guard should list no problem. The report table should show 12 reports to publish (see "What moves").
8. **`pnpm ship --shared`.**
   - **Baseline gate.** Only Jack Smith, 9/11 and Jan6 should differ from their committed baselines; the others carry theirs from their PRs. Read the diffs against the table above, then `--ack baseline`.
   - **Commit the new text.** Commit and push `full.md` and `baseline.json` (and `fidelity.md` if it changes) in `jack-smith-report`, `us-911-commission` and `us-jan6-committee`.
   - **Corpus and editorial gates:** they should be clean after step 6; `--ack` after reading.
   - **Aliases, cards, queue and ratchet:** they should regenerate nothing. Commit anything they write in a site PR and resume.
9. **D1 estimate.** 12 reports are to publish. The reindex is incremental: changed paragraphs only, and the #72 reports' search passages should be unchanged.
   - Expect a few thousand row writes and well under 1M rows read. It must fit today's headroom.
   - If analytics is unreadable or it does not fit, stop and wait for 00:00 UTC (`pnpm d1-usage`).
10. **`pnpm ship --shared --yes`:** publish with `--no-reindex`, deploy (#301's CSS and templates; #305's code), reindex, seed, `verify-prod` and record.
11. **The seed is required:** `pnpm seed-highlights --remote`. Ship's seed step does it, so check it ran.
    - It adds #305's two Jack Smith highlights and the new Philip Morris highlight.
    - It deletes the old Philip Morris editor highlight row.
12. **After the ship:**
    - `pnpm publish-report --all --status --base https://reportsthatmatter.org` must list nothing to publish.
    - `pnpm marks check --all --refresh` (one read) must be clean.
    - `pnpm quality ratchet --record`, then commit `reports/quality-last.json`, plus `reports/verify-last.json` and `docs/scores.json` if `record` changed them.
    - `reports/pipeline.yaml` rows stay `reached: publish, state: waiting`.
13. **Beads.** Close those each PR names as fixed: sh1b, ni9o, nen (A); bqu0, smof, 5sf1, 2t3t (B); ahzb, p3o8 (C and E); 0bf, 07k, 7go, bi5 (D); y960 (E). Use `--reason "Shipped in v0.25.0 / site <sha>"`.
    - **Leave open:** the decision beads 12s2, hcam, pbkk, nupj and yvfr; the beads #301 filed (te56, fd08, ntjx); 8non (budget raised, not fixed); and js2r and cs92.
    - Then `bd dolt push`.
14. **Housekeeping.** `pnpm worktrees prune` (plan only) between sessions. Reviewer worktrees to drop afterwards:
    - `rtm-review025-1009` and its `-reports/`
    - `ingest-review025-1009`, `ingest-review70-1009`, `ingest-review74-1009`
    - `rtm-review301-1009`, `rtm-review304-1009`, `rtm-review305-1009`
    - Restore the link first: `pnpm ingest link --restore` in `rtm-review025-1009`.

## Follow-ups (not blocking)

- **Low:** new page-join hyphen artefacts in Lehman (4) and PSI (1): "quarter‐ end", "mortgagebacked", "e‐ mail", "sub- prime".
- **Low:** #72 does not join a host wrapped at a dot ("levesoninquiry. org.uk/…"). Those links were broken before too.
- `reportsthatmatter-js2r`: decision numbers unique.
- `reportsthatmatter-cs92`: unknown flags rejected before a D1 read.

## Lessons

Committed to `docs/design/lessons.md` on the #305 branch:

- An ingest PR can ship without its `dist/`.
- Parallel PRs collide on decision numbers.
- An unknown flag can fall through to a production read.
- A test can trust a stale build cache.
