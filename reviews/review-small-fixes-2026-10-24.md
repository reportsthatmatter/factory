# Review: small fixes #391, #392, #294 (2026-10-24)

Reviewer: Opus agent (reviewer, small-fixes batch). I did not touch production: no merge, deploy, release, publish, seed, D1 query, Bluesky call or `wrangler deploy` (only `--dry-run`). No fixups pushed: nothing needed one.

**Verdict: ship all three (#391, #294, #392); merge in that order; deploy only, no migration.**

## Scope

| PR | bead | branch | head at start | head after review |
|---|---|---|---|---|
| #391 Rate-limit newsletter signups | bvl7 | `bvl7-rate-limit` | `c45b7029` | `c45b7029` (unchanged) |
| #294 Edge-cache of mark counts | 6kky | `marksreads-1004` | `b439a09c` | `b439a09c` (unchanged) |
| #392 Long-quote cards as needs-hook items | f9we | `f9we-hooked-long-quotes` | `c24eba06` | `c24eba06` (unchanged) |

Base: origin/main `305d511b` (#390).

## Trial

Worktree `~/src/reportsthatmatter/rtm-review-smallfix-1024`, branch `trial-smallfix-1024` from `305d511b`, `pnpm bootstrap`, then `git merge --no-ff` of #391, #294, #392 in that order. All three merged clean (`src/index.ts` auto-merged: #391's subscribe changes and #294's `/api/mark` and `/marks` changes are in different hunks).

| check (on the trial merge of all three) | result |
|---|---|
| `pnpm typecheck` | pass |
| `pnpm test` | pass, 1449 passed, 1 skipped (90 files) |
| `pnpm posts --check` | pass (exit 0); lists the 8 needs-hook items; 1 candidate skipped (PSI, see #392) |
| `pnpm posts --verify` | pass, "marketing/queue.yaml is up to date" |
| `pnpm quality check`, `pnpm corpus check`, `pnpm editorial`, `pnpm aliases check` | pass |
| `pnpm quality ratchet --dry-run` | nothing to lower |
| `VERIFY_SHARED=1 ./scripts/verify.sh` | pass, "All checks passed." One warning, peer noise (R6): `reports/pipeline.yaml drifts from the artefacts` (no PR here touches reports). Log: `factory/logs/2026-10-24/rtm-review-smallfix-1024-verify.log` |
| `pnpm exec wrangler deploy --dry-run` (wrangler 4.118.0) | pass; binding list shows `env.SIGNUP_LIMITER (5 requests/60s) Rate Limit` |
| `pnpm ship --plan --state build/review-ship-state.json` | plan prints; nothing run |
| `git status --short` after all checks | clean |

## Verdicts

| PR | verdict | reason |
|---|---|---|
| #391 | SHIP | `[[ratelimits]]` valid (dry-run lists the binding); 429 + `Retry-After: 60` for JSON, 303 to `?error=limited` for a plain form; fails open on a missing or throwing binding; the IP is only the limiter key (tested: not in rows or response); daily-cap SQL reads at most 500 rows (checked with `EXPLAIN QUERY PLAN`). |
| #294 | SHIP | Rebase resolution in `/api/mark` is right: eviction (`waitUntil`) runs before `publishMark`, both after the response; feed test updated to 2 pending tasks. Lessons are three well-formed `docs/design/lessons/` files, no edit to `lessons.md`. Design as decision 0031. |
| #392 | SHIP | Queue diff is append-only (35 existing items byte-identical as parsed; 8 added, all `needs_hook: true`, no hook). Both posters refuse an over-length item (`validateItem` in `src/lib/poster.ts`, shared by `scripts/post-next.mjs` and `workers/poster/`). Round-trip refusal drops no queued item. Cards checked by eye. Needs hooks before 2026-11-29 (bead irro). |

## Findings, by severity

None blocking. No fixups pushed.

### #391 (bvl7)

- Checked: `subscribers.id` is `INTEGER PRIMARY KEY` (migration 0006), so `ORDER BY id DESC LIMIT 500` is a reverse rowid scan with no temp sort (`EXPLAIN QUERY PLAN`: `SCAN subscribers`, no `USE TEMP B-TREE`); on a 3,000-row scratch table the count came out right (201 in the window). The cap counts rows, so a repeat address (ON CONFLICT DO NOTHING) does not use up the cap.
- Checked: `assets/signup.js` shows the JSON `message` whatever the status, so a 429 reads "Too many signups just now…" in place.
- Minor, not fixed: with no `cf-connecting-ip` (never on Cloudflare in production) every caller shares the key `"unknown"`. Fine.
- Not checkable from here: `namespace_id = "4619001"` unique in the shared Datopian account. No other local `wrangler.toml` under `~/src` declares a ratelimit. A clash would share counters with another Worker, not break the deploy.

### #294 (6kky)

- Checked: both callers (`/reports/:id/marks` and the overview's "Most marked") pass the same `MARK_THRESHOLD`, so one cache key per report is safe.
- Minor, not fixed: `seed-highlights` writes marks straight to D1, so after ship's seed step editor highlights show at most 2 to 30 minutes later at a warm location. The PR body says this. Production verify reads `/marks` but does not assert a new mark, so it is not affected.
- Minor, not fixed: if the `/marks` route's prerender meta fails to load, it caches counts without alias mapping, and the overview then serves those for up to one TTL. Rare; self-heals.
- Minor race, not fixed: a miss that started before a mark landed can `put` after the eviction. The TTL bounds it.

### #392 (f9we)

- Checked: `needsHookViolations` gates `--check` once `scheduled <= today`. Dry-run `pnpm post-next` on a queue of only the needs-hook items with `POSTER_TODAY=2026-11-29`: `POSTER FAILED: … not postable: text is 357 graphemes, over Bluesky's 300`. The Worker uses the same `runPoster`/`validateItem`.
- Checked: the link round-trip refusal in `resolveCandidate` skips one candidate, `us-psi-financial-crisis/case-study-examines-detail-four` (a `%` in the quote). It was not in main's queue. `--check` reports no stale unposted item, so no existing item is dropped.
- Cards checked by eye: `us-lehman-examiner/q-f329dfde.png` (300-character quote, the longest), `uk-leveson-inquiry/q-c162d632.png` and `uk-post-office-horizon-inquiry/q-a8810e8c.png`. All three show the whole quote legibly at the floor size, with no cut. All 8 card files already exist on main; the PR adds no images. Most of the 8 go over 300 because of the source line plus the context line, not the quote (quotes are 110 to 300 characters).
- Operational, filed: a due needs-hook item with no hook stops the poster. `--check` fails, so the workflow posts nothing (`POSTER_RECORD_ONLY`), and `selectNext` keeps picking that earliest due item. Hooks are needed by 2026-11-29: bead **reportsthatmatter-irro** (poster-live).
- Minor, not fixed: `CARD_QUOTE_LIMIT` is compared with `graphemeLength`, but `cards.mjs fitToCard` cuts on `text.length` (UTF-16). These differ only for astral characters, and no current quote has any.

## Found only by the trial merge

Nothing. The three PRs touch `src/index.ts` (#391, #294) in separate hunks; any merge order is clean.

## Integrator steps

1. `bd dolt pull`. Check that origin/main is still `305d511b` and the heads are #391 `c45b7029`, #294 `b439a09c`, #392 `c24eba06` (`gh pr view <n> --json headRefOid`). If anything else has landed, re-run `pnpm typecheck && pnpm test` on the new main after each merge.
2. `gh pr merge 391 --squash`.
3. `gh pr merge 294 --squash`. If GitHub reports a conflict in `src/index.ts`, keep both sides. #391's hunks are in `/api/subscribe` and the imports; #294's are in `/api/mark`, `/marks`, `topMarkedPassages` and the marks imports.
4. `gh pr merge 392 --squash`. If poster-live has changed `marketing/queue.yaml` on main in the meantime and it conflicts, take main's file, then run `pnpm posts` on the PR branch: it re-appends the needs-hook items. Then `pnpm posts --check`, commit, and merge.
5. Pull the shared site checkout `--ff-only`, then in a worktree at the new main: `pnpm bootstrap && pnpm typecheck && pnpm test && pnpm posts --check && pnpm posts --verify`.
6. Ship with `pnpm ship` (the `integrate-and-ship` skill). There is no ingest release, pin bump, migration, secret or D1 change. The deploy creates the rate-limit binding (wrangler 4.118 understands `[[ratelimits]]`). If the deploy refuses `namespace_id 4619001` as taken, change it to another unused integer in `wrangler.toml` in a one-line PR and deploy again.
7. After the deploy, on `https://reportsthatmatter.org` (not workers.dev, where the Cache API does nothing): `/newsletter?error=limited` shows "Too many signups just now…" (no signup row written). The day after, `pnpm d1-usage` should show the marks query's rows read well below calls x marks.
8. Close on ship: `bd close reportsthatmatter-bvl7 reportsthatmatter-6kky reportsthatmatter-f9we`. Leave `reportsthatmatter-irro` open for poster-live (hooks by 2026-11-29).

## Beads

- Filed: **reportsthatmatter-irro** (P2), write hooks for the 8 needs-hook items before 2026-11-29 (poster-live).
- Noted on bvl7, 6kky, f9we: the verdict and this file.

## Lessons

- An append-only claim on `marketing/queue.yaml` is cheapest to check by parsing both files and comparing `main[:n] == trial[:n]`, not by reading the diff.

## Getting better faster

- Three small PRs, one trial, all green. The time went on cross-checks: SQL plan, poster refusal, queue append.
- A `pnpm post-next` dry run with `POSTER_QUEUE` and `POSTER_TODAY` is a two-second proof that the poster refuses an item. Worth adding to the pr-review skill for any queue PR.
- Quoting `pnpm -s "$c"` in a loop made pnpm read "quality check" as one script name. Split the arguments.
