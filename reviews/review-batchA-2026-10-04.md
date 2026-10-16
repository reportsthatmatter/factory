# Agent review of release batch A (2026-10-04)

Reviewer: Opus agent. I did not touch production D1 and did not deploy; no `wrangler d1 --remote`, no publish, no seed, no reindex against production. Site-only batch: no ingest release, no report content change.

Scope:

- **site #287** D1 read budget (`t4al`): migration 0004, reindex layouts, rank-ordered search, `pnpm d1-usage`, ship budgets reads.
- **site #285** SEO structured data (`y2y8.4`).
- **site #283** phone highlighting and the `?h=` landing panel (`bght.3`, `bght.4`), stacked on #279.
- **site #282** docs: marketing outreach lessons, "11" to "13" reports in the launch drafts.
- **marketing#1** outreach drafts (private repo).
- #279 and #278 were already reviewed (`review-site-pr279-2026-10-04.md`) and merge first; they are part of the trial.

Neither repo has CI. I trial-merged, in order, #279, #278, #287, #285, #283, #282 onto `origin/main` at `d72327fb` (the v0.22.0 re-ingest) in the worktree `~/src/reportsthatmatter/rtm-reviewA-1004` (branch `review-batchA-1004`, pushed as **`batchA-trial-1004`**, not for merging: use it as the conflict recipe), then `pnpm bootstrap` and the checks below.

## Verdicts

| PR | Verdict | Reason or exact fix |
|---|---|---|
| site #279 sharing and highlights | **MERGE** first | As reviewed; unchanged (head `6c2147c4`). |
| site #278 lessons: marketing research | **MERGE** second | As reviewed: `docs/design/lessons.md` conflict, keep both sections. |
| site #287 D1 read budget (t4al) | **MERGE** (with one conflict to resolve) | Correct and safe; details under "#287 in depth". Conflicts after #279 in `scripts/seed-highlights.mjs` and `lessons.md` (recipes below). Migration 0004 is additive and needs no rollback for a code revert. Bead filed for a pre-existing race corner (`ib2b`, P3, not blocking). |
| site #285 SEO structured data | **MERGE** (reviewer pushed a fixup) | Fixup `b39a470a` on `seo-structured-data`: the PR silently renamed Columbia's registry `title` from "…Report, Volume I" to "…Report, Vol. I". The PR body does not mention it; it changes the page heading, `citation_title`, and makes the 6 committed Columbia cards (`default.png` and five #279 `q-*` cards) stale (`pnpm cards` re-renders them). I restored "Volume I" (the `common_name` already says "Volume I"; the marketing Wikipedia draft uses it too); with it, `pnpm cards` is byte-identical. Conflicts with #279 in `layout.ts`, `report.ts`, `section.ts` (take `seo-on-279-trial`'s versions, verified identical to my resolution) and `AGENTS.md`, `lessons.md` (keep both). |
| site #283 phones and landing panel | **MERGE** after #279 (reviewer pushed a fixup) | Fixup `9eb4bf50` on `mobile-highlights`: `verify.sh` now runs `pnpm seed-highlights` (local D1) before the browser checks. Without it, `e2e-mobile.mjs` fails in any fresh worktree (and would fail the integrator's verify): "[iPhone 13]/[Pixel 7] an editor's highlight is labelled as the editor's — Shared passage", because the panel's label comes from the editor rows in local D1, which only the author's hand-seeded D1 had. It merges with no textual conflict. |
| site #282 lessons: marketing outreach | **MERGE** last (reviewer pushed a fixup) | Fixups `ebcba955` and `484e9bda` (reviewer lessons) on `marketing-outreach-lessons-1004`: the launch draft said "13 reports so far" and then listed 11 names; Chilcot and the Lehman examiner are added. `lessons.md` conflict: keep both. |
| marketing#1 outreach drafts | **MERGE** (Rufus's repo; nothing ships) | Nothing posts automatically: the repo has no `.github/workflows/` (only an issue template); every draft says Rufus sends by hand. `scripts/check-drafts.py` against the trial: "checked 38 files; ok" (every blockquote verbatim, every `?p=` id resolves on v0.22.0). Spot checks: Challenger House Report 99-1016 transmittal "October 29, 1986" and approval "October 7, 1986" are in `full.md`; the Litvinenko quote (`10-11-i-am-sure`) is verbatim; the launch plan's 14 Oct 14:00 UTC first post matches the poster's cron (`0 14 * * *`) and queue. The Wikipedia models carry COI disclosure and an "UNVERIFIED rights" warning. |

## Checks on the trial (main + all six PRs + the three fixups, `batchA-trial-1004`)

- **Static checks:** `pnpm typecheck` clean. `pnpm test` 889/889 (51 files). `pnpm corpus check`: 13 reports unchanged. `pnpm quality check`: all 13 within budget. `pnpm editorial`: 13 approved files, every quote verbatim. `pnpm cards`: 92 cards, **byte-identical after the #285 fixup** (before it, 6 Columbia PNGs re-rendered). `pnpm posts --check`: every unposted item resolves.
- **`VERIFY_SHARED=1 ./scripts/verify.sh` (local only): "All checks passed."** That is 352 passes, including the desktop browser checks (51 ok) and the new phone checks (63 ok: iPhone 13 in WebKit, Pixel 7 in Chromium, a desktop pass). WebKit was already installed (`webkit-2336`). The one warning is the existing `reports/pipeline.yaml` drift. The first run, before the #283 fixup, failed only the two "editor's highlight is labelled as the editor's" checks (see the verdict table).
- **No published content moves.** `diff -rq` of the trial's `assets/generated/` against the shared checkout's (main `d72327fb`) differs only in `.prerender-stamp`, because #285 adds registry fields. So this batch needs **no publish, no reindex and no search write**. It is a deploy, one migration, one `--record-layouts` and a seed dry run.
- **#285's heads, rendered by local `wrangler dev`:**
  - Columbia's landing page has the title "Columbia Accident Investigation Board Report, Volume I: full text, searchable — Reports that Matter" and canonical `https://reportsthatmatter.org/reports/columbia-accident`.
  - It carries 9 `citation_*` tags and no `citation_pdf_url`.
  - Its JSON-LD is one `Report`: ISO `datePublished` "2003-08", `author`/`sourceOrganization` the Board, `publisher` Reports that Matter, `isBasedOn` a `CreativeWork` with the NASA URL, and `archivedAt` the repo.
  - The `?p=just-before-2-24-p&h=|So what?|` link redirects to its section and keeps `og:url` with the query. Its canonical is the bare section URL, and `og:image` stays `default.png` (the bght.9 regression check still holds). Its JSON-LD is `[Report (isPartOf the report), BreadcrumbList]`.
  - The home page has `[WebSite, Organization]`.
- **Not blocking (#285):**
  - `citation_publisher` and JSON-LD `publisher` name Reports that Matter. The issuer is in `author` and `isBasedOn`, so this is defensible for our edition, but Zotero will record us as the publisher.
  - `Report` is not a Google rich-result type, so the Rich Results Test will show only the section pages' breadcrumbs as eligible. Use the Schema Markup Validator for the `Report` nodes.
- **#283 on screen:** I checked the PR's iPhone 13 screenshot of the editor's-highlight landing panel. The kicker reads "Editor’s highlight", with the quote, "p. 29 · Report of Special Counsel…", and Share / Copy link / Copy quote. The highlighted words sit clear of the panel. The OS selection menu itself is not emulated, so **try it on a real iPhone and a real Android phone after deploy** (the PR asks for this too).

## #287 in depth

**Verdict: correct; safe on production; the ranking change returns identical results.**

**The layout scheme.** It stays correct under concurrent or older-checkout reindexes. I read `scripts/lib/reindex.ts`, `reindex-run.ts`, `d1.ts`, `reindex-search.mjs` and the migration, and exercised each case against SQLite (3.43, FTS5) and the local wrangler D1:

- **A layout is only ever trusted after it is checked.** `readThroughLayout` returns null, and the run falls back to one full scan, if any row in a run belongs to another report, or if the row count differs from the recorded `n`. A stale layout costs a scan; it is not a wrong diff.
- **An older checkout's reindex clears the layout.**
  - Pre-#287 `incrementalStatements` upserts only `content_version, indexed_at`, so `NEW.layout IS OLD.layout AND NOT NULL` fires the trigger and sets `layout = NULL`. Verified in SQLite.
  - Old `--full` and `pnpm index-search` DELETE the version row and INSERT one without `layout`, so it is NULL anyway.
  - Locally, I inserted an extra jack-smith row with implicit rowid and ran the old-style upsert. The layout was cleared, and the next reindex said "read by scanning the whole table", deleted the stray row ("1 gone") and recorded a fresh layout. 430 rows.
- **Two layout-aware writers.**
  - Each file first sets `layout = NULL`. This fires no trigger, because NEW is NULL.
  - Each then writes the version row only `WHERE indexed_at IS <the value it read>`. A racer that read the same stamp leaves the row as the winner wrote it, with layout NULL, and exits 1. Verified in SQLite.
  - If both computed the same `firstRowid`, the explicit-rowid insert conflicts and D1 rolls the racer's whole file back.
- **Runs stay pure.**
  - New rows always go above the table's highest rowid, explicitly. `layoutAfter` shrinks every run to the rows kept, so each run's upper bound is a live row of that report and no later insert, implicit or explicit, can land inside it.
  - The probe's implicit insert goes at max+1 and is deleted by rowid, after checking that the row's `report` is `__rtm_probe__`.
- **`--record-layouts` writes conditionally** (`AND indexed_at IS <stamp read before the scan>`), so a reindex that ran during its scan keeps its own layout, or none.
- **The local end-to-end run worked.** On the trial's local D1 (39,688 rows, migration 0004 applied by verify):
  - `--record-layouts --local` recorded 13 layouts, one run each.
  - I changed 3 jack-smith bodies and marked its version stale. The reindex read through the layout ("1 rowid run"), reported "3 changed", wrote 4 statements and recorded `runs [[39689,40118],[79377,79379]], n 430`.
  - `--full` on Chilcot, then an incremental run, said "already matches; only its indexed-at stamp", keeping 1 run.
  - `publish-report jack-smith-vol1 --dry-run --local` prints "estimated D1 rows read: 5".
- **Known limit (by design, documented):** a hand insert into `passages` that touches no version row is invisible to a layout whose count still matches. Only manual SQL does that.
- **Pre-existing corner, filed as `reportsthatmatter-ib2b` (P3):** two same-content reindexes racing, where the second computes `firstRowid` after the first applied.
  - The second's inserts land, so every changed paragraph is duplicated. Its version upsert is skipped, and it exits 1 with "Run again (it diffs what is there)".
  - A re-run, though, sees `content_version` current and does nothing. `planReindex` keys stored rows in a Map, so a forced diff would not delete the extra copy either. Recovery is `--full`.
  - The old code duplicated in the same way, so this is not a regression. Only one integrator ships.

**Rows read, measured.** `pnpm exec tsx scripts/measure-d1-reads.mjs`: miniflare's binding counts rows the way D1 bills them. Run on the trial, it reproduces the PR's table exactly:

| per release, all 13 reports (dry run + probe + reindex + check) | rows read |
|---|---:|
| before #287 | 2,063,802 (locally; production read more, because its rows are scattered) |
| first release after 0004, with no `--record-layouts` | 1,031,966 |
| `--record-layouts`, once | 39,688 |
| every release after | 79,491 (Leveson 25,509; Jack Smith 867) |

- **Search rows per query:** "the" 69,766 → 19; "a" 73,932 → 19; "bloody sunday" 1,832 → 19; "the" scoped to Jack Smith 35,190 → 7,224 (a scoped query still filters an UNINDEXED column).
- **Production will have more runs per report than local** (one per past incremental insert, holes allowed). Each run is at least one `wrangler d1 execute` process, so a report with dozens of runs takes longer to read, but not more rows. `--record-layouts` prints the run counts, so look at them once.

**The ranking change does not alter result quality.** `rank MATCH 'bm25(0, 3, 0, 0, 1)' ORDER BY rank` uses the same weights as the old `ORDER BY bm25(passages, 0, 3.0, 0, 0, 1.0)`. My script (`scratchpad/rankcmp.py`) compared 215 query cases on the trial's index:

- 124 unscoped queries: common words, multi-word phrases, report-specific terms, 30 random corpus words.
- 78 scoped queries (6 per report).
- "the" scoped to each of the 13 reports.
- **All 215 return the identical top 20, in the identical order, with identical scores.** No case even differs in tie order.
- After the local reindex flow above (moved rowids, a `--full`), the same 215 queries return the same (report, paragraph) lists as before it.

**Migration 0004 on production.**

- **Additive.** `ALTER TABLE search_index_versions ADD COLUMN layout TEXT` (nullable; SQLite rewrites no rows) and one `AFTER UPDATE` trigger.
- **Nothing else reads the column.** The Worker never reads `search_index_versions`, and `check-search-staleness` selects named columns. Every old writer names its columns, so the old code keeps working with the column present. Reverting #287's code therefore needs **no schema rollback**: the trigger then just keeps clearing layouts nobody reads.
- **If a schema rollback is ever wanted:** `DROP TRIGGER IF EXISTS search_index_layout_guard; ALTER TABLE search_index_versions DROP COLUMN layout; DELETE FROM d1_migrations WHERE name = '0004_search_layout.sql';` (13 rows). D1 Time Travel is the backstop.
- **The trigger cannot recurse:** its own UPDATE sets NULL, which fails `NEW.layout IS NOT NULL`, and recursive triggers are off by default.
- **Without 0004** (or before `--record-layouts`), every read falls back to one full pass. That is still better than before #287.

**Other parts of #287, read and fine.**

- `pnpm ship`'s d1-estimate now budgets reads. It refuses up front unless it has 13 × 49k, plus a 500k reserve, plus 10%. A dry run that cannot read the index now prints "unknown" and exits 1, instead of "1".
- The probe finds its row by `last_row_id`.
- The ledger (`~/.local/state/rtm/d1-usage.jsonl`) is best-effort and never fails a publish.
- `pnpm d1-usage` reads Cloudflare analytics only, which costs no D1 rows.
- `check-search-staleness` and `seed-highlights` go through the ledgered runner (after my conflict resolution, `seed-highlights` does too).

## Merge order and conflict recipes

The merge order is #279, #278, #287, #285, #283, #282, each with a merge commit (as #279's review asked). Run the steps only **after v0.22.0's `pnpm ship --yes` and `verify-prod` have finished** (same reason as #279: deploy and seed run from the shared checkout).

1. **#279** `sharing-highlights` (head `6c2147c4`): clean.
2. **#278** `marketing-lessons-2026-10-03`: `docs/design/lessons.md`: keep both sections (HEAD's "Sharing and highlights (bght)" first, then "Marketing research (y2y8, 2026-10-03)", a blank line between).
3. **#287** `d1-reads-budget` (head `89599c65`): two conflicts.
   - `docs/design/lessons.md`: keep both; "D1 quotas (t4al)" goes after "Marketing research".
   - `scripts/seed-highlights.mjs`: keep #279's whole file (diff-only seeding) and route its two D1 calls through #287's ledgered runner. Exactly: `git checkout batchA-trial-1004 -- scripts/seed-highlights.mjs` (origin), or by hand: drop `import { execFileSync } from "node:child_process";`, add `import { wranglerRunner } from "./lib/d1.ts";`, replace the `d1 = (extra) => execFileSync(...)` helper and the `JSON.parse(d1([...]))` read with `const run = wranglerRunner(root); const target = remote ? "--remote" : "--local"; const read = run(target, { command: \`SELECT id, report, section, paragraph, exact, prefix, suffix, page FROM marks WHERE actor = '${EDITOR_ACTOR}'\` });`, and the final `d1([\`--file=${file}\`])` with `const [result] = run(target, { file });` plus #287's `D1: N rows read, M rows written` line. Drop #287's `ACTOR` constant (#279's `EDITOR_ACTOR` replaces it). `package.json` takes #287's `tsx` for `seed-highlights` automatically.
4. **#285** `seo-structured-data` (head `b39a470a`, includes my fixup):
   - `src/templates/layout.ts`, `report.ts`, `section.ts`: `git checkout origin/seo-on-279-trial -- src/templates/layout.ts src/templates/report.ts src/templates/section.ts` (#287 does not touch them, so the trial branch's versions are the right merge; I confirmed `git diff origin/seo-on-279-trial batchA-trial-1004 -- src/` shows only #287's `search.ts` and #283's two lines).
   - `AGENTS.md`: keep #285's new paragraph "Page heads are tested as a crawler reads them." and then **#287's** (HEAD's) longer "Cloudflare." paragraph (it carries the reads quota and `pnpm d1-usage`); drop #285's older copy of "Cloudflare.".
   - `docs/design/lessons.md`: keep both.
5. **#283** `mobile-highlights` (head `9eb4bf50`, includes my fixup): no conflict. If GitHub has not retargeted it to `main` after #279 merged, merge the branch locally all the same (it contains #279's commits, already in main).
6. **#282** `marketing-outreach-lessons-1004` (head `484e9bda`, includes my fixup and the reviewer lessons): `docs/design/lessons.md`: keep both.

Then `git diff batchA-trial-1004 HEAD --stat` in the integration should be empty apart from lessons ordering (or `src/generated/*` if regenerated).

## Integrator steps

Prerequisite: v0.22.0 is live and its `pnpm ship` run has finished (`build/ship/state.json` past `verify-prod`; `pnpm publish-report --all --status` lists nothing to publish). Then in the shared checkout:

1. **Merge** in the order and with the recipes above. Push `main`.
2. **Pull and re-check:** `git pull --ff-only && CI=true pnpm install` (#279 adds `upng-js`), `pnpm prerender && pnpm editorial && pnpm cards`, and confirm `git status` is clean (`src/generated/*` and `assets/cards/` unchanged). Optionally `VERIFY_SHARED=1 ./scripts/verify.sh` (all checks pass on the trial; needs WebKit: `pnpm exec playwright install webkit`).
3. **Check today's quota:** `pnpm d1-usage` (analytics only, no rows). Proceed if under ~4M rows read and ~95k written today.
4. **Deploy:** `./scripts/deploy-cloudflare.sh`. Its drift table must say all 13 reports are current (this batch moves no content). If any is DRIFT, stop: v0.22.0 did not finish.
5. **Migration 0004** (#287's PR body, verbatim):
   - `pnpm wrangler d1 migrations list reportsthatmatter-marks --remote` first.
   - If 0001 to 0003 show as applied: `pnpm wrangler d1 migrations apply reportsthatmatter-marks --remote`.
   - If the list wants to re-run them (they were applied by hand), apply only this one: `pnpm wrangler d1 execute reportsthatmatter-marks --remote --file=migrations/0004_search_layout.sql`.
   - Never re-run 0001 to 0003: `0002` would try to recreate `passages`.
6. **Record layouts, once:** `pnpm exec tsx scripts/reindex-search.mjs --record-layouts --dry-run` first (it scans the table, ~40k rows read, and prints each report's row and run counts), then `pnpm exec tsx scripts/reindex-search.mjs --record-layouts` (the same ~40k reads again plus 13 writes). To save the 40k, skip the dry run; the real run prints the same table. Expect 13 reports and about 39.7k rows in all; the run counts are production's real fragmentation.
7. **Seed:** `pnpm seed-highlights --dry-run --remote` → expect "163 stored, 163 wanted … 0 row write(s)" (if v0.22.0's ship seeded). Run `pnpm seed-highlights --remote` only if it plans writes, after reading the `-`/`+` lines.
8. **Decision 0014 and beads** as in #279's review (steps 7 to 9 there): mark 0014 accepted; close `bght.9`, `bght.7`, `bght.1`, `f2e`, `wb0`; also `bght.3`, `bght.4` (#283), `y2y8.4` (#285) and `t4al` fixes (2) to (4) (leave `t4al` open if fix (1), hosting/`2oz`, is tracked there). `bd dolt push`.

## Post-ship checks (production, read-only)

1. **Search works and is cheap.**
   - `curl -s 'https://reportsthatmatter.org/search?q=the' -o /tmp/s.html; grep -c 'class="result' /tmp/s.html` should show results.
   - Run a scoped one too: `…/search?q=iraq+intelligence&report=uk-chilcot-inquiry`.
   - About 10 minutes later, `pnpm d1-usage` should show the Worker's search queries at ~20 rows each, not tens of thousands.
2. **JSON-LD sanity on live pages.** For each of `/`, `/reports/columbia-accident`, `/reports/columbia-accident/full` and one section (e.g. `/reports/jack-smith-vol1/mr-trumps-supporters-attack-the-united-states-capitol`), run:
   `curl -s <url> | python3 -c "import sys,re,json; s=sys.stdin.read(); print(re.search(r'<title>(.*?)</title>',s).group(1)); print(re.findall(r'<link rel=\"canonical\"[^>]*>',s)); [print(json.dumps(json.loads(m),indent=1)[:800]) for m in re.findall(r'<script type=\"application/ld\+json\">(.*?)</script>',s,re.S)]"`
   Expect:
   - exactly one canonical, with no query string
   - JSON that parses
   - `datePublished` ISO (`2003-08`)
   - a section's array holding `Report` + `BreadcrumbList`
   - home's array holding `WebSite` + `Organization`
   - no `citation_pdf_url` anywhere
   Then paste two URLs (a report landing page and a section) into https://validator.schema.org (expect 0 errors) and https://search.google.com/test/rich-results (expect "Breadcrumbs" eligible on the section; `Report` is not a rich-result type, so "no items detected" on the landing page is normal).
3. **A `?p=` link canonicalises.** `curl -sL 'https://reportsthatmatter.org/reports/jack-smith-vol1?p=just-before-2-24-p' | grep -E 'rel="canonical"|og:url'` should show the canonical as the bare section URL and `og:url` with `?p=`.
4. **A real `?h=` link, on a real phone.** Open the first queue item, `https://reportsthatmatter.org/reports/jack-smith-vol1?p=just-before-2-24-p&h=location%20at%20the%20Capitol.%20|When%20an%20advisor%20at%20the%20White%20House%20learned%20this%2C%20he%20rushed%20to%20the%20dining%20room%20and%20informed%20Mr.%20Trump%2C%20who%20replied%20%22So%20what%3F%22|&src=bsky`, on an iPhone (Safari) and an Android phone (Chrome).
   - The words are marked and sit clear of the panel.
   - The panel's kicker says **"Editor’s highlight"**, then the quote and "p. 29 · …".
   - × closes it.
   - Selecting other words brings up the bottom dock (Share / Copy quote / Save) without colliding with the OS menu.
   - On desktop, the panel is a bottom-right card with a Bluesky button.
   - Do #279's head checks on the same link too: `og:image` is `q-75447469.png`, 2400×1260.
5. **A reader's link.** The same page with `&h=|So%20what%3F|` should label the panel **"Shared passage"**, and it must preview with `default.png`.
6. **Search Console:** after this deploy, request indexing for the home page and two report pages (Rufus, `y2t.1`, once Search Console is verified).

## D1 budget for shipping batch A

Measured locally where possible; production figures are estimates. This batch moves no content, so there is **no publish, no reindex and no search write**.

| step | rows read | rows written |
|---|---:|---:|
| `pnpm d1-usage` (analytics API) | 0 | 0 |
| deploy (`deploy-cloudflare.sh` drift table: `report_versions` per report) | ~15 to 30 | 0 |
| `wrangler d1 migrations list` + `apply` 0004 (`d1_migrations`, ALTER, CREATE TRIGGER, one migrations row) | < 50 | < 20 |
| `--record-layouts --dry-run` (optional) | ~39.7k (one pass, rowid + report only) | 0 |
| `--record-layouts` | ~39.7k + 13 | 13 (version rows; ~26 billed if the PK autoindex is counted) |
| `seed-highlights --dry-run --remote` (scans `marks`: its actor filter cannot use `marks_report_actor`) | < 1,000 | 0 |
| `seed-highlights --remote`, only if it plans writes | < 1,000 | 0 expected; worst case ~980 billed (326 planned × 3, bead `q2ph`) |
| post-ship checks (pages, `/marks`, 2 to 4 searches at ~20 rows each) | < 500 | 0 |
| **total** | **~42k (~82k with the dry run)** | **< 100 expected (< 1,100 worst case)** |

That is under 2% of the 5M daily reads and 1% of the 100k writes. It fits on the same UTC day as v0.22.0's ship, provided `pnpm d1-usage` shows v0.22.0's own reindex did not spend more than about 4.5M reads. Check it at step 3.

**From the next day on, #287 saves reads.**
- Search drops from up to ~70k rows per common-word query to ~20.
- A future 13-report release reads ~79k instead of ~2M+ locally. On 2026-10-03, production's comparable steps read 4.68M.

**If `--record-layouts` is skipped,** the next release's reindexes each scan once (~1M reads that day) and then use layouts.

## Found only by the trial merge

1. **#285 renamed Columbia's title without saying so.** This made 6 committed cards stale, and they surface only when someone runs `pnpm cards`. Fixed on the branch by restoring the old title. A test that `pnpm cards` is a no-op on a clean tree would have caught it (the same family as `u09x`).
2. **#283's phone check depended on the author's local D1.** `verify.sh` never seeded the editor's highlights, so the "Editor's highlight" label check passed only where someone had run `pnpm seed-highlights` by hand. Fixed: verify seeds (diff-only, so re-runs write nothing). The check's failure hint "WebKit missing?" was misleading here; WebKit was installed.
3. **#287 × #279 in `seed-highlights.mjs`.** Both rewrote it: #279 made it diff-only, #287 routed it through the ledger. Resolved by keeping #279's logic on #287's runner.
4. **#282's "13 reports" list named 11.** Fixed on the branch.
5. **A pre-existing reindex race corner** (bead `ib2b`, P3): see "#287 in depth".

## Getting better faster (reviewer retro)

- **What slowed me down:** the full `verify.sh` takes about 12 minutes (the ingestion fidelity and anchor steps re-ingest all 13 reports), and I ran it twice because of the seeding gap. The local D1 is shared by verify, `reindex-search --local` and `seed-highlights`, so the #287 experiments had to wait for verify to finish.
- **What would have caught these earlier:**
  - a test that `pnpm cards` (and `pnpm posts`) is a no-op on a clean checkout
  - verify running against a fresh scratch D1 (`--persist-to` a temp dir), so it never depends on what a developer's D1 happens to hold
- **One proposal:** a `VERIFY_FAST=1` mode for app-only PRs, which skips the re-ingest steps when `git diff origin/main` touches no `reports/`, ingest pin or `scripts/ingest` path, and gives verify its own throwaway D1 state dir.
