# Design review: public highlights feed (PR #331, bght.10), 2026-10-16

- **Reviewer:** independent design validator (Claude Opus 5.5), 2026-10-16.
- **Scope:** `docs/design/2026-10-16-public-highlights-feed.md`, decision record (renumbered to `docs/decisions/0023-public-highlights-feed.md`), beads bght.10 and bght.10.1 to .8. I checked the claims against the code on `origin/main` (c6b1e196), the live site and `pnpm d1-usage`, not the doc alone.
- **Pushed to the PR branch** (a321fafc, rebased on main): decision renumbered 0021 → 0023 (0021 is the factory record and 0022 email capture, both on main; `pnpm decisions next` gave 0023; no duplicate numbers); a "Validated" status line; four factual corrections (below). PR retitled. Bead titles and descriptions now say 0023.
- **Decision:** 0023 marked decided (Rufus accepted the defaults, relayed by the supervisor); bght.10.8 closed.
- **Filed:** reportsthatmatter-bght.10.9 (P1, bug, `level:specced`, `handoff`): fix the false "never sent to us" byline now, independent of the feature.

## Verdict: approve with changes

The product half is right and I would build it as written: `/highlights` public and never empty, the personal list at `/highlights/yours`, Editor's picks in their own labelled block, coarse times, verification of the words against the report, affected-community reports held out until 63cd, honest copy plus an opt-out. It meets Rufus's intent (social, running, never empty, anonymous, personal list kept separate).

The serving half is overbuilt for a site with **0 reader marks** today. It has a Cron Trigger every 10 minutes, three new tables, a watermark, a JSON snapshot, an hourly "this week" rebuild and a landing-page block. It also carries a real CPU risk that the doc understates, and its case against a simpler design rests on a false premise. A smaller v1 ships the same visible page sooner, is easier to roll back, and keeps the cron and snapshot available as the scale-up path.

## What I verified

| Claim | Result |
| --- | --- |
| "never sent to us" is false | **True, and live.** `assets/share.js` `report()` POSTs `{report, section, paragraph, exact, prefix, suffix, page, kind}` to `/api/mark` on Save, Copy link, Copy quote and a completed Share. Live `/highlights` shows "Kept in this browser · never sent to us", and live `share.js` contains the call. The same false claim appears in comments at `src/templates/highlights.ts:7` and `src/index.ts:319`, and in the published `docs/CHANGELOG.md:638` ("none of it is sent to us"). This bug exists today, independent of the feed, and is filed as bght.10.9. |
| `marks.exact` is unverified and unbounded | True (`parseMarkPayload`). So are `prefix`, `suffix`, **`page`** and `section`. |
| 0 reader marks, 224 editor rows | Consistent with `pnpm d1-usage` today: 7,530 rows read, **0 written**. |
| `id > watermark` is a cheap rowid range | True. The same fact means `ORDER BY id DESC LIMIT n` needs **no** new index, which undercuts the doc's §6 objection to a read-time query (corrected in the doc). |
| Cron fits the 10 ms CPU limit; `topMarkedPassages` is evidence | **Not established.** Cloudflare's limits page gives Free 10 ms per Cron Trigger as well as per request, with "some built-in flexibility" for occasional overruns, and 5 Cron Triggers per account. `topMarkedPassages` **never runs in production** because all 18 editorials are approved. Measured on Node/V8: one fragment decode + `extractParagraph` + `locate` costs 0.73 ms on the largest fragment (Lehman `repo-105`, 1.1 MB) and 0.38 ms on Leveson `news-corporation`; `JSON.parse` of Leveson `meta.json` (1.5 MB) costs 3.7 ms. 25 verifications per run comes to about 18 ms before any meta parse. The snapshot build also calls `loadReportMeta` for section titles across every report it shows. Workers is slower than an M-series Mac. The cap of 25 is therefore too high, and the §4.7 budget leaves out meta parses entirely. |
| Default-export change breaks tests | Low risk. Every test imports the named `{ app }`. |
| Editor's-picks fallback "from a generated module" | It needs generator work. `src/generated/editorial.ts` holds no highlights today (bead .3 already says to extend `scripts/editorial.mjs`). |
| Affected-community exclusion | It covers **9 of the 18** live reports (challenger, columbia, 911, deepwater, hillsborough, saville, post office, grenfell, CIA torture). The doc didn't say so (now added). Its §3.2 mock used a Challenger passage, which its own policy excludes; I replaced it with a real Chilcot passage, p. 6. |

## Required changes

1. **Replace the v1 serving design (§4.2 to §4.7; beads .1, .2) with write-time verification into one small table and a cached read.** The design's own objections to this don't hold: there is no index cost (rowid), the delay can be a read-time filter, and a stateless read has nothing to race on.
   - Migration `0005_public_marks.sql` (number it when the PR opens: email capture may also want a migration): `public_marks(id INTEGER PRIMARY KEY, report TEXT NOT NULL, paragraph TEXT NOT NULL, slug TEXT NOT NULL, section_title TEXT NOT NULL, words TEXT NOT NULL, anchor TEXT NOT NULL, page INTEGER, created_at INTEGER NOT NULL, status TEXT NOT NULL DEFAULT 'visible', reason TEXT NOT NULL DEFAULT '')`. No secondary index and no actor column. Rows are derived and can be rebuilt from `marks`.
   - In `POST /api/mark`, after `recordMark` returns `ok`, `executionCtx.waitUntil(publishMark(...))` does the following, each step swallowing its own failures so the `marks` insert and the 204 are never affected:
     - **Skip** the row when the report is held (affected list, `FEED_EXCLUDE`, `FEED_ENABLED=false`).
     - **Skip** it when this actor already marked these words today (the design's prior-mark check on `marks_report_actor`).
     - **Hold** it when the actor already has 5 or more marks in this report in 24 h. Reuse the `COUNT(*)` that `recordMark` already runs (return it), so this costs no extra read.
     - **Verify** it: load `fragments/<section>.html` for the client's slug and require `<p id="<paragraph>"`. Fall back to `loadReportMeta` + `resolveParagraph` only when section is `""` (the `/full` page) or the id has moved. Then `extractParagraph` and `locate`. The common path never parses `meta.json`, so a mark costs no more CPU than a `?h=` quote link does today.
     - **Insert** one row.
   - `/highlights`: through `cached(c, render, 300)`, run `SELECT … FROM public_marks WHERE status = 'visible' AND created_at <= ?(now − delay) ORDER BY id DESC LIMIT 200`. Group in memory into passages (count = rows = reader-days, last seen = max), apply the diversity rules and the 30-day window, and render. A missing table or any error renders the picks alone.
   - Defer **Most highlighted this week** (it needs 3+ passages with 2+ reader-days, which is weeks away at 0 marks) and the **landing-page block (.6)**. Those blocks are where the design's cron and snapshot earn their place. When they are built, the cron reads the verified `public_marks`, not `marks`, so it does no verification and carries no CPU risk.
   - Budget for this v1 at A1/A2: **+1 row write per mark** (500/day, 0.5%, against the design's 2,600; the free tier holds about 25,000 marks a day). Reads: about 2,000 a day on the write path (prior-mark check plus the `report_versions` pointer). `/highlights` reads at most about 250 rows per cache miss, with misses no more than min(views, active data centres × 288). At 1,000 views a day that is at worst about 250k (5%), and in practice much less. **Scale-up trigger:** when `pnpm d1-usage` shows that query above 250k rows a day, or `/highlights` above about 5,000 views a day, add the design's snapshot row (§4.4) built by a cron from `public_marks`.
2. **Print only what the report contains: the words, the anchor and the page.** Store `words` as the slice of the published paragraph that `locate` returns, never the client's `exact` (which can also be a `head⋯tail` GAP selector). Re-derive `prefix`/`suffix` from the paragraph, and build `anchor` with `encodeAnchor` from those. Take `page` from the paragraph's `data-page`, not the payload. The design stored client prefix and suffix (which reach the "Read in place" URL) and printed the client's page ("P. 45"), both unverified. `locate` falls back to `exact` alone, so prefix and suffix are never checked.
3. **Escape on render.** `extractParagraph` decodes `&amp; &lt; &gt; &quot;`, so verified words can contain markup characters. Render with `escapeHtml` (`src/templates/layout.ts`) and add a test with a `<` in the words.
4. **A kill switch that needs no deploy.** `[vars]` changes go through the full prerender-and-deploy path. `pnpm feed hide --all` / `--report <id>` (one `UPDATE … SET status='hidden'`) is the immediate switch. Keep `FEED_ENABLED` as the second line.
5. **Tests that must exist before .1/.2 merge**, in addition to the beads' lists:
   - (a) A mark whose words are not in the paragraph never produces a `public_marks` row and never appears on `/highlights`.
   - (b) A mark whose words are real but whose `prefix`, `suffix` or `page` are forged appears with the report's own prefix, suffix and page; the forged strings appear nowhere in the HTML, including hrefs.
   - (c) A GAP-encoded `exact` stores the full located slice.
   - (d) A `/full` mark (section `""`) verifies through meta.
   - (e) A missing `public_marks` table: `/api/mark` still returns 204 and inserts into `marks`; `/highlights` returns 200 with picks.
   - (f) Editor actors are never published.
   - (g) An affected-community report is never published.
6. **Ship the copy fix now** (bght.10.9), before any feed work. It's a false privacy statement on a live page and costs one template edit.

## Recommended simplifications and smaller points

- **Reader-days are not readers.** "3 READERS" counts one reader on 3 days as 3. Either say "highlighted 3 times", or accept the overstatement and say in 0023 Q1 that the count is reader-days.
- **Saves are private acts; shares are public ones.** Rufus asked for "what readers are highlighting", so including saves matches his intent, and the honest copy plus opt-out covers it. Note the distinction in 0023 Q4, so that if anyone objects, "shares only" is a one-line policy change.
- **Re-identification:** nothing shown identifies anyone. At K=1 and launch traffic, the remaining signal is a cluster of passages from one report within one hour, which reveals one reader's trail but not who they are. The per-report cap of 4 in 20 already limits it. Acceptable. The daily-salted actor never leaves `marks`, and the revised table has no actor.
- **Flooding by rotating user agents** gets past the per-actor caps. Before adding a /24 hash, check whether the Workers Rate Limiting binding (keyed on IP in memory, nothing stored) is available on the Free plan for `/api/mark`.
- **Rollback with a cron is a trap.** Rolling back to a pre-feed version leaves the trigger firing into a Worker with no `scheduled` handler. The v1 has no cron, so there is no such hazard. If the cron comes later, the release checklist needs a line for it.
- **Migration ordering** is unchanged in substance: apply the migration to production before the deploy that writes to it. Code treats a missing table as "feed off" (test 5e). Rollback leaves an unused table, which is harmless.
- `marks.id` has no `AUTOINCREMENT`, so a rowid can be reused after deleting the top row. That doesn't matter for the v1 (no watermark), but it matters for a later cron watermark.
- §4.6 (moving `/reports/:id/marks` onto the rollup) stays out of scope, as the doc says.

## Implementation order (decision 0023 decided; beads edited)

Rufus accepted the defaults A for Q1 to Q5 on 2026-10-16 (relayed by the supervisor), so the question is no longer whether to proceed. Decision 0023 is marked decided on the PR (df101a7c) and bght.10.8 is closed. I edited the child beads directly so an implementation agent can start from them:

1. **bght.10.9** (P1, specced, new): the copy fix. Ship first.
2. **bght.10.1** (P1, judgement), **rewritten**: the `public_marks` migration, `publishMark` in `/api/mark` (verification at write time, report-derived words, anchor and page), `feed-policy.ts`, payload caps, and tests (a) to (h). This takes in what was .1 plus the verification part of .2.
3. **bght.10.3** (P1, judgement), **validator block prepended**: reads `public_marks` through a 5-minute cache, has no "this week" block, escapes output, prints "highlighted N times", and now depends on .1, not .2.
4. **bght.10.4** (P1, specced): unchanged apart from a note (rebase on .9; About text).
5. **bght.10.5** (P2, specced), **note**: targets `public_marks`, adds `hide --all` as the no-deploy kill switch, and `rebuild` re-verifies locally.
6. **bght.10.7** (P3): check after one week against this review's v1 budget, and decide whether .2's trigger is met.
7. **Deferred:** **bght.10.2** (P3, rewritten as the cron and snapshot scale-up built from `public_marks`, now depending on .7) and **bght.10.6** (landing block, after .2).

.1 and .9 can run in parallel. .3 follows .1. .4 follows .3.

## Getting better faster

- **What slowed me:** the design's CPU claim pointed at code (`topMarkedPassages`) that is dead in production, so I had to find and time the live path myself. The branch's worktree was held by the design agent, so I made a second worktree on a review branch.
- **What would have caught it:** a "does this code path run in production?" check on any cited evidence. A measured CPU number, not an argument, for any work proposed on Workers Free.
- **Proposal:** a small `scripts/measure-cpu.mjs` that times `loadReportMeta` + `loadFragment` + `extractParagraph` + `locate` on the five largest fragments and metas (my scratch version took 20 lines). Design docs that add Worker work would cite its output. The design's own lesson (check privacy copy against network calls) stands; bght.10.9's test is the first instance of it.
