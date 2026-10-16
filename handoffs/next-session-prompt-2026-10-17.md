# Next session: reportsthatmatter (written 2026-10-17, ~01:45 CEST)

Replaces `next-session-prompt-2026-10-16.md` (read it for the full record of 16 Oct). Start in `~/src/reportsthatmatter/reportsthatmatter`: `git pull`, `bd dolt pull`, read AGENTS.md, then this file. Standing agent note: `../protocols/agent-protocol-2026-10-16.md` (still valid; make a dated note for your session). Reviews of 16–17 Oct: `../reviews/*-2026-10-1[67].md`.

## State

- **Launch: Tue 2026-10-20.** Everything that gates it is shipped (site main `82685018`, Worker `a2d5c9c8`, deployed 2026-10-16 23:15Z; production verify passes). Rufus accepted every recommended default (decision records 0009, 0015–0020, 0022–0032).
- **Hosting is the Datopian Cloudflare account** (Workers Paid; runbook `docs/hosting-migration.md`). `~/src/reportsthatmatter` is bound to the `datopian` wrangler profile. Atomatic is reachable as `--profile default` for reads/rollback only, and is decommissioned on/after 2026-10-23 (bead 4g4d.7, decided).
- **Bluesky:** handle is now **@reportsthatmatter.org** (switched 2026-10-17 via the API). Actions secrets `BLUESKY_HANDLE` (= DID `did:plc:owtfs5pk265ppfmojghzr56t`) and `BLUESKY_APP_PASSWORD` are set; `POSTER_LIVE` is unset. The app password lives at `~/.config/bluesky/reportsthatmatter`.
- **Poster:** crons 13:47/14:17/14:47/15:17 UTC, not before 13:00, once a day, kept-clear days skipped. First posts: 20 Oct Challenger p.146, 21 Oct Challenger p.3. Queue runs to 19 Nov (Columbia p.203, kept there by Rufus). The workflow dry run has never logged in (nothing due before 20 Oct); local login with the same credentials works.
- **Contact:** contact@reportsthatmatter.org → rufus@lifeitself.org (Cloudflare Email Routing on the Datopian zone). Shown on /about#contact. Not yet tested by mail (Rufus: low priority).
- **Highlights feed** live (`/highlights`, `/highlights/yours`; `public_marks` was empty at ship; one-week check bght.10.7 ~2026-10-23).
- **Factory** is public: github.com/reportsthatmatter/factory (logs and scratch stay local).

## Launch-day plan (agent steps marked)

1. **Mon 19 (agent):** gate check on production (/about#contact, /about#ai, a ?p= source panel, /highlights), `VERIFY_SHARED=1 VERIFY_BASE=https://reportsthatmatter.org ./scripts/verify.sh`, `POSTER_TODAY=2026-10-20 pnpm post-next` locally (expect Challenger p.146), `pnpm posts --check`, marketing `scripts/check-drafts.py`.
2. **Tue 20:** Rufus `gh variable set POSTER_LIVE --body true` before 13:47 UTC → **agent checks at 13:50** that the post went out (`gh run list --workflow post-next.yml`, the org profile) → only if nothing posted, Rufus runs `gh workflow run post-next.yml -R reportsthatmatter/reportsthatmatter -f dry_run=false` at 13:55 → Show HN 14:00 → agent confirms at 14:10 → rest per `marketing/drafts/2026-10/launch-day-checklist.md`. Off switch: `gh variable set POSTER_LIVE --body false`.
3. **Evening 20 Oct (agent):** day-one numbers per the checklist.

## Agent-doable next (no Rufus needed)

- 4g4d.5 (in progress): one plain `pnpm migrate-hosting sync-d1` from `~/src/reportsthatmatter/rtm-datopian-1016` if it still exists, else a fresh worktree on main (never `import-d1` / `sync-d1 --versions`); confirm `sync-r2` reports 0 missing; then close 4g4d.5.
- 4g4d.7: decommission atomatic on/after 2026-10-23 (D1 export backup first).
- 4g4d.8: refuse `RTM_CF_ACCOUNT=atomatic` in seed-highlights, feed --remote, deploy-cloudflare.sh, ship's publish.
- gweq: measure a week of poster start times (target within 30 min of 13:47), then close or fall back to a Cloudflare cron trigger.
- pp7b: queue runway past 19 Nov (mark more Tier A highlights per strategy §8, or new excerpts); y8t6 (check-drafts misses some counts); ba23 (Mueller Vol II source link); bpcc; ve75; 9u3h; lkt6 (P3); 3gx4 (self-host fonts: real CLS 0.28 on cold shared links).
- jc46 (CIA severed paragraphs 24 → bar 13), fgle.
- Housekeeping: 32 worktrees remain; `pnpm worktrees prune --apply` once they're >24 h old; some hold uncommitted work (rtm-decisions, rtm-hbnj, rtm-launchdates) or unpushed trial commits — check before removing.

## For Rufus

1. Campaign-group emails (`marketing/drafts/2026-10/campaign-group-review-emails.md`) must go by **Mon 2 Nov**; before sending, settle Post Office 6.205 and Grenfell 2.19 (offered as cards but read as Tier B; either swap for Tier A lines or send them text-only).
2. Search Console + Bing verification (y2t.1 remainder).
3. Test mail to contact@reportsthatmatter.org (low priority).
4. Decided 2026-10-17: Thiokol memo image is fine to post; no Home Office email about Hillsborough's licence (9kn4 closed); Lehman stays live (hx7j low-priority ask).
