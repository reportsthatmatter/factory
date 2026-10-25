# Review: Thirlwall Inquiry summary report (site #403, uk-thirlwall-inquiry#1), 2026-10-25

Reviewer: Claude (Opus 5.5) agent `thirlwall-review`. I did not touch production: no merge, no release, no pin bump, no `publish-report` (only `--dry-run --offline`), no deploy, no reindex, no `seed-highlights`, no `pnpm ship` beyond `--plan`. Two fixup commits pushed to the site PR branch (below).

**Verdict: MERGE both, then SHIP (report repo PR #1 first, then site #403 at `cda76e9f`). No must-fix left open.**

## Scope

| PR | Branch | Head at start | Head after fixups |
| --- | --- | --- | --- |
| reportsthatmatter/uk-thirlwall-inquiry#1 | `summary-1024` → `main` (README stub `d067ac8`, fast-forwardable) | `c874bf98` | `c874bf98` (unchanged) |
| reportsthatmatter/reportsthatmatter#403 | `thirlwall-summary-1024` → `main` (`7abbd0a6`) | `6a58533e`, then `8ff869f6` (author's changelog commit, pushed after I started) | `cda76e9f` |

Bead: reportsthatmatter-gqsy.16 (stages .16.1-.16.5). No ingest PR; pin stays @rtm/ingest v0.33.0 (`pnpm ship --plan`: "0 of 21 repos need a pin bump").

## Trial

- Site: `~/src/reportsthatmatter/rtm-review403-1024`, branch `trial403-1024` from `origin/main` `7abbd0a6`, `git merge --no-ff origin/thirlwall-summary-1024` (clean, at `6a58533e`, then `8ff869f6`, then my fixups), `pnpm bootstrap` (21 reports prerendered; uk-thirlwall-inquiry 4 sections, 38,710 words).
- Report repo: `~/src/reportsthatmatter/rtm-review403-1024-reports/uk-thirlwall-inquiry`, branch `trial403-1024` from `origin/main`, merged `origin/summary-1024` (clean). Archive PDF SHA-256 matches `a1e1a50e…c2db2`; `full.md` and `PROCESSING.md` byte-identical to the site's copies. All checks ran with `RTM_REPORT_DIRS=~/src/reportsthatmatter/rtm-review403-1024-reports`.
- Main has not moved since `7abbd0a6`; `git merge-tree origin/main origin/thirlwall-summary-1024` is clean.

Logs in `~/src/reportsthatmatter/factory/logs/2026-10-24/rtm-review403-1024-*.log`.

| Check | Result |
| --- | --- |
| `pnpm typecheck` | pass |
| `pnpm test` | pass, 91 files, 1465 passed, 1 skipped (re-run after fixups: same) |
| `pnpm editorial --check` | pass; uk-thirlwall-inquiry approved, 8 findings, 4 to read, 6 quotes, 14 highlights, hero; generated file current |
| `pnpm posts --check` | pass; no Thirlwall item; `grep -c thirlwall marketing/queue.yaml` = 0; no `card: true` in the file |
| `pnpm ingest verify uk-thirlwall-inquiry` | pass; golden 0/8 match, 8 known failures (e9im, hy63, b1za, o1jj) |
| `pnpm ingest check uk-thirlwall-inquiry` | pass |
| `pnpm quality check` | pass, 21 within budget |
| `pnpm corpus check` | pass, 21 unchanged |
| `pnpm aliases check` | pass; uk-thirlwall-inquiry 474 ids, 4 aliases, 0 unmatched |
| `pnpm quality ratchet --dry-run` | nothing to lower |
| `VERIFY_SHARED=1 ./scripts/verify.sh` | **exit 0** (with `RTM_REPORT_DIRS`, so `ingest aggregate` passes too, unlike the PR body's run); one warning, pipeline drift (fixed below) |
| `pnpm pipeline status` | before fixup: Thirlwall "over-claim: editorial.card-highlights unmet (0 cards, gate wants 3)"; after: `ok` |
| `pnpm publish-report uk-thirlwall-inquiry --dry-run --offline` | 6 objects, 0.6 MB, hash `c0579eb1edc9ec9a` |
| `pnpm ship --plan --state build/review-ship-state.json` | runs nothing; guard stops only on "not main" (expected in a trial) |

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| uk-thirlwall-inquiry#1 @ `c874bf98` | MERGE | Pinned PDF, datapackage, licence, ingest.ts with each pass justified, golden and adjudicated reference; text equals the site's copy |
| reportsthatmatter#403 @ `cda76e9f` | MERGE, then SHIP | Checks green on the trial merge; editorial meets decision 0026 and report-introductions.md after one wording fixup; no cards, nothing queued |

## Fixups pushed (site branch `thirlwall-summary-1024`)

1. `8e55ddaf` editorial, finding 1. It said "no one at the hospital understood ... and that no safeguarding referral was made until March 2018". Para 3.45 says no one "really seemed to understand"; 3.46 says Ms Kelly (head of the safeguarding team) made no referral until March 2018, not that none was made. Now: "no one at the hospital seemed to understand ... and that the head of its safeguarding team made no safeguarding referral until March 2018." `src/generated/editorial.ts` updated to match; `pnpm editorial --check` says current.
2. `cda76e9f` `reports/pipeline.yaml`: the Thirlwall row gets `waive: {editorial.card-highlights: reportsthatmatter-6j4h}`. No cards is deliberate (decision 0026, courtesy emails not yet sent), so the gate item is owned by 6j4h rather than shown as over-claim drift. Evidence corrected from "golden 3/8 + 5 xfail" to "golden 0/8 + 8 xfail (e9im, hy63, b1za, o1jj)", and the notes add o1jj (sub-headings at h4).

## Findings, by severity

None blocking.

**Editorial (the special-attention items)**

- No baby or family is named in our text, not even by the report's letters; no parent or victim impact statement is quoted; the reading guide's "2. The babies" has no excerpt. All six on-page quotations and all 14 highlights are the Chair's own findings about the hospital, its managers, the College, CQC or the NHS (3.45, 3.47, 3.97, 3.206, 4.2, 4.32 on the page; highlights checked by `pnpm editorial` verbatim). No highlight counts deaths or names Ms Letby, so none would be Tier C. No individual manager is named in our text: roles only ("the then Chief Executive", "the head of its safeguarding team").
- Every judgment is attributed ("The Inquiry finds", "The report finds", "the Chair writes", "in the Chair's words"). I checked every cited paragraph against `full.md`: each claim is supported, after fixup 1.
- CCRC stated as a dated fact: "received an application ... in February 2025 and said on 13 February 2026 that a review of her convictions is underway; it had announced no decision by 25 October 2026." I fetched the CCRC Chair's statement: dated 13 February 2026, "A review of Ms Letby's convictions is underway." A web search finds no CCRC decision by 25 October 2026. The Inquiry's 15 September 2026 release confirms 17 recommendations in Chapter 45 and that the Reporting Restriction Orders still apply.
- Background deaths figures match 1.23-1.25 (1-3 a year 2010-2014; 8 in 2015; 5 in the first half of 2016). Background on the convictions and appeals is stated, which the facts need; the background otherwise names only Lady Justice Thirlwall and Ms Letby. `private_people: [Letby]` is set.
- Minor, not changed: finding 3 cites `3-55-baby-p-died`, so a cite link's URL fragment carries the report's letter "baby-p". The id is the report's own words and our text doesn't name a baby. Section aliases `baby-b` … `baby-p` (recorded from the pre-h4 split, never published) also live in `aliases.yaml`. I accept both: they are the report's headings, not ours.
- Minor, not changed: finding 7 says the coroner's office "did not have" the Thematic Review at the inquest; 3.144 has the coroner say it "almost certainly" was not before him, and the Chair calls it "a significant failure by the coroner's office". The Chair treats it as a finding, so the wording stands.
- Pre-existing, site-wide: decision 0026's "visible removal route on every such landing page" isn't on any affected-community landing page yet (Grenfell lacks it too). Bead reportsthatmatter-8gbe covers it; Thirlwall gets it through `affected_community: true` when that lands.

**No cards, no queue.** No `card: true` anywhere in `editorial/uk-thirlwall-inquiry.yaml`; `pnpm posts --check` passes; the queue has no Thirlwall item. The default card (`assets/cards/uk-thirlwall-inquiry/default.png`) is the typographic plate of para 3.47 with the title: no place, logo or person. The hero (Liverpool Town Hall, Rodhullandemu, CC BY-SA 4.0) is recorded in `docs/design/2026-09-27-hero/sources.yaml` with its rationale, and it's on the landing page only.

**Sub-headings at h4 (o1jj): accepted.** At h3 the splitter made 32 sections named after the wrong headings ("Baby P" holding Chapter 3's opening). At h4 the site has the four chapters, which is the reader's structure, and the section slugs (`2-the-babies`, `3-concerns-reviews-errors`, `part-two`) are the ones the o1jj fix should also produce, so the workaround should not move permalinks when it is reverted. The costs, all recorded and beaded: section pages go h1/h2 → h4 with no h3 (a heading-level skip, an accessibility nit), and three more golden pages are xfail on o1jj. Remaining wart, independent of h4: Chapter 1 sits in the first section, titled and slugged `contents` (the cover and contents open it; "4. Part Two" is h3, hy63). The reading guide's `title: "1. Introduction"` override handles the landing page, but the section nav and the section's h1 read "Contents". Aliases will cover the slug when hy63/o1jj are fixed; not a blocker.

**Rendered pages (local `wrangler dev`, port 8794).** `/reports/uk-thirlwall-inquiry`: 200, hero with credit, standfirst, the "Introduction by Reports that Matter" label, background, 8 findings with page cites, reading guide and contents; meta description is the file's `description`; og:image is the default card. `/3-concerns-reviews-errors`: 200, 20 h4 sub-headings, ¶ numbers on paragraphs. `/contents`, `/2-the-babies`, `/part-two`: 200. `?p=3-45-there-safeguarding-team` redirects (302) to its section and anchor. `/baby-p` (the old section alias) redirects to `/2-the-babies`.

## What moves

Only uk-thirlwall-inquiry, new: 474 ids, 4 sections, 6 R2 objects (0.6 MB). Corpus check: the other 20 reports are unchanged.

## D1 estimate

Small. Publish: 1 row (the commit). Reindex: one full index of 474 paragraphs (on the order of 500-2,000 row writes with FTS). Seed: 14 highlight rows. All far under the 1,666,000 daily limit. `pnpm publish-report uk-thirlwall-inquiry --dry-run --yes` gives the exact figure from production if wanted.

## Found only by the trial merge / conflicts foreseen with southport

The southport branch (`southport-vol1-1024`, `7bb0049e`, no PR yet) against this trial (`git merge-tree`) conflicts in three files, all append-only:

- `docs/design/2026-09-12-imagery/sources.yaml`: keep both entries.
- `scripts/score/reference.py`: keep both `REFERENCES` entries.
- `src/generated/marks.ts`: take either side, then rerun `node scripts/imagery/build.mjs` (it generates the file); or keep both entries by hand.

These auto-merge: `reports/manifest.yaml`, `reports/pipeline.yaml`, `reports/score-sets.yaml`. Once southport adds its registry entry and editorial, expect these too:

- `reports/registry.yaml`: append both.
- `src/generated/affected.ts`: regenerate with `pnpm editorial`.
- `src/generated/editorial.ts` and `src/generated/cards.ts`: take main's, then run `pnpm editorial`.
- `tests/posts.test.ts`: if Southport gets a `context_line`, the sorted array in "the live registry flags exactly ..." must carry both `uk-southport-inquiry` and `uk-thirlwall-inquiry`.
- `docs/CHANGELOG.md`: keep both entries, newest first.
- `docs/scores.json`, `reports/quality-budget.yaml`, `reports/anchor-budget.yaml`, `reports/oracle-budget.yaml`: keep both reports' keys.

## Integrator steps

1. Report repo: `gh pr merge 1 --merge -R reportsthatmatter/uk-thirlwall-inquiry` (head `c874bf98`; `main` is its ancestor). Then update the shared checkout, which is on `main` at the stub: `git -C ~/src/reportsthatmatter/uk-thirlwall-inquiry pull --ff-only && (cd ~/src/reportsthatmatter/uk-thirlwall-inquiry && pnpm install --frozen-lockfile)`.
2. Site: `gh pr merge 403 --merge` at head `cda76e9f` (re-check `gh pr view 403 --json headRefOid` first). If southport has merged first, resolve as in the section above, then `pnpm editorial && pnpm test`.
3. In the site's main (shared checkout): `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only` (if `.beads/issues.jsonl` blocks it, `git restore .beads/issues.jsonl` first), then `pnpm prerender && pnpm editorial --check && pnpm posts --check && pnpm aliases check && pnpm corpus check`. Expect 21 unchanged and no Thirlwall queue item.
4. `VERIFY_SHARED=1 ./scripts/verify.sh`: exit 0, and `git status --short reports/` must be empty.
5. Ship: `pnpm ship --plan`, then `pnpm ship --shared --yes`. That runs publish uk-thirlwall-inquiry (never published before, so the Worker deploy comes in the order `ship` gives), deploy, `./scripts/reindex-search.sh uk-thirlwall-inquiry --yes`, `pnpm seed-highlights --remote --yes` (14 highlights) and production verify.
6. On production, open `/reports/uk-thirlwall-inquiry` (hero, credit, introduction), `/reports/uk-thirlwall-inquiry/3-concerns-reviews-errors` and `/reports/uk-thirlwall-inquiry?p=3-206-inquiry-heard-evidence`. `pnpm publish-report --all --status --fail-on-drift --base https://reportsthatmatter.org` must list nothing.
7. Record: `pnpm quality ratchet --record`, `pnpm scorecard --record`, `pnpm grade --record`; then a PR that sets the Thirlwall row in `reports/pipeline.yaml` to `reached: publish` (keep `state: active` and the 6j4h waiver; run `pnpm test` and `pnpm pipeline status` before committing). The changelog entry is already in #403.
8. Beads: close gqsy.16.1, .16.2, .16.3, .16.4, .16.5 and .16.6 on ship. Keep gqsy.16 open (Volumes I-III), and keep 6j4h, e9im, hy63, vsd2, b1za, o1jj, 0iof and 8gbe open. Do not mark any Thirlwall highlight `card: true` or queue one until 6j4h is done.

## Beads

None filed. Noted on gqsy.16: the verdict and this file's path. Existing beads cover every limitation (e9im, hy63, vsd2, b1za, o1jj, 0iof, 6j4h, 8gbe).

## Lessons

- A new report that deliberately has no cards needs a `waive: {editorial.card-highlights: <bead>}` in its pipeline row, or verify's pipeline-record step shows drift. Covid Module 2 and Southport will need the same. (Recorded here; the author's lessons are already on the branch.)

## Getting better faster

1. Verify's `ingest aggregate` failure in the PR body was the missing `RTM_REPORT_DIRS`, not peer noise. With it set, verify exits 0.
2. The pipeline gate's "wants 3 cards" conflicts with decision 0026 for every affected report not yet emailed. A rule that waives it automatically when `affected_community` is true and 6j4h is open would save each such PR a fixup.
3. Checking each finding against its cited paragraphs took 10 minutes and caught the one overstatement. Keep doing it for every affected-community introduction.
