# Review: public highlights feed v1, implementation (PR #333, bght.10.1, .3, .4, .5), 2026-10-16

Reviewer: Claude Opus 5.5 agent (second reviewer; the first was stopped, and I started fresh in my own worktrees). I did not touch production: no `--remote` wrangler call of any kind, no `pnpm feed --remote`, no `migrations apply --remote`, no deploy, no merge, no seed, no publish. Every D1 test ran on `node:sqlite` (the PR's `tests/support/sqlite-d1.ts`) or on a throwaway miniflare D1 under my scratchpad (`wrangler dev --local --persist-to …`).

**Verdict: MERGE after #335, then SHIP, with the integrator steps below. Four fixups pushed. No blocking changes left.**

## Scope

| PR | Branch | Head at start | Head after fixups |
| --- | --- | --- | --- |
| #333 Public highlights feed v1 | `bght10-highlights-feed` | `a2688ea9` | `336883f6` |
| #335 Hosting: Datopian account (read for the order and the conflict only; reviewed separately) | `hosting/datopian-migration` | `8abb297f` | unchanged |

Spec: `docs/design/2026-10-16-public-highlights-feed.md`, decision 0023, and the design validation `reviews/review-design-highlights-feed-2026-10-16.md`. Where they differ, the validation wins.

## Trial

- Worktrees: `~/src/reportsthatmatter/rtm-review333c-1016` (branch `trial333c-1016`: `origin/main` `dabd310c` + #335 + #333), `~/src/reportsthatmatter/rtm-fix333c-1016` (my fixups, pushed to the PR branch). The earlier reviewer's `rtm-review333*-1016` worktrees were left alone.
- Merge order main → #335 → #333: **one conflict, `AGENTS.md`** (both rewrite the end of the Cloudflare section). Resolution: keep #335's **Cloudflare.** paragraph whole, then #333's **The public highlights feed** paragraph after it; drop #333's copy of the old atomatic paragraph. `wrangler.toml`, `docs/release-checklist.md` and `package.json` merge cleanly: the three `FEED_*` lines land in the Datopian top-level `[vars]` after `LEGACY_BASE`.
- Checks on the trial (logs in `factory/logs/2026-10-16/`):
  - `pnpm typecheck`: pass (`rtm-review333c-1016-typecheck.log`).
  - `pnpm test`: 1220 passed, **1 failed**: a time-dependent test in `tests/highlights-feed.test.ts` that fails in the first five minutes of every hour (it ran at 21:0x). Fixed on the branch (`336883f6`); passes since (`rtm-review333c-1016-test.log`).
  - `pnpm editorial --check`, `pnpm lessons check`, `pnpm decisions check`: pass.
  - `VERIFY_SHARED=1 ./scripts/verify.sh` on main + #335 + #333 at `336883f6`: **exit 0, all checks passed**, 1221 tests, e2e including "with counting off, Save sends nothing to /api/mark" and the iPhone 13 / Pixel 7 checks of both pages (`rtm-review333c-1016-verify2.log`). The first run (`rtm-review333c-1016-verify.log`) failed only on the flaky test fixed in `336883f6`.
  - Not run: `pnpm ship --plan` (its probe may read remote state; this PR moves no report text, and the integrator steps below are explicit).
- Local product run: `wrangler dev --local --port 8799 --var FEED_DELAY_MINUTES:0` on a fresh D1 with all five migrations; forged-payload script and its output in `rtm-fix333c-1016-forge.log`; screenshots at 375 and 1280 in `rtm-fix333c-1016-shots/`.

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| #333 at `336883f6` | MERGE after #335, then SHIP | Meets the validated design; every field on /highlights comes from the report and is escaped; forged and oversized payloads tested; D1 and CPU bounded. Four fixups pushed (below). |
| #335 | (merge first; its own review gates it) | Production already runs from it. Merging #333 first would leave main's `wrangler.toml` pointing at the atomatic account's D1 id, and the 12a migration command (now Datopian) would not resolve. |

## Fixups pushed (to `bght10-highlights-feed`)

1. **`0e35b5c5` Forged paragraph id stored verbatim (security, medium).** `verifyMark` checked `fragment.includes('<p id="' + paragraph + '"')`, a substring test. A real id with the next attribute appended (`based-above-facts-after-analyzing" data-page="33`) passed it, `extractParagraph` then matched the real paragraph, and the row stored the client's string as `paragraph`. That string reached the public `?p=` link and the passage's grouping key. It was encoded and escaped, so there was no injection, but it was browser text on a public page and it broke the link. Fix: a paragraph id must match `^[^\s"'<>&\\]{1,200}$` (all 92,210 ids in the corpus pass; ids contain accented letters, so it is not ASCII-only) before any lookup. Test added under (a).
2. **`2dcc081c` `pnpm feed --remote` still read production without `--yes` (operations, high).** The author's fix gated writes, but `status --remote`, `list --remote` and `rebuild --remote --dry-run` all ran remote reads without `--yes` (confirmed: with a fake `pnpm` on PATH, all three called wrangler on the PR head). The gate now sits in the script's only runner, so no path can skip it, and `rebuild --dry-run --remote` needs `--yes` too. Eight tests run the real script with a fake `pnpm` that records any call and assert it is never called (`tests/feed-admin.test.ts`; they fail on the PR head). The lesson file is updated. jp6i remains open for the other scripts.
3. **`0a53c74d` About copy (privacy, low).** "What we record" said the browser sends the words, report, paragraph, page and time, and that the daily code is "used only to count" readers. `marks` also stores the context either side (`prefix`/`suffix`) and save-or-share (`kind`), and the actor code also drives the 40-a-day rate limit and the feed's cap. The copy now says all of that.
4. **`8490064b`, `336883f6`**: release checklist 12a now gives the Datopian form of the migration commands (`--env "" --profile datopian`), and says the atomatic account does not need 0005 (the PR said "apply on both accounts", written before the cutover). The flaky coarse-time test now inserts its row on the hour.

## Findings checked, no change needed

**Security.** Forged payloads through `POST /api/mark` on miniflare (`rtm-fix333c-1016-forge.log`): a `<script>` prefix, an `onerror` suffix and page 9999 on real words produced a row with the report's own context and page (p. 93), and none of the three strings appears anywhere in the HTML, hrefs included. Invented words, an unknown report id, a Challenger mark and a 15-character selection produced no row. A section of `../meta`, `meta.json` or `a..` never leaves the report: `SLUG` refuses it, or the fragment is missing and verification falls back to meta. The words `M&A` render as `M&amp;A`. Oversized `exact` (1,001), `prefix` (65), `paragraph` or `section` (201) and a 3 MB body all answer 400 through the route. `section_title` comes from the fragment's own heading, `anchor` from `encodeAnchor(selectorFor(paragraph text))`, `page` from `data-page`, and `report` is checked against the registry. Every rendered field goes through `escapeHtml`, the paragraph additionally through `encodeURIComponent`. Row ids and exact times never reach the page.

**Privacy.** `public_marks` has no actor, IP, UA or session column; `created_at` is ms but the page shows only the hour, coarsely. `share.js` has the only `fetch('/api/mark')` in `assets/`, and it returns before the fetch when `rtm.count-marks` is `off` (read at send time, so a change on Yours applies at once). On miniflare, unticking the box on Yours stores `off`. The e2e checks that no POST is sent then and one is sent when it is on. The copy on Yours ("sent to us … counted anonymously … removing a passage here does not remove it from those counts") matches the code. With storage blocked, counting stays on and the box cannot remember a choice; acceptable (default A).

**Exclusions.** Tested in `tests/feed.test.ts`: all nine affected-community reports (loop over `AFFECTED_COMMUNITY_REPORTS`), the editor's actor, `FEED_ENABLED=false`, `FEED_EXCLUDE` (also at read time, for rows written before a report was added), under 4 words or 20 characters, and the blocklist (`held`). The generated picks hold 9 reports, none on the affected list, and the route filters picks through `isExcludedReport` again.

**D1 cost.** The author's deviation is right. With no index on `status` or `created_at`, the review's `WHERE status='visible' AND created_at <= ? ORDER BY id DESC LIMIT 200` stops only after 200 matches, so after `pnpm feed hide --all` every cache miss would scan the whole table. `ORDER BY id DESC LIMIT 250` reads exactly min(250, table) rows at any size and any mix (author's miniflare measurements: 200/250/250, and 250 with 5,000 hidden; consistent with how SQLite walks a rowid descending). The cost: a burst of more than about 250 rows within the delay, or many held/hidden recent rows, pushes older visible passages out. A flood of held rows (one actor past the cap, up to 35 a report a day) could empty Recently highlighted; the picks still show. That is the bght.10.2 trigger. Per request: cache miss 250 reads, cache hit 0; write path +1 to +40 reads (the duplicate check on the indexed `(report, actor, created_at)` range) +1 `report_versions` read +1 write; duplicates write nothing. The cache key is `origin + /highlights` plus the deploy id, so a query string cannot force a read. A missing table, a D1 error or `FEED_ENABLED=false` renders the picks (200) and still caches for 5 minutes. On Workers Paid (25 billion reads a month included), even the worst case (every data centre missing every 5 minutes: about 86k misses × 250 ≈ 21.6M reads a day) is under 3% of the daily allowance. Minor, not fixed: a HEAD request bypasses `cached()` (`method !== "GET"`) and reads D1 each time. `/reports/:id/marks` is uncached by design already, so this opens no new class; fold it into bght.10.7.

**CPU.** Production is on the Datopian account's **Workers Paid** plan (Standard usage model) since the 2026-10-16 cutover. The limit there is **30 s of CPU per invocation by default** (configurable up to 5 minutes; `wrangler.toml` sets no `[limits]`). `waitUntil` work counts against the same invocation. The measured worst case, 9.18 ms (Leveson `/full`, meta parse included), is 0.03% of that, and the common path is about 1 ms. The 10 ms Free limit no longer applies, except on a rollback to `[env.atomatic]`, where occasional overruns are tolerated. lkt6 (send the slug from /full) becomes an efficiency nicety: suggest P3. CPU is billed past 30M CPU-ms a month: 500 marks a day × about 4 ms is about 60k CPU-ms a month.

**UI** (miniflare, 375 and 1280, `rtm-fix333c-1016-shots/`, plus the author's shots). One `main` and one `h1`, no sideways scroll at 375, Readers/Yours tabs at 44 px with `aria-current="page"`, sections labelled by their headings, no live region or polling. Recently highlighted comes first, then Editor's picks in their own labelled block ("Not counted as readers' highlights", no time, no count). Empty state: the stream says what will appear and the picks follow, so the page is never empty. The opt-out shows only with JS. The design is restrained and the site's own: serif quote, mono meta line. It delivers Rufus's ask: anonymous, running ("updated every few minutes", coarse times, "highlighted N times"), social, never empty, with the personal list on its own tab. One product note (not blocking): passages print exactly what the reader selected, so a sloppy selection reads "…the S" or starts mid-word ("ald J. Trump"). Snapping the stored slice out to word boundaries in `verifyMark` would make the stream read much better. Filed as reportsthatmatter-ve75.

## Deploy order and #335

#335 first. Production already runs from it, and its `wrangler.toml` has the Datopian `account_id`, D1 id `ceb6c2cc-…` and custom-domain routes. Then #333. Conflict: `AGENTS.md` only (recipe above). After both, `scripts/lib/d1.ts` adds the Datopian account args to every `--remote` call (`RTM_CF_ACCOUNT`, default `datopian`), so `pnpm feed --remote --yes` reaches the Datopian D1. `[env.atomatic.vars]` does not get the `FEED_*` lines. Wrangler does not inherit `[vars]` into an env, so a rollback there runs the code defaults (on, 30 minutes), and with no `public_marks` table on that D1 it shows picks only. That is fine and needs no change.

## Integrator steps (RTM_CF_ACCOUNT=datopian)

1. Merge #335 (its own review).
2. Update #333 onto main and resolve `AGENTS.md`: `git -C <site worktree> merge origin/main`; keep main's **Cloudflare.** paragraph, then #333's **The public highlights feed** paragraph; commit, push, wait for CI. Merge #333 (`gh pr merge 333 --merge`).
3. In a fresh worktree on the new main: `pnpm bootstrap`, then `pnpm typecheck && pnpm test && pnpm editorial --check`.
4. Check what production lacks: `pnpm wrangler d1 migrations list reportsthatmatter-marks --remote --env "" --profile datopian`. Expect exactly `0005_public_marks.sql`. If 0001-0004 also show, the `d1_migrations` table did not come across in the copy: stop and check by hand (`SELECT name FROM d1_migrations` with the same flags) before applying anything.
5. Apply it: `pnpm wrangler d1 migrations apply reportsthatmatter-marks --remote --env "" --profile datopian` (one `CREATE TABLE`, no rows).
6. Deploy: `RTM_CF_ACCOUNT=datopian ./scripts/deploy-cloudflare.sh`. A deploy before step 5 is safe (feed off, picks only), but do it after.
7. Check production: `curl -s https://reportsthatmatter.org/highlights | grep -c 'Editor’s picks'` (≥1) and `curl -s https://reportsthatmatter.org/highlights/yours | grep -c noindex` (1). Then `RTM_CF_ACCOUNT=datopian pnpm feed status --remote --yes` (expect "empty"). Then `VERIFY_SHARED=1 VERIFY_BASE=https://reportsthatmatter.org ./scripts/verify.sh`.
8. Know the kill switch: `RTM_CF_ACCOUNT=datopian pnpm feed hide --all --remote --yes` (no deploy), then `FEED_ENABLED = "false"` in `[vars]` and a deploy to stop new rows.
9. Visual changelog entry with real data after the first day (left by the author).
10. Close bght.10.1, .3, .4, .5 on ship. Leave lkt6 (lower it to P3), jp6i, bght.10.2, .6, .7 open.

## Beads

- Noted the verdict on bght.10.1, .3, .4, .5 and jp6i.
- Filed: reportsthatmatter-ve75 (P3, specced): snap stored words to word boundaries.
- #294 (marks-cache, stale since 10-04) also edits `POST /api/mark` and `src/lib/marks.ts`: it will conflict with #333's `recordMarkCounted` if it is revived.

## Getting better faster

- **What slowed me:** the PR described the `--yes` problem as fixed, and its follow-up bead said the test was still to come. A test that ran the real script with a recording fake `pnpm` took ten minutes and found three more remote reads. The flaky test cost a trial rerun because it only fails in the first five minutes of an hour.
- **What would have caught it:** for any CLI with `--remote`, the PR's own test should exercise the real entry point against a runner that cannot reach the network. For time-formatting tests, insert rows on fixed boundaries, or freeze the clock.
- **Proposal:** a shared `tests/support/fake-pnpm.ts` helper (PATH-shadowing recorder, about 15 lines, as in `tests/feed-admin.test.ts`), and jp6i applies it to `seed-highlights`, `reindex-search`, `publish-report` and `marks check`.
