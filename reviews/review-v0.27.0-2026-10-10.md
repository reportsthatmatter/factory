# Agent review of the v0.27.0 release (batch I: the Senate Torture Report, 2026-10-10)

Reviewer: Opus agent. I made no merge, release, deploy, publish or seed, and no D1 call of any kind.

Base: `@rtm/ingest` main `9c0781f`, which is v0.26.0 plus #78 (`KNOWN_PAGE_PASS_NAMES` one per line; merged, no output change, rides this release), and site main `7a26685e` (#317 merged).

**Verdict: SHIP v0.27.0 and publish the Senate study at grade B-.** That follows Rufus's default for the grade question. `PROCESSING.md` states the limits honestly; see below.

## Verdicts

| PR | Head to merge | Verdict | Note |
|---|---|---|---|
| ingest #77 `lines-passes-1009` | **d1052a55** | MERGE | Based on current main; CI green. Adds `contentsOutline({ ocr })`, a report-defined `source` stage (`SourcePass`), and the uncoded `[Redacted]` shape in `redactions.ts` and `fidelity.ts` |
| ingest #78 | merged `9c0781f` | rides v0.27.0 | Reviewed in `review-pr317-2026-10-10.md` |
| us-senate-cia-torture-report#1 `first-ingest-1009` | **057548d** (was af93bc0) | MERGE after the release | Reviewer fixup: `PROCESSING.md` says where the boxes not shown went. Pins v0.26.0; `bump-pin` moves it to v0.27.0 |
| site #312 `cia-torture-report-1009` | **d7dbaba6** (was 8ad5c7ed) | MERGE | Reviewer fixups below |

## Trial

- **Ingest trial:** `~/src/reportsthatmatter/ingest-review027-1010` is main plus #77, squashed with no conflict.
  - `pnpm build` leaves `dist/` clean.
  - `typecheck` is clean and **922/922 tests** pass.
- **Site trial:** `~/src/reportsthatmatter/rtm-review027-1010` is main plus #312 (a fast-forward), linked to the ingest trial.
- **Report worktrees:** `~/src/reportsthatmatter/rtm-review027-1010-reports/*` holds all 18 reports, with the study's repo at its PR head.
- **No other report moves.** `pnpm ingest check` passes for **all 18** against their committed baselines (`markdownSha`, so the text is byte-identical): the 17 live reports are unchanged, and the study reproduces its PR's `full.md` byte for byte. Every report's site copy equals its repo copy.
  - **Shared-pass changes in #77 are neutral.** `FOOTNOTE_INLINE` now also accepts "[Redacted]". `contentsOutline` and `layoutPageJoins` gain options, and there is the new source stage. None of them moves Mueller, the other `foiaRedactions` and `contentsOutline` user.
  - `ingest recheck --passes contentsOutline,foiaRedactions` matched every correction once.
- **The rest of the suite passes:**
  - `typecheck` is clean; `test` gives **1110 passed, 1 skipped**.
  - `corpus check`: "18 report(s) unchanged".
  - `quality check`: within budget; `pnpm quality ratchet`: "Nothing to lower".
  - `aliases check`, `ingest anchors --check` (within budget) and `ingest crosscheck` pass.
  - `editorial`: 18 files.
  - `posts --verify`: the queue is up to date.
  - `cards --check`: 117 cards match.
  - `pipeline status` exits 0.
  - `pnpm decisions check`: "numbering ok"; `pnpm lessons check`: ok.

## The gate, item by item

- **The text is honest about its limits.** `PROCESSING.md` says that:
  - the words are the OCR's, with examples;
  - redaction placement is approximate;
  - **about 270 notes are merged** into the note before them, and **about 84% of note markers are linked**;
  - **about 57 paragraphs break mid-sentence and about 100 carry false quotation lines** (jc46, untouched);
  - the minority views' sub-headings are partly paragraphs;
  - a few classification markings remain, and tables are shown as OCR text.

  It names the source (GPO's govinfo PDF, with SHA-256) and says why there is no better edition: the Committee's own release is the same scan, and govinfo's HTML is images. My one addition: it said 11,680 boxes were found and 9,947 redactions shown, without saying where the difference went. It now says the rest sit in the classification banners and portion markings that are left out.
- **Redactions follow decision 0020, including its uncoded addendum.**
  - #312 adds the addendum to `0020-redactions.md` and creates no new record. The next free number stays **0021** (`pnpm decisions next`).
  - The text has **9,947 `[Redacted]`** and **0 coded `[Redacted: …]`**: right, because a declassified report prints no FOIA codes.
  - The report's own **1,480 "[REDACTED]"** (capitals: its substitutions for names), its pseudonyms and its bracketed site names are kept.
  - `PROCESSING.md` explains the difference ("Words the report itself prints in brackets are its own and are kept"), and the addendum records the risk that the two look alike, with the option to change the marker word.
  - Both counts are identical in the rendered `full-body.html` (9,947 and 1,480), so no marker is lost to Markdown (none becomes a link or a reference definition).
  - The box positions come from a committed pack checked by SHA-256 (`reference/redactions.json.gz`). The build reads it and runs no detection or network call.
- **The introduction meets `docs/report-introductions.md`.**
  - **Counts:** `why_it_matters` 65 words (in 50–80); `background` 269 words in 3 paragraphs; 7 findings, 4 with stand-alone quotations; 5 reading-guide sections; 14 highlights, 3 of them `card: true`, all on numbered pages.
  - **Voice:** sober and attributed ("The Committee concludes…", "It finds…"). Judgments are in the report's words: the finding title "brutal and far worse than the CIA represented to policymakers and others", and Feinstein's "personal conclusion" that detainees were tortured, quoted as hers.
  - **Minority dissent:**
    - In the background: Chambliss and five others "dispute its method and many of its conclusions", and the CIA contested findings.
    - Finding 7 quotes the minority on the decision not to interview witnesses.
    - The reading guide ends with the minority views.
  - **Facts checked:**
    - the 17 September 2001 memorandum of notification;
    - Executive Order 13491 of 22 January 2009;
    - the 14–1 vote of March 2009;
    - approval 9–6 on 13 December 2012;
    - the CIA response, June 2013;
    - sent for declassification April 2014, released 9 December 2014;
    - the McCain–Feinstein limit enacted November 2015;
    - 119 detainees, at least 26 wrongfully held, $81 million to the contractors.
  - Every quote is verbatim (`pnpm editorial` passes).
- **Aliases, editorial and cards are consistent.**
  - `aliases.yaml` maps the branch's earlier ids, never published, to the current ones.
  - `published-ids` and `published-sections` (55 section pages) are present.
  - The default card and 3 quote cards are generated, and the queue is up to date.
  - **Fixed:** `src/generated/editorial.ts` had **no entry for the report**, so its landing page would have shown no introduction until the editorial was regenerated. Regenerated in `de5fcbcd`. This is the third PR in a row with a stale generated editorial (#304, #306, #312). I appended the evidence to `reportsthatmatter-v724` and raised it to P2.
- **Not done in the PRs:** `reports/pipeline.yaml` has no row for the report, and `docs/CHANGELOG.md` has no entry. Both belong to the integrator's record step (below).

## Integrator steps (following `.claude/skills/integrate-and-ship/SKILL.md` and `docs/release-checklist.md`)

0. **Prepare.**
   - `git -C <each shared checkout> pull --ff-only` and `status` clean.
   - Clone the new repo as a sibling: `git clone git@github.com:reportsthatmatter/us-senate-cia-torture-report ../us-senate-cia-torture-report`. Its PDF is in the repo; check that `archive/CRPT-113srpt288.pdf` is present after the clone.
   - `unset RTM_REPORT_DIRS RTM_REPO_ROOT`.
   - `pnpm wrangler d1 list` must show `reportsthatmatter-marks`; `pnpm d1-usage` must show analytics numbers.
1. **Merge ingest #77** (squash) at **d1052a55**.
2. **Release:** in the shared `ingest/` on main, `pnpm release 0.27.0 --dry-run`, then `pnpm release 0.27.0`. It is minor: #77's new options and the source stage, plus #78.
3. **Merge us-senate-cia-torture-report#1** (squash) at **057548d**. It goes after the release: its `ingest.ts` uses `contentsOutline({ ocr })` and the source stage.
4. **Merge site #312** (squash) at **d7dbaba6**.
5. **Bump the pin**, in a site branch `pin-v0.27.0`:
   - `pnpm bump-pin 0.27.0 --dry-run`, then `pnpm bump-pin 0.27.0 --shared`. That is 18 repos, the study included; it pins v0.26.0 today.
   - Commit and push each report repo's `package.json` and `pnpm-lock.yaml`.
   - `pnpm bootstrap`, `pnpm prerender`, then `pnpm corpus check`: expect "18 report(s) unchanged".
   - `pnpm editorial`, `pnpm cards --check` and `pnpm posts --verify` must change nothing.
   - Open the pin-bump PR and merge it.
6. **Ship:** `pnpm ship --reset`, `pnpm ship --plan` (no guard problem; 1 report to publish), then `pnpm ship --shared`.
   - Every baseline gate should pass, because each repo carries its own baseline.
   - The corpus and editorial gates should be clean.
7. **D1 estimate.** This is the study's **first index**: about 2,350 paragraphs (`<p id>` in its 55 section pages), plus headings and list items.
   - **Expect roughly 7,500–15,000 row writes:** an insert-only first index at 3 per row, up to 6 per row in the worst case. No other report reindexes.
   - **Reads:** the probe, plus the recorded layouts.
   - It must fit today's headroom; otherwise wait for 00:00 UTC.
8. **`pnpm ship --shared --yes`:** publish the study (first publish, `--no-reindex`), deploy (the registry, cards and plate), reindex, seed, `verify-prod` and record.
   - The study is served from the deploy until its publish step, and the ship deploys first.
9. **Seed:** `pnpm seed-highlights --remote` (ship's seed step) writes the study's 14 editor highlights.
10. **Record** (in a site PR):
    - **`docs/CHANGELOG.md`:** a new-report entry with screenshots of the landing page and a section with redactions (AGENTS.md "Changelog"). Run the `report-announce` drafts.
    - **`reports/pipeline.yaml`:** a new unit, then `pnpm vitest run tests/pipeline-status.test.ts`:

      ```yaml
      - id: us-senate-cia-torture-report
        scope: "S. Rept. 113-288, the declassified Executive Summary, findings, foreword and views"
        bead: reportsthatmatter-gqsy.6
        reached: publish
        state: waiting
        evidence: "ingest #77 (contentsOutline ocr, source passes, uncoded [Redacted]); report repo #1; site #312 (registry, manifest, plate, default and 3 quote cards, editorial approved); decision 0020 addendum"
        notes: "Live <date> (ingest v0.27.0, grade B-). OCR text; ~270 notes merged, ~84% of markers linked; jc46 57 severed / ~100 severed-into-quote."
      ```

    - **Record files:** `pnpm quality ratchet --record`, then commit `reports/quality-last.json`, `reports/verify-last.json` and `docs/scores.json` if `record` changed them.
11. **After the ship:**
    - `pnpm publish-report --all --status --base https://reportsthatmatter.org` must show all 18 current.
    - `pnpm marks check --all --refresh` (one read).
    - **Beads:** close gqsy.6 with "Shipped in v0.27.0 / site <sha>". Leave open jc46 (the study's severed paragraphs), oh89 and 8fsl (decision 0020), and v724.
    - `bd dolt push`.

Reviewer worktrees to drop afterwards:

- `rtm-review027-1010` and its `-reports/` (run `pnpm ingest link --restore` there first)
- `ingest-review027-1010`
