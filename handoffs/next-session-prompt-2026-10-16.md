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
