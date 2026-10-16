# Next session: reportsthatmatter (written 2026-10-16, ~21:30 CEST)

Replaces `next-session-prompt-2026-10-10.md`. Start in `~/src/reportsthatmatter/reportsthatmatter`: `git pull`, `bd dolt pull`, read AGENTS.md, then this file. Agent note for the day: `../protocols/agent-protocol-2026-10-16.md`. Reviews: `../reviews/*-2026-10-16.md`.

## Shipped 2026-10-16 (site main `c05cc4d2`, ingest v0.28.0 `36712f4`)

- **Hosting moved to the Datopian Cloudflare account** (Workers Paid; decision 2oz, epic 4g4d, PR #335, runbook `docs/hosting-migration.md`). Zone `reportsthatmatter.org` active on Datopian; Worker custom domains apex + www, zone route `old.*`. Outage ~18:27–19:05 UTC: adding the zone auto-imported proxied A records and the nameservers switched (error 1000); lesson filed. `~/src/reportsthatmatter` is bound to the `datopian` wrangler profile (old bindings backed up in `scratch/`). D1 limits are now monthly, no daily stop.
- **Public highlights feed** (bght.10; design #331, decision 0023, impl #333): `/highlights` = Recently highlighted (verbatim-checked, anonymous, 30-min delay, affected-community reports excluded) + Editor's picks; `/highlights/yours` = the browser list with an opt-out; `pnpm feed` moderation and kill switch. `public_marks` empty at ship.
- **Privacy correction** (#332): the "never sent to us" claim was false; corrected with a dated changelog note.
- **Poster queue clean-up** (#327, ay85): 19 Tier C items out, Challenger-first / spacing / blackout as tested rules, plate-less Post Office and Hillsborough cards. Start date is a placeholder (2026-10-20). POSTER_LIVE not set.
- **Senate study paragraphs** (jc46, ingest #80, report #2, site #330): severed-into-quote 101→23, severed-paragraph 57→24; republished. jc46 stays open (bar 13).
- **Tooling**: ship --reset (fjej), publish retries (moej), pin-bump shared guard (0eeq), editorial --check (v724), ingest link drift (irfs), control-character check (5p44).
- **Copy/marketing**: count-free launch copy + count check (marketing#3, #322), Follow on Bluesky footer link (#326).
- **Factory**: this repo, public at github.com/reportsthatmatter/factory (decision 0021).

## Pending, agent-doable

- `pnpm migrate-hosting sync-d1` (plain, from `~/src/reportsthatmatter/rtm-datopian-1016`, branch pulled) on 2026-10-17; confirm `sync-r2` reports 0 missing. Then close 4g4d.5. Never `import-d1` / `sync-d1 --versions`.
- Decommission atomatic on/after 2026-10-23: bead 4g4d.7 (Rufus decided).
- Follow-ups filed today: zh1w (P1, editorial warns on Tier C card:true), fzdo (VERIFY_SHARED docs), 4g4d.8 (split publish if RTM_CF_ACCOUNT=atomatic), jp6i (remote-gate test helper), a0rx (flaky Pixel 7 layout-shift), 9u3h (site returns real D1 errors), bpcc (anchor check), lkt6 (P3 now), ve75 (whole-word excerpts), bght.10.7 (one-week feed check ~2026-10-23).
- Integrator retro: bump-pin --shared should commit/push report repos (main/master); ship should tolerate a dirty `.beads/issues.jsonl`; production preflight in ship/publish --status.
- Housekeeping: `pnpm worktrees prune --apply`; remove today's review/integration worktrees.

## Questions for Rufus

1. **Launch date** — then re-date the queue (`pnpm posts --start <date>`) and set POSTER_LIVE.
2. **63cd** affected-community tier rule (some lower-tier 9/11 items still queued) and **80qf** which report leads / news-day-only reports (Jack Smith is 2nd, 21 Oct).
3. **Bluesky custom handle**: Settings → Account → Handle → reportsthatmatter.org (DNS TXT is in place on the new zone).
4. **4g4d.6**: confirm the domain registration is on Datopian; export the old atomatic zone DNS by ~2026-10-30; old.* scanned record (optional).
5. **3d41** email capture (recommended: none at launch).
6. Two published blog posts still say "thirteen reports" — leave or update?
7. Still open from 10 Oct: licences (y2y8.9, P2), AI wording (0ino), marks cache #294, Search Console/Bing (y2t.1).

## Update 2026-10-17 ~00:00 CEST: launch batch shipped

- Launch is **Tue 2026-10-20**; Rufus accepted all recommended defaults (see `../protocols/agent-protocol-2026-10-16.md`, evening update; decision records 0009, 0015-0020, 0022, 0024-0032).
- Shipped and deployed 2026-10-16 21:47Z (Worker 5eeeb91d): batch 5 (#337-#344: affected_community flag, Tier C fails, release-tooling fixes, remote-gate tests, CLS fix), decisions #345, launch dates #346, queue rules #348 (tier A only for affected reports, news_day_only Jack Smith/Jan 6/Chilcot, kept-clear dates, first post Challenger p.146 on 20 Oct, runway to 18 Nov), source line + /about#contact + removal form + /about#ai #349, licences #347 + all repos (MIT; per-report text status), marketing #4 (launch-day checklist `drafts/2026-10/launch-day-checklist.md`), changelog #350. Production verify passes.
- Reviews: `../reviews/review-batch5-2026-10-16.md`, `review-batch6-launch-2026-10-16.md`, `review-licences-2026-10-16.md`.
- Worktree clean-up removed ~66 merged, pushed worktrees (more than intended; nothing lost). Kept ones with unpushed/uncommitted work are listed in the integrator's report: trial review worktrees, rtm-decisions/hbnj/launchdates (uncommitted), unmerged branch worktrees.

## Launch steps (Rufus unless noted)

1. By Sat 17: `gh secret set BLUESKY_HANDLE` and `gh secret set BLUESKY_APP_PASSWORD` (password at `~/.config/bluesky/reportsthatmatter`); switch the Bluesky handle to reportsthatmatter.org.
2. Mon 19 (agent): gate check, `POSTER_TODAY=2026-10-20 pnpm post-next`, `gh workflow run post-next.yml -f dry_run=true`, production verify.
3. Tue 20: `gh variable set POSTER_LIVE --body true` before 14:00 UTC; at 14:00 `gh workflow run post-next.yml -R reportsthatmatter/reportsthatmatter -f dry_run=false` (scheduled run is 4-8 h late: gweq); then the checklist.

## Open questions (new)

fh6v contact email inbox; 3g1h Litvinenko affected?; vx0u campaign-group emails before 12/14 Nov; 0018 phone nav option 2 OK?; Columbia "History is cause" card (Tier B); 9kn4 Hillsborough licence (recommend keep live while asking the Home Office + TNA); hx7j Lehman; MIT holder name. Agent-doable next: gweq (late cron), pp7b (queue runway), sync-d1 (4g4d.5), ba23, y8t6, 4g4d.8 remainder, decommission 4g4d.7 on/after 23 Oct.

## Update 2026-10-17 ~01:30 CEST: pre-launch batch 7 shipped

- Deployed 2026-10-16 23:15Z (Worker a2d5c9c8): poster off-peak crons 13:47/14:17/14:47/15:17 UTC + not-before 13:00 (#351, gweq open until a week measured), Litvinenko affected + Columbia Tier A card on 19 Nov + /about#contact email (#352), changelog #353; marketing #5, #6 (no draft names the dead; campaign-group emails drafted). Review: `../reviews/review-batch7-prelaunch-2026-10-17.md`.
- contact@reportsthatmatter.org → rufus@lifeitself.org via Cloudflare Email Routing (Datopian zone; rule f93d4f08). Rufus to send a test.
- Bluesky: app password works (identifier = DID or reportsthatmatter.bsky.social). Actions secrets set (BLUESKY_HANDLE = did:plc:owtfs5pk265ppfmojghzr56t). Workflow dry run green but exits before login until 20 Oct; local login tested OK. Handle still not reportsthatmatter.org.
- Local `POSTER_TODAY=2026-10-20 pnpm post-next` selects Challenger p. 146.

## Launch day (Tue 20 Oct), from the batch 7 review

Mon 19 (agent): gate check, production verify, `gh workflow run post-next.yml -f dry_run=true` after 13:00 UTC on 20 Oct is the first real login test, so on Mon do a local dry run with `POSTER_TODAY=2026-10-20`. Tue 20: Rufus `gh variable set POSTER_LIVE --body true` before 13:47 UTC; agent check 13:50; Rufus backup `gh workflow run post-next.yml -R reportsthatmatter/reportsthatmatter -f dry_run=false` at 13:55 only if nothing posted; Show HN 14:00; agent confirms 14:10; then `marketing/drafts/2026-10/launch-day-checklist.md`.

Open for Rufus: Thiokol memo image (prints two names) in the prelaunch notes; Post Office 6.205 / Grenfell 2.19 offered as cards in the campaign emails but read as Tier B; handle switch; Search Console/Bing; Hillsborough licence email (9kn4); campaign emails by 2 Nov.
