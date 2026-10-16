# Agent review of v0.24.0: ingest #68 and the Grenfell Tower Inquiry Phase 2 Volume 1 (2026-10-04)

Reviewer: Opus agent. I did not touch production: no `wrangler d1`, no publish, no seed, no reindex, no deploy, no merge, no release. Every D1 figure below is measured on a scratch local D1 or reasoned from the jan6 ship's numbers.

**Verdict: SHIP v0.24.0 AND PUBLISH GRENFELL.** Release ingest #68 as v0.24.0, bump every pin, merge reportsthatmatter/uk-grenfell-tower-inquiry#1 (after its pin bump) and site #293 (head **291c2678**, which carries my fixups), then `pnpm ship --shared`. #68 is opt-in: no live report moves. The Grenfell text, introduction, plate and budgets meet the bar Post Office and January 6th met. Under AGENTS.md this review is the gate; nothing waits for Rufus.

Scope as reviewed:

- **ingest #68** (`grenfell-passes`, tip **89242fc**): `furnitureFaces`, `typographicHeadings({ faces, relevel })`, `numberedOpenings`, `figureFaces`. It is based on main `40f461d` (v0.23.0), so it squashes cleanly.
- **uk-grenfell-tower-inquiry#1** (`phase2-vol1`, **fb20b06**). It still pins `#v0.22.0` and needs the bump to v0.24.0 (step 3 below).
- **site #293** (`grenfell-vol1`): **f55f8d03** at review start, **291c2678** after my fixups and a merge of main `d546e91b` (January 6th live, #295, #296).

## Trial

- **ingest** worktree `ingest-reviewG-1004` (branch `reviewG-trial-1004`) is origin/main `40f461d` with #68 squashed. `pnpm build` leaves `dist/` unchanged and `pnpm check-dist` is clean. `pnpm typecheck` is clean and **838/838 tests** pass.
- **Opt-in check:** `pnpm ingest try ../ingest-reviewG-1004` over **all 15 live reports** (including jan6), against the pinned v0.23.0. Log: `~/src/reportsthatmatter/rtm-reviewG-1004-try.log`.
  - Rendered text is unchanged for 15 of 15.
  - "no quality signal moved".
  - The score decisions for all 7 reports with a reference are unchanged (27 rows, 0 flips).
  - No oracle, anchor or quality finding appeared or vanished.
  - No page changed source or folio read.
  - The code agrees: each change is gated on its declared pass. `numberedOpenings` also needs `numberedParagraphs`, and `relevel` needs `faces`. `geometry("per-page")` was already on main.
- **site** worktree `rtm-reviewG-1004` is #293 merged with main `d546e91b`, plus my fixups. Branch `reviewG-trial-1004` is identical to the pushed `grenfell-vol1` apart from the lessons commit. The trial ingest is linked with `pnpm ingest link` into the site and into the Grenfell report worktree `rtm-reviewG-1004-reports/uk-grenfell-tower-inquiry`, which is at #1's head.
  - **Re-ingest:** `pnpm ingest run uk-grenfell-tower-inquiry` reproduces the report repo's `full.md` **byte for byte**, and it equals the site copy. The report repo stays clean afterwards. Results: 2,019 notes, all resolved; 204 headings; 95.8% of the source words retained, every output word in the source.
  - **Full verify:** `RTM_REPORT_DIRS=… VERIFY_SHARED=1 ./scripts/verify.sh` on the final state reports **"All checks passed."**: 412 checks and **966 tests**, browser checks included. Log: `rtm-reviewG-1004-verify3.log`.
  - **Also passing:** `pnpm quality check` (all 16 within budget), `pnpm editorial` (16 files), and `pnpm test` after the pipeline fix below.
- **`pnpm ship --plan`** was run on the trial with a scratch state file. It finds 0 of 16 repos needing a pin bump today, and every live repo's text equals the site copy. The guard stops for only two reasons, both expected before the merges: the site is not on main, and Grenfell has no pin because its main has no `package.json` until #1 merges.

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| ingest #68 | SHIP | Opt-in, measured as above. The README documents all four passes, and there are tests for each. |
| uk-grenfell-tower-inquiry#1 | SHIP after its pin bump | Reproduces byte for byte at #68. golden 7/7 (p.58 is no longer xfail; p.30 was added). It adds `corrections.yaml` c-0001 (note 1148, checked against the page) and `adjudicated.yaml` (30 breaks: reference wrong 2/30, ours 3/30). |
| site #293 | SHIP AND PUBLISH (reviewer fixups pushed, 291c2678) | Fixups listed below. |

## Fixups pushed to `grenfell-vol1`

1. **Background, the government response** (`bac04b3b`).
   - The background said the government "accepted 49 of the report's 58 recommendations in full". The response says "49 of the recommendations are being accepted in full by the government and other responsible organisations".
   - It now reads: "49 of the report's 58 recommendations were accepted in full, by the government and the other organisations responsible, and the other 9, all directed at government, in principle. The government said it fully supports the Metropolitan Police in its investigation."
   - I re-checked this against the GOV.UK response today, and the yaml source comment now quotes it.
2. **Finding cites** (same commit).
   - The finding on "systematic dishonesty by … Arconic, Celotex and Kingspan" cited 2.19, which names none of them, plus 2.20 and 2.42, which name only Arconic and Kingspan. It now also cites 2.21 (Arconic), 2.28 (Celotex: "a dishonest scheme") and 2.37 (Kingspan: "dishonestly relied").
   - The 1999-onwards warnings finding names the Lakanal coroner and the APPG. It now cites 2.12, which names both. `src/generated/editorial.ts` was regenerated; the diff is only these changes.
3. **Plate rights recorded** (`docs/design/2026-09-12-imagery/sources.yaml`).
   - The imprint (PDF p.4) licenses the publication under OGL v3.0 "except where otherwise stated".
   - Nothing on p.65 or in note 232 marks Figure 5.8 as third party, so the plate is used under the OGL.
   - Residual risk: the photograph came in through Dr Lane's expert presentation and may have been taken by a test house. If anyone claims it, swap the plate for one of the Inquiry's own Chapter 5 diagrams.
4. **Held-out caveat** (`reports/score-sets.yaml`, `24e7e51e`).
   - Grenfell stays held-out. But its own opt-in passes were written by reading it and its reference, so its first held-out score (boundary F1 90.9%) measures the report, not whether those passes generalise.
   - Its reference is also over the error ceiling (2 of 30 wrong, 6.7% against 5%), so `pnpm score` warns.
5. **Merged origin/main `d546e91b`** (`31d3adda`).
   - Conflicts: `reports/pipeline.yaml` (kept main's jan6 notes and appended the Grenfell row) and `docs/design/lessons.md` (kept both).
6. **Quality ratchet** (coordinator's request, `259273c2`).
   - `pnpm quality ratchet` on the trial lowers Grenfell `note-reference-sequence` from 1 to 0. The 7mvi false positive is gone with #290's note-sequence fix (bead noted; close 7mvi on ship). Nothing else moved.
7. **Main is red; fixed here** (`dbda99a2`).
   - #296 set jan6 to `reached: editorial, state: done` (the jan6 review's step 8 said so). `state: done` is not a valid state, so `tests/pipeline-status.test.ts` fails on main today.
   - It now reads `reached: publish, state: waiting`, as every live report does. Merging #293 turns main green.
8. **Lessons** appended (`291c2678`).

## Grenfell: text, tone and editorial

- **Tone.** This concerns 72 deaths, and the introduction treats it soberly.
  - Our wording is plain and attributed: "The Panel concludes", "The report finds".
  - The judgments are the report's own words in quotation marks: "systematic dishonesty", "muddled, slow, indecisive and piecemeal", "death trap" (1.3).
  - No adjective of ours judges anyone.
  - It says what an inquiry cannot do: it may not rule on liability (1.14), and "With some exceptions we have not attempted to apportion blame" (1.16).
  - It records the race and social-background question as the Panel answers it (1.22, 1.25).
  - It says nothing about prosecutions beyond the government's sentence on the Met investigation. That is right while charging decisions are pending.
- **Verbatim.** `pnpm editorial` passes, and I read every finding, excerpt and highlight against `full.md`. The background's in-report facts match:
  - Flat 16: 1.2.
  - 30 October 2019: 1.1.
  - 27 January 2020 and 312 days: 1.8.
  - 247 warning letters: 1.11.
  - Section 2(1): 1.14.
  - The MCM ban and the building control reforms: 1.17.
  - Refurbishment 2012-2016: 4.1.
- **Sourced outside facts**, each checked today against the GOV.UK response:
  - 72 dead, 18 of them children.
  - The kitchen of Flat 16.
  - The Inquiries Act 2005 and the chairman.
  - 26 February 2025.
  - 49 accepted in full and 9 in principle.
  - The Met sentence.
  - Phase 2 published 4 September 2024: the GOV.UK publication page.
- **Cards and posts.** Four `card: true` quotes: 1.16, 2.19, 2.103 and 2.114. Each is a full sentence with its own subject and none needs context. They are queued 19-22 December.
- **Plate.** BS 8414 test rig from Figure 5.8 (PDF p.73). It shows no fire and nothing of the tower; it is an exhibit, and that suits the subject.
  - The figure's white label leader lines show as thin diagonals. This is recorded as a caveat; the embedded image would avoid them.
  - Provenance and OGL: fixup 3 above.
- **Tables and figures.** `PROCESSING.md` "Known limitations" says plainly that the figures, charts and seven tables are not shown, that only their captions remain, and that the PDF is the authority for them (bead bnqs, HTML tables).
- **Rendered pages** (local `wrangler dev --local`), all 200:
  - **Landing:** the fixed background and findings render. There is one `<img>` (the plate), and og:image is the default card.
  - **Sections:** `chapter-2-executive-summary` (11 sidenotes), `chapter-5-fire-testing` (130 sidenotes, figure captions only, no figure words) and `part-1-introduction`.
  - **Links and search:** `?p=2-86-rbkc-s-building` gives a 302 to chapter 2 with the anchor (f951's paragraph exists). A search for "systematic dishonesty" returns 2.19 with `?h=`.
- **Measures (as #293 states, reproduced):**
  - 843 citable paragraphs, 798 numbered with the sequence complete, 2,019/2,019 notes linked, 204 headings.
  - Anchors 0/0/0, golden 7/7, held-out boundary F1 90.9%.
  - Quality within budget after the ratchet.
  - **Grade A-**: the only gap is the absent tables.

## Advisory signal `numbered-paragraph-glued`

`advisory: true` puts it outside `GATED` (`src/lib/quality/budget.ts`) and gives it no budget in `quality.mjs`, so it is never gated. Leveson shows 3 (marked "(advisory)"), all true (bead 1iz4), and `pnpm quality check` says "All 16 reports within budget". Grenfell shows 0. It has unit tests. Declaring `numberedOpenings` for Leveson is 1iz4's follow-up and would move ids there, so it is not part of this release.

## D1 estimate (free tier: 5M reads and 100k writes a day)

Only Grenfell publishes. The 15 live reports reproduce at v0.24.0 (the try above), so they need no reindex.

- **Writes:**
  - The new report's search index is 843 passages at about 3 rows each (jan6: 6,850 for 2,283), so **about 2.6k**. `scripts/measure-reindex.mjs` on the trial gives a 5,212-row full DELETE plus INSERT for Grenfell, consistent with about 2.6k for the INSERT alone.
  - 12 highlights seeded, plus a handful of version rows.
  - **Total about 2.7k, about 3k with ship's margin.**
- **Reads:**
  - The new report has no recorded layout, so its reindex scans `passages` once: about 45-50k rows. That happens twice (the estimate's dry run and the real run), so about 100k.
  - verify-prod: about 4k.
  - **Total about 105k, about 2% of a day.**
- **Room:** today has already spent the v0.23.0 ship (about 6.8k writes, 190k reads) and the jan6 ship (about 7k writes, 100k reads), so there is plenty left. Check `pnpm d1-usage` first anyway.

## Integrator steps

1. **Before anything:**
   - `bd dolt pull`, then `pnpm d1-usage`.
   - Check origin/main is still **`d546e91b`**. If anything else landed, use the new head as `<old-ref>` below and merge main into #293 again.
   - Check that #293's head is `291c2678` and #68's is `89242fc`.
2. **Ingest release** (in an ingest worktree, never the shared checkout):
   - Squash-merge #68 on GitHub. It is based on current main, so there are no conflicts.
   - `git pull --ff-only` the shared `ingest/`.
   - In a worktree at the post-merge main: `CI=true pnpm install && pnpm build`. Commit `dist` only if it changed; it did not in the trial.
   - `pnpm check-dist`, `pnpm typecheck && pnpm test` (expect 838).
   - `pnpm release 0.24.0 --dry-run`, then `pnpm release 0.24.0`.
3. **Grenfell report repo pin**, in a worktree of `uk-grenfell-tower-inquiry` on `phase2-vol1`:
   - Set `"@rtm/ingest": "github:reportsthatmatter/ingest#v0.24.0"`.
   - `pnpm install --no-frozen-lockfile`. Check that `pnpm-lock.yaml` resolves to the v0.24.0 commit, not v0.22.0's; a `sed` on the lockfile is not enough.
   - `RTM_REPORT_DIRS=<that worktree's parent> pnpm ingest run uk-grenfell-tower-inquiry` from a site worktree at v0.24.0. Expect no change to `full.md` or `baseline.json`.
   - Commit "Pin @rtm/ingest v0.24.0" and push.
   - Merge #1 (merge commit), then `git -C ~/src/reportsthatmatter/uk-grenfell-tower-inquiry pull --ff-only` and `pnpm -C ~/src/reportsthatmatter/uk-grenfell-tower-inquiry install`. Its main has had no `node_modules` until now.
4. **The other 15 report repos' pins** (the frozen-lockfile lesson: ship's bump step cannot finish on its own). In each shared checkout, on `main`, or `master` for columbia-accident and uk-leveson-inquiry:
   - Set the pin to `#v0.24.0`.
   - `pnpm install --no-frozen-lockfile` (run `pnpm -C <repo> install` first if it has no `node_modules`).
   - Commit "Bump @rtm/ingest to v0.24.0" and push.
   - Then `pnpm ingest preflight` from the site should list all 16 at 0.24.0.
5. **Site:**
   - Merge #293 (merge commit). It conflicts with nothing on `d546e91b`.
   - Pull the shared site checkout `--ff-only`.
   - On main, bump `package.json` to `github:reportsthatmatter/ingest#v0.24.0` and run `CI=true pnpm install --no-frozen-lockfile`. Check that `pnpm-lock.yaml` names the v0.24.0 tarball commit.
   - Commit "Bump @rtm/ingest to v0.24.0" and push, then `pnpm prerender`.
   - Until this bump, main's `pnpm ingest check` cannot re-ingest Grenfell, because its passes are not in v0.23.0.
6. **Plan:** `pnpm ship --plan`. Expect:
   - 0 of 16 repos needing a bump.
   - Every repo clean on main.
   - Every text equal to the site copy.
7. **Run:** `VERIFY_SHARED=1 pnpm ship --shared --old-ref d546e91b` (or the head you found in step 1). Add `--d1-limit <writes left> --d1-read-limit <reads left>` if ship cannot read usage. Expected stops:
   - **baseline:** nothing should move. All 15 live reports are byte-identical under #68, and Grenfell reproduces its `baseline.json`. If anything moves, stop and read it: it means something other than #68 changed.
   - **aliases:** Grenfell is unpublished (843 ids seeded, no aliases), and nothing else changes.
   - **corpus:** Grenfell's baseline is accepted in #293, so this should pass.
   - **editorial / cards:** these should pass, because cards, the queue and the generated editorial are committed.
   - **ratchet:** already run in #293. If ship's ratchet step still dirties main, commit what it writes in a PR (as #295) and `--redo` the step.
   - **status / d1-estimate:** expect exactly **1 report to publish (uk-grenfell-tower-inquiry)**, about 3k writes and about 105k reads. If it lists more, stop.
8. **Production:** `pnpm ship --yes`. This runs publish, deploy, reindex (Grenfell only), seed (12 highlights) and verify-prod.
9. **Post-ship checks:**
   - The drift table is empty.
   - https://reportsthatmatter.org/reports/uk-grenfell-tower-inquiry:
     - the background reads "49 of the report's 58 recommendations were accepted in full, by the government and the other organisations responsible";
     - the plate shows the test rig and there is no hero;
     - og:image is the default card.
   - `/reports/uk-grenfell-tower-inquiry/chapter-5-fire-testing`: sidenotes in the margin and page anchors. Figure captions appear, but no figure words ("Chimney", "Combustion chamber").
   - `?p=2-86-rbkc-s-building` redirects into chapter 2 (f951).
   - A search for "systematic dishonesty" includes Grenfell 2.19.
   - `/assets/cards/uk-grenfell-tower-inquiry/q-f60c81ce.png` returns 200.
   - Highlights show as marks in chapter 2.
   - Spot-check one live report, Leveson: unchanged.
10. **Record:**
    - Changelog: the Grenfell Tower Inquiry Phase 2 report, Volume 1, is on the site. It has 843 citable paragraphs, 2,019 linked notes and its plate. It is searchable and has an approved introduction. The tables are not yet shown, and the hero waits.
    - `reports/pipeline.yaml` Grenfell: `reached: publish`, `state: waiting`. **Not `done`**, which fails `tests/pipeline-status.test.ts`.
    - Run `pnpm quality ratchet --record` and `pnpm scorecard --record --no-score`, then commit.
    - Close gqsy.4's Volume 1 stage, f951, 7150, cx0y, 7mvi and ni9o's fixed part (if the adjudication agrees). Leave gqsy.4.1 (hero), gqsy.4.2, bnqs, f1o1 and 1iz4 open.

## Beads

- **Notes added:** gqsy.4 (review summary), 7mvi (the false positive is gone; close on ship), and b78.1 (two defect classes no check caught: a finding naming parties that appear in none of its cites; an invalid pipeline state committed by ship's record step).
- **Filed:** none new; f1o1, bnqs, ni9o and 1iz4 already cover what I saw.

## Getting better faster (reviewer retro)

- **What slowed me down:**
  - Main moved twice mid-review (#295, #296). The second move brought a red test from the jan6 record step, which only showed up in my verify rerun.
  - `pnpm install` after merging main silently undid the site's ingest link while the report worktree stayed linked (irfs). Re-linking needs `--restore` first.
- **What would have caught these earlier:**
  - Ship's record step could run `pnpm test` (or at least the `pipeline-status` test) before committing `pipeline.yaml`.
  - `pnpm editorial` could warn when a proper name in a finding's text appears in none of its cited paragraphs.
- **One proposal:** add `--report <id>` to `pnpm ingest try` with `--report-ref <branch>`, so the opt-in check and the new report's reproduction are one command. Today it is a `try` over 15 reports, then a manual link and re-ingest of the new one from its PR branch.
