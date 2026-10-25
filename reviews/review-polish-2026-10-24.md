# Review: polish batch before the 27 Oct launch (2026-10-25)

Reviewer: Claude agent (Sonnet 5.5). I did not touch production: no merge, tag, release, deploy, publish, seed, `wrangler d1 execute --remote`, and no live Bluesky/Lighthouse run. I pushed no fixups (the brief said not to edit PRs). Rehearsal commands I ran are listed under Trial.

**Verdict: all eight PRs are MERGE. Nothing is irreversible except the ingest release for f1o1, and that one changes labels only. One real conflict (#374 and #375, one import line) that the integrator resolves as below.**

## Scope

| PR | Bead | Branch | Head at review |
|---|---|---|---|
| site #374 | ntjx | ntjx-og-desc-1024 | a5dc5290 |
| site #375 | te56 | te56-full-head-1024 | ae1d38a6 |
| site #376 | n5p8 | n5p8-sitemap-llms-1024 | 19fe7033 |
| site #377 | xq4i | xq4i-intro-footer-src-1024 | ff49b74b |
| site #378 | 3rj | 3rj-sidenote-collapse | a8c9b86b |
| site #380 | 076k | 076k-mobile-overflow (based on 3rj-sidenote-collapse) | 1c8f2b79 |
| site #382 | 3gx4 | 3gx4-self-host-fonts | dc871658 |
| ingest #84 | f1o1 | f1o1-section-page | ed5fdfff |

Site base: origin/main 5f6250d7 (#381). Ingest base: origin/main a70ddbb (v0.29.0). Heads were not changed by me.

## Trial

- Site worktree `~/src/reportsthatmatter/rtm-reviewpolish-1025` (branch `trialpolish-1025`), merged `--no-ff` in the order 374, 375, 376, 377, 378, 380, 382. Only #375 into #374 conflicted (below); the rest merged clean, including the styles.css and e2e*.mjs overlaps of #378/#380/#382 and the layout.ts overlap of #377/#382 (different hunks).
- `pnpm bootstrap`, `pnpm typecheck` (exit 0), `pnpm test` (89 files, 1419 passed, 1 skipped), `pnpm editorial --check`, `pnpm aliases check`, `pnpm quality check`, `pnpm corpus check`, `pnpm quality ratchet --dry-run`: all exit 0, nothing would fall, so no ratchet commit. `VERIFY_SHARED=1 ./scripts/verify.sh`: exit 0, including the new overflow checks, the "no request to fonts.googleapis.com" check and the /llms.txt and `<lastmod>` assertions. `pnpm ship --plan` prints cleanly.
- Logs: `~/src/reportsthatmatter/factory/logs/2026-10-25/rtm-reviewpolish-1025-{bootstrap,typecheck,test,checks,verify}.log`.
- Ingest worktree `~/src/reportsthatmatter/ingest-reviewpolish-1025`: `pnpm build` leaves `dist/` unchanged (committed dist is current), `pnpm test` 944 passed. `pnpm typecheck` fails in `tests/covid-inquiry-passes.test.ts` (TS2554, two lines); the file is not touched by the PR and the PR says main fails too (it came in with #83). Not caused by f1o1.
- f1o1 corpus effect: linked the ingest trial into the site trial, `pnpm prerender`, compared `meta.json` section pages before and after for six reports. Slugs identical in all. Effect on `src/generated/editorial.ts` after `pnpm editorial`: 112 lines changed, all reading-guide `page` values. Then `pnpm ingest link --restore` and reverted the file (trial worktree is clean).

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| #374 ntjx | MERGE first | Meets bead acceptance (optional `description:` validated 1-200, first-sentence fallback, /full and section and quote descriptions capped, test over every registry report asserts og == meta <= 200). Not setting `description:` in any file is allowed by the bead. `editorial.ts` stays unchanged (checked). |
| #375 te56 | MERGE after #374 (one import-line conflict) | Works and is tested; see the 8 MB and Vary notes below. |
| #376 n5p8 | MERGE | `<lastmod>` from build-time JSON, no D1 read; `/llms.txt` from the registry; tests and verify.sh assertions. Git-date caveat checked below. |
| #377 xq4i | MERGE | a, b, c each done and tested; `src=` and other params survive the `?p=` 302 after `p=` and `h=`. Footer wording is the author's choice, one line in layout.ts. |
| #378 3rj | MERGE | CSS-only, e2e checks for collapse (desktop) and Show less (both phone profiles), keyboard operable, NOT_PROSE unaffected. Short notes on phones still close only by re-tapping the marker (recorded by the author, needs an ingest change). |
| #380 076k | MERGE after #378 | 15 routes x 2 profiles x 2 widths, fixes press, landing row, `/full` URLs; editor-label check skips locally. Compares scrollWidth to the set width (right call). Verify now takes longer (about 6 min total per the PR). |
| #382 3gx4 | MERGE | See LCP note. Deviates from the bead on two points (no preload, simulated LCP worse) with measurements; fixes the a0rx 0.28 layout shift and sends no reader IP to Google. |
| ingest #84 f1o1 | MERGE, then release and pin bump (integrator steps 5-7) | Correct on every sample I checked; moves labels on all 20 reports and no slugs, ids or text. |

## Scrutiny items

**te56, 8 MB vs decision 0019's about 3 MB.**
- Behaviour is sound. Preview agents (cardyb, facebookexternalhit, Twitterbot, Slackbot, Mastodon, LinkedInBot, WhatsApp, Discordbot, Telegram, matched on UA substring) get the head-only page only when the `/full` body is over the threshold. Googlebot, other search engines, browsers and no-UA requests get the whole report. Canonical points at `/full`, the `?p=`/`?h=` quote description is preserved, and the page links to the contents page.
- `Vary: User-Agent` is set on every `/full` response that loads a report (set before the `?p=` redirect, not on the 404). The in-Worker cache (`maybeCached`) is skipped for preview agents on `?p=`/`?h=`, so a bot cannot poison the cached copy readers get. Plain `/full` is not cached in the Worker at all. Cloudflare's edge does not key HTML cache on `Vary: User-Agent`, and browsers key on it harmlessly. No effect on normal readers that I can find.
- Threshold: the head-only page carries the same tags as the full page, so lowering the threshold changes nothing for a preview that works today; the author's reason (3 MB "would change their working previews") is weaker than stated. 8 MB leaves Philip Morris (4.4 MB), Jan 6 (3.8) and PSI (3.1) on the whole-page path, which is where they were before. Mastodon's fetch limit is reportedly much smaller than Bluesky's, unverified. Not launch-blocking: the campaign links to landings and passages. Decision: ship at 8 MB (matches the decision's own "only Leveson today"), re-test after deploy, and lower the one constant to 3_000_000 if any 3-5 MB report fails on Mastodon (the test "Philip Morris still served whole to Bluesky" would then need updating).
- The record in decision 0019 documents the deviation. Bead rlp2 should keep Leveson as the one listed exception.

**n5p8, lastmod via git dates.**
- Computed in `pnpm prerender` with `git log -1 --format=%cs` over `full.md` and `editorial/<id>.yaml`; a report with no history gets no `<lastmod>` rather than a wrong one. Deploy is `./scripts/deploy-cloudflare.sh` from the integrator's own checkout (prerender runs there), not a CI clone. The checkout is flagged shallow but its graft is a 2019 commit, so every report date is real; I printed them: 2026-10-10 to 2026-10-25.
- Dates are coarse: a bulk re-ingest or any commit touching `full.md` bumps a report (most are 10-16/17 from the last ingest round). That is honest for crawlers and acceptable. Do not deploy from a `--depth` clone or a fresh shallow worktree; `verify.sh` now fails if `<lastmod>` is absent, which would catch it.

**3gx4, LCP 1.67 to 2.11 s in Lighthouse simulated mode.**
- Acceptable. Under applied (devtools) throttling LCP is level (1.40 s both) and the render-blocking Google stylesheet plus two origins are gone; simulated mode charges the extra html, css, font chain. Preload was measured and made both models worse (93 KB of fonts compete with the hero image), so omitting it is the right call even though the bead suggested it; re-adding is two lines in layout.ts. Metric-matched fallbacks remove the 0.28 layout shift (a0rx). The bead's "LCP must not get worse" is met only under applied throttling, so the post-deploy production Lighthouse run below is the real check.
- Nits, not worth blocking: the CSS header comment says the two faces "are preloaded in layout.ts" but the PR deliberately does not preload; the immutable cache rule in `assets/_headers` is `/fonts/*`, which the author confirmed arrives through the Worker's `/assets/*` route locally but should be confirmed on production (below). Font files are not content-hashed, so a future font change needs a new filename.

**f1o1, labels on all 20 reports.**
- Changed/total sections per report are listed in the PR (near-universal off-by-one fixed, mostly -1). My samples, against `full.md`: Grenfell Part 1 is `%%page 1%%` before the heading (was 3, now 1), Part 2 `%%page 33%%` before heading (was 35, now 33), Chapter 2 now 9; Hillsborough Report summary 4 to 3; 9/11 "1.2 Improvising a Homeland Defense" 15 to 14; Chilcot Introduction 5 to 4; Mueller INTRODUCTION TO VOLUME I 2 to 1 and Front matter now "i" (roman folio, a new label); Jack Smith "VOLUME ONE" 4 (pagination restarts, so the old 2 was wrong). Slugs unchanged for all six checked.
- Jack Smith's labels can go non-monotonic (4 then 2) because the report's pagination restarts; that is true to the source.
- Reading-guide pages in editorial files change (112 lines in `editorial.ts`); `pnpm editorial --check` is part of ship and will force the regeneration.

## Findings by severity

None blocking. Not fixed (no bead needed unless the integrator wants one):
1. ingest `pnpm typecheck` is red on main (`tests/covid-inquiry-passes.test.ts`, two TS2554). Fix before or with the release if the release script or CI runs typecheck.
2. 3gx4 stale comment about preload in `assets/styles.css` (one-line edit).
3. 3rj: short notes on phones have no close label (follow-up needing an ingest change and a corpus re-render, post-launch).

## Found only by the trial merge

- #374 and #375 both edit the import block of `src/templates/section.ts` (conflict). Resolution: keep both, i.e. the lines

```
import { HEAVY_FULL_WORDS } from "../lib/preview-agents";
import { quotedPassage, truncate, capDescription, firstSentence, quoteDescription, shareImage, shareImageAlt, sharedUrl, frontispiece, publicationYear, sourceAttributes } from "./report";
```

  (the second is #374's list; #375 adds only the first line). Everything else, including `src/index.ts` (#375, #376, #377 in different places), `src/templates/report.ts` (#374 and #375 different regions), `layout.ts` (#377 footer vs #382 head links) and `styles.css`/`e2e*.mjs` (#378, #380, #382), merged without help. Merge #374 first, then #375 needs a rebase onto main, or resolve at merge as above. Do not change the order of #378 and #380.

## What moves

- Reports: f1o1 changes section page labels in all 20 (contents page and reading guide); no ids, slugs or text, so no marks or alias effect and `pnpm corpus check` unchanged. No other PR touches report text. #374 changes landing, section and /full meta descriptions on every page; #375 changes Leveson's `/full` for preview bots and the landing link text for Leveson only.
- D1: none from these PRs (no text, ids or marks change). f1o1 needs no reindex.

## Integrator steps

Site PRs, one by one (squash or merge; keep this order). After each: `git pull`.

1. Merge #374. Then #375: resolve the `section.ts` import as above if GitHub shows a conflict (rebase `te56-full-head-1024` on main, keep both import lines, `pnpm typecheck && pnpm vitest run tests/full-preview.test.ts tests/description-length.test.ts`, push).
2. Merge #376, #377, #378 in that order, then #380 (GitHub retargets it to main once #378 is merged; if not, `gh pr edit 380 --base main`), then #382.
3. Ingest: in `~/src/reportsthatmatter/ingest` merge #84 (`gh pr merge 84 --repo reportsthatmatter/ingest`), then release per the ingest release process (next version after v0.29.0; section-page labels only, so a patch or minor is fine). Optionally fix the red `tests/covid-inquiry-passes.test.ts` typecheck first.
4. Site: `pnpm ship --plan`, then `pnpm ship --shared`. It bumps the `@rtm/ingest` pin, runs `pnpm prerender`, then `pnpm editorial` (expect only reading-guide `page` changes in `src/generated/editorial.ts`; commit it in the pin-bump PR). `--ack` the corpus/baseline gates only after reading them: expected "unchanged" for ids and text.
5. Before the production steps, `pnpm ship` shows the drift table. If sections' `meta.json` show as drifted for the 20 reports, run the normal `publish-report` for them (the PR's own view is that a deploy is enough; the table decides).
6. Deploy (`./scripts/deploy-cloudflare.sh`), then production verify (`VERIFY_SHARED=1 VERIFY_BASE=https://reportsthatmatter.org ./scripts/verify.sh`).
7. Production checks, in this order:
   - `curl -sI -A cardyb https://reportsthatmatter.org/reports/uk-leveson-inquiry/full` is small with `vary: user-agent`; `curl -s -o /dev/null -w '%{size_download}' https://reportsthatmatter.org/reports/uk-leveson-inquiry/full` (no UA) is about 10.9 MB. Then Bluesky's card validator on the Leveson `/full` URL. If a 3-5 MB report fails on Mastodon later, lower `FULL_PREVIEW_THRESHOLD_BYTES` to 3_000_000 in `src/lib/preview-agents.ts` and update the "Philip Morris served whole" test.
   - `curl -sI https://reportsthatmatter.org/assets/fonts/eb-garamond-latin.woff2` shows `cache-control: public, max-age=31536000, immutable`; if not, the `_headers` path (`/fonts/*`) needs `/assets/fonts/*` and a deploy.
   - Lighthouse mobile on `/reports/us-911-commission`, same method as the PR's table (production before 1.71 s LCP simulated, 1.58 s applied). If applied-throttle LCP is clearly worse than 1.58 s, add the two preload lines for the latin EB Garamond and Inter faces back in `layout.ts` (one-line change, measured worse locally, so only if production disagrees).
   - `curl -s https://reportsthatmatter.org/sitemap.xml | grep -c '<lastmod>'` is nonzero; `curl -s https://reportsthatmatter.org/llms.txt` lists 20 reports.
   - Spot-check three contents pages' page labels against the table above (Grenfell Part 1 p. 1, Chilcot Introduction p. 4, 9/11 1.2 p. 14).
8. Close beads on ship: ntjx, te56 (leave rlp2 open, Leveson exempt), n5p8 (items 2 and 4), xq4i, 3rj (and GitHub #111 closes with #378), 076k, 3gx4, f1o1. Run `bd dolt push`.
9. Clean up worktrees: `git -C ~/src/reportsthatmatter/reportsthatmatter worktree remove ~/src/reportsthatmatter/rtm-reviewpolish-1025` and the same for `ingest-reviewpolish-1025` in the ingest repo (`git -C ~/src/reportsthatmatter/ingest worktree remove ...`).

## Beads

None filed. Optional: a `stream:quality` task for the red ingest typecheck, and a post-launch `stream:product` task for 3rj's short-note close on phones. Verdict lines appended to each bead.

## Lessons

- A PR that departs from a decision's number (te56: 3 MB to 8 MB) should record why in the decision, as this one did; the reason should be checked against what the change actually costs (a head-only page loses nothing a preview needs).

## Getting better faster

- Slow: `verify.sh` takes about 6 minutes with the new mobile checks, and a shell `sleep` is blocked, so I polled with background loops. A `verify.sh --quick` for reviewers or a printed ETA would help.
- Caught late: the decision-vs-implementation threshold gap and the stale preload comment were only visible by reading PR bodies against the bead and decision text. Proposal: the `pr-review` skill's checklist should say "diff each PR's numbers against the decision it cites".
