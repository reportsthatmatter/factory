# Coordination plan, 2026-10-24

Written by the planning-and-coordination sub-session on Sat 2026-10-24. It comes after `next-session-prompt-2026-10-17.md`. Not committed. Nothing was changed: no code, no bead statuses, no other repos. The only side effect was `bd dolt pull`. Evidence was read from `origin/main`, beads, GitHub Actions, the public Bluesky API and production.

## 1. Where things stand

### Headline: the launch has not happened

- **`POSTER_LIVE` was never set.** `gh api .../actions/variables` returns no variables. Every scheduled `post-next.yml` run from 20 to 24 Oct succeeded as a dry run ("would be queued").
- **The Bluesky account @reportsthatmatter.org has 0 posts** (public API, 24 Oct). The custom handle switch did take effect.
- Whether Show HN or the personal-account launch thread went out on 20 Oct is unknown: nothing in the repos records it. Rufus to confirm (Q1).
- Beads still read as pre-launch. y2t.6 (Launch) and y2y8.7 (launch-week batch) are open, and y2t.1 is open.
- **Consequence for the queue.** The poster "selects the first unposted item whose date has arrived; a missed day is made up one post at a time" (`docs/poster.md`). Switching it on today without re-dating would post Challenger p.146 (dated 20 Oct) today. The queue would then run four or more days behind for the rest of the campaign. Kept-clear and news-day-only dates would drift against their posts. So re-date with `pnpm posts --start <date>` before going live.

### Shipped since the 17 Oct handoff

- Site **#354** "Add self-hosted Umami analytics to every page head" (Rufus, merged 20 Oct 19:32Z). It is **merged but not deployed**: the production `/` has no umami script. The local shared checkout `reportsthatmatter/` is one commit behind `origin/main` because of it (not pulled, to avoid touching a shared checkout).
- Beads closed since 17 Oct: 3g1h (CIA study + Litvinenko marked affected-community), fh6v (contact email), 9kn4 (Hillsborough licence: no Home Office email). All were recorded in the 17 Oct handoff.
- Nothing else has merged on site, ingest, marketing or factory since 17 Oct. The marketing and ingest PRs from 16–17 Oct are all merged. The local marketing checkout is 7 commits behind origin, which is harmless but should be pulled.

### In flight

- **4g4d.5** (in_progress): the final plain `migrate-hosting sync-d1` and the `sync-r2` 0-missing check were never recorded. It is still marked blocked by i1wy (the Rufus Cloudflare dashboard steps), but the cutover happened on 16 Oct, so i1wy is effectively done.
- **gweq** (poster timing): a week of measurements now exists. Crons 13:47/14:17/14:47/15:17 UTC actually started at 16:58–18:10 (24 Oct), 17:58–19:15 (23 Oct), 18:10–19:13 (22 Oct) and ~18:40–19:20 (21 Oct). The first run is 3–4.5 h late, which fails the "within 30 min of 13:47" target. Per the bead, fall back to a Cloudflare cron trigger.
- **bght.10.7**: the one-week highlights-feed check was due ~23 Oct and has not been done. It needs `pnpm feed status --remote --yes`.
- **4g4d.7**: decommissioning atomatic was due on or after 23 Oct and has not been done.

### Stuck or stale

- **Beads that look done but are open:** i1wy (cutover done 16 Oct), 5k36 (homepage order and NEWS_DAY_ONLY, shipped in #348?), 7pcf (tier rule in AGENTS.md/poster.md?), vx0u (campaign emails drafted in marketing #6), y2y8.7 (dated 13–17 Oct), y2t.1 (Bluesky part done; only Search Console/Bing remain), gqsy.3 (Jan 6 is a "candidate" but `reports/us-jan6-committee` is live). These need a burn-down pass to verify and close.
- `reports/pipeline.yaml` still says "The thirteen live reports"; there are 18. Every unit is waiting on y2t.6 to move to `announce`.
- Two queued campaign items are skipped for being over 300 graphemes: Post Office 6.205 (303) and Grenfell 2.19 (333). These are the same two the 17 Oct handoff flagged as Tier B for the campaign emails.
- PRs open since September or early October: #294 (marks edge cache, accepted 16 Oct but not merged) and #182 (Duelfer register), plus the matching `ingest-duelfer` PR.

### Decisions waiting on Rufus

- Launch status and a new date (Q1).
- Open `needs-user` / `decision` beads: y2t.1 remainder (Search Console + Bing), 4g4d.6 (old.* record, registration, atomatic zone export by ~30 Oct), y2y8.8 (first outreach batch), 38s.7 (improvement-architecture synthesis), 9u69, i8en, qap0, efso, fc3x, hx7j (Lehman licence).
- Campaign-group emails (vx0u) must go by **Mon 2 Nov**.
- A test mail to contact@reportsthatmatter.org (low priority).

### Backlog shape (open beads, 24 Oct)

| stream | open | handoff-ready specced (ready now) |
|---|---|---|
| marketing | 18 | 7 |
| reports | 24 | 3 |
| quality | 90 | 19 |
| product | 24 | 12 |
| platform | 19 | 12 |

236 beads are open in total. The last backlog snapshot is 2026-10-09.

## 2. Proposed workstreams, in priority order

### W1. Launch, take two (stream:marketing). Rufus steers; its own sub-session.

- **Goal:** the poster live, the first excerpt and Show HN out on a re-dated launch day, and the campaign emails sent by 2 Nov.
- **Beads:** y2t.6, y2y8.7 (re-date or close), y2t.1 remainder, pp7b (queue runway), vx0u (emails), y2y8.8 (outreach batch), 5k36/7pcf (verify), cl2e (blog post + HN plan, after launch).
- **Steps:**
  - Rufus picks a date (Q1).
  - Agent: `pnpm posts --start <date>` in a worktree, `pnpm posts --check`, a marketing re-date PR (checklist, threads, emails), agent review, then integrator.
  - Ship #354 (Umami) in the same deploy.
  - Day before: the gate check as in the 17 Oct handoff, plus `POSTER_TODAY=<date> pnpm post-next`.
  - Launch day: Rufus sets `POSTER_LIVE`. A manual run happens at 13:55 regardless, because gweq is not fixed. Then Show HN at 14:00.
- **Model:** Sonnet for the re-dating, checks and integration. Opus only if copy needs rewriting.
- **Depends on:** Q1; W2's deploy for Umami. The gweq fix is not needed for launch day (the manual run covers it) but is needed for the days after.
- **Repos:** site (queue tooling, poster.md), marketing (drafts, queue.yaml).

### W2. Platform close-out and poster timing (stream:platform). Unsupervised Sonnet integrator plus one Sonnet fix agent.

- **Goal:**
  - The atomatic account is retired cleanly.
  - The Datopian migration beads are closed.
  - The daily post lands near 13:47 UTC without a human.
  - Main is deployed.
- **Beads:**
  - 4g4d.5: sync-d1, then sync-r2 0-missing, then close; close i1wy with evidence.
  - 4g4d.7: export the D1 backup to `factory/scratch/`, then decommission. Destructive, so Rufus's one-line go-ahead first (Q4).
  - 4g4d.8: refuse `RTM_CF_ACCOUNT=atomatic` once it is gone.
  - gweq: a Worker `scheduled()` cron on Datopian at 13:47 that calls GitHub `workflow_dispatch` (needs a fine-grained token as a Worker secret, Q2). An alternative is to post directly from the Worker, but that duplicates the poster logic. Recommend dispatch.
  - bght.10.7: the feed one-week check.
  - Ship #354.
- **Model:** Sonnet throughout. gweq is `level:judgement` but small.
- **Depends on:** Q2 and Q4. It feeds W1.
- **Repo:** site.

### W3. Bead burn-down and backlog snapshot (all streams). Unsupervised Sonnet, `burn-down` skill.

- **Goal:** open beads reflect reality, so `bd ready` is trustworthy for every agent this run.
- **Scope:**
  - Verify and close the stale ones listed in §1.
  - Re-date anything still saying 13 Oct, 19 Oct or "launch week".
  - Check the gqsy candidates against the registry.
  - Fix the "thirteen" comment in `reports/pipeline.yaml` (a small PR).
  - Write `factory/backlog/backlog-2026-10-24.md`.
- **Model:** Sonnet.
- **Depends on:** nothing. Run it first, in parallel with W2.
- **Repos:** beads; site (pipeline.yaml comment); factory (snapshot).

### W4. Reader-facing polish from handoff-ready specced beads (stream:product). Unsupervised: 2–3 Sonnet fix agents, one Sonnet reviewer, then the W2 integrator.

- **Goal:** fix what a launch-day visitor is most likely to hit.
- **Beads:**
  - ntjx: og:description 300–560 chars.
  - te56: Leveson /full is 10.9 MB, LCP 15 s; pairs with rlp2.
  - f1o1: contents page labels off by one.
  - 3rj: footnote can't be collapsed.
  - 076k: mobile overflow.
  - xq4i: label the intro box as ours.
  - n5p8: SEO follow-ups (sitemap lastmod, IndexNow, llms.txt).
  - 3gx4 (from the 17 Oct list): self-hosted fonts, CLS 0.28.
- **Model:** Sonnet.
- **Depends on:** nothing. Ideally it ships before the W1 launch date. Batch it into one review and ship.
- **Repo:** site.

### W5. Quality: the grade and the improvement loop (stream:quality). Opus agent unsupervised for 38s.16; the 38s.7 synthesis needs Rufus.

- **Goal:** one per-report grade from existing measurements (38s.16), which unblocks `pnpm grade` (38s.19), `grade next` (38s.20) and the agent loop (38s.21). This is "get better faster" rather than one more fix.
- **Beads:** 38s.16, then 38s.19, then 38s.20; znfm (thresholds into docs/scoring.md, specced); then 38s.7 synthesis with Rufus (Fable, `level:design`).
- **In parallel, specced per-report fixes (Sonnet):**
  - Litvinenko 8rv and men.
  - b78.10 and fapr (new signals).
  - ebnp, 11g7 and jb6w (`ingest try` tooling).
- **Model:** Opus for 38s.16 and 38s.19/20; Sonnet for the specced ones; Fable for 38s.7 in a Rufus-steered session.
- **Depends on:** nothing for launch. Keep it out of the launch-week deploy unless it is byte-identical on the corpus.
- **Repos:** ingest, site, and report repos (one owner each).

### W6. Next reports (stream:reports). Rufus steers the choice; then the stage skills unsupervised.

- **Goal:** the next report or volume through the pipeline, and report-day readiness for the remaining Post Office volumes and Grenfell Vol. 7.
- **Beads:**
  - s1m5: report-day pipeline (dry run, watcher, stub page, paragraph-number gate).
  - gqsy.2 / gqsy.4: Post Office later volumes, Grenfell phases.
  - ivg.4: Duelfer hybrid (unparks Duelfer and resolves stale PR #182).
  - egsa: Deep Water EPUB.
  - 6n9u + njr9: CIA study introduction and hero, still missing.
  - ifb5.10/.11/.12: stage tooling, specced.
- **Model:** Opus for sourcing, evaluation and introductions; Sonnet for repo, first ingest, heroes and tooling.
- **Depends on:** Q7, which report.
- **Repos:** site, ingest, new report repos.

### W7. Post-launch content (stream:marketing). After W1. Opus, unsupervised drafts on branches.

- cl2e ("Getting the text right" blog post + HN plan).
- 9uog (per-paragraph dataset).
- z7u2 long-read.
- y2y8.11 (MARKETING.md foundation).
- 0z1o.
- Drafts only; Rufus publishes.

**Concurrency.** W2, W3 and W4 can start at once (≈5 agents). W1 starts as soon as Rufus answers Q1. W5 takes the next free slots. W6 waits for Q7. Stay within 5–7 agents.

## 3. Questions for Rufus, each with a recommended answer

1. **Did anything go out on 20 Oct (Show HN, your launch thread), and what is the new launch date?** Recommended: **Tue 27 Oct**. Re-date with `pnpm posts --start 2026-10-27`, set `POSTER_LIVE` that morning, do the manual run at 13:55 and Show HN at 14:00. If Show HN already went out, do a quiet poster start on 27 Oct with no second HN.
2. **Poster timing:** GitHub cron is 3–4.5 h late every day. Recommended: a Datopian Worker cron at 13:47 that dispatches `post-next.yml`, with a fine-grained GitHub token (Actions: write, this repo only) stored as a Worker secret. You create the token, or approve the agent using `gh`'s. The alternative is to accept posts at ~17:00–19:00 UTC.
3. **Umami (#354) is merged but not deployed. Ship it?** Recommended: yes, in the W2 deploy before launch. The /about copy already covers it.
4. **Decommission atomatic now** (D1 export to `factory/scratch/` first, then delete the Worker, D1 and R2 on atomatic)? Recommended: yes. Also do your 4g4d.6 zone export by ~30 Oct.
5. **The uncommitted /about AI-note wording in `rtm-hbnj-1016`** says "for some scanned reports … agrees word for word". Main says "for one scanned report, the Challenger report … closely matches". Keep or drop? Recommended: drop it, unless vision-assisted breaks are now used beyond Challenger. If they are, the W3 agent checks and files it as a one-line PR.
6. **Campaign-group emails (by Mon 2 Nov):** Post Office 6.205 and Grenfell 2.19 read as Tier B and are also over Bluesky's 300 graphemes. Recommended: send them text-only, without cards, and drop both from the queue. Send after the launch date so the site is "launched" when groups look.
7. **Which report next?** Recommended: s1m5 (report-day readiness for the Post Office final volumes and Grenfell Vol. 7), if either is expected before January. Otherwise ivg.4 Duelfer hybrid, which closes two stale PRs.
8. **38s.7 improvement-architecture synthesis:** do you want to steer it this run? Recommended: yes, after launch, in its own Fable session once 38s.16 has produced a grade table.
9. **Search Console + Bing (y2t.1) and a test mail to contact@:** still yours. Recommended: do both before the launch date.
10. **Stale PRs #294 (marks cache, accepted 16 Oct) and #182 (Duelfer):** recommended: rebase and ship #294 in W2 or W4; fold #182 into ivg.4.

## 4. Housekeeping

### Worktrees in `~/src/reportsthatmatter/`

There are 45 rtm-/ingest-/marketing- worktrees. I checked them and deleted nothing.

**Uncommitted work (look before removing):**

- `rtm-decisions-1016`: staged edits to decisions 0009, 0017 and 0024. These are older and shorter versions; main has the fuller records. Safe to discard.
- `rtm-launchdates-1016`: `docs/poster.md` re-dated to 20 Oct. Superseded by main. Safe to discard.
- `rtm-hbnj-1016`: `src/templates/about.ts` AI-note wording that is **not on main**. See Q5.

**Unpushed commit with content:**

- `rtm-highlightsfeed-1016`: b41106f9, the design doc plus "decision 0021 proposed". The feed shipped as decision 0023, and main has `docs/design/2026-10-16-public-highlights-feed.md`. Check that the copy on main matches before removing; it is likely superseded.

**Trial and review worktrees with unpushed trial-merge commits** (expected; their PRs are squash-merged):

- `rtm-review333*`, `rtm-review335`, `rtm-reviewbatch1..6*`, `rtm-reviewlicence`.
- `ingest-reviewbatch2*`, `ingest-reviewbatch3`.
- `marketing-reviewbatch6` (detached).

These are safe to remove.

**Broken (not git repos; leftover directories):**

- `rtm-cia-1016-reports`
- `rtm-fix333c-1016`
- `rtm-reviewbatch3-1016-reports`
- `rtm-reviewbatch3b-1016-reports`
- `rtm-reviewbatch5-1016`

Check the report repos for stale `git worktree` entries (`git worktree prune` in each).

**Open PRs, keep:**

- `rtm-duelfer` + `ingest-duelfer` (#182)
- `rtm-marksreads-1004` (#294)

**Merged branches, clean:** the rest (rtm-0eeq, ay85, cia, decisions2, factory, follow, gweq, moej, reportcount, v724, 0eeqrebase, ingest-5p44, the marketing-* ones). Safe to remove.

**Recommendation:** once Rufus agrees on Q5, the W3 agent runs `pnpm worktrees prune --apply`, removes the broken directories, and deletes the merged remote branches. Note o9c6: prune skips locked worktrees.

### Shared checkouts

- The site checkout is one commit behind `origin/main`, and marketing is 7 behind. Recommend a plain `git pull` on main in both; no branch switch is needed.
- The site's `.beads/issues.jsonl` shows as modified (beads export noise).

### Protocol

- Write `factory/protocols/agent-protocol-2026-10-24.md`:
  - Worktrees `*-1024`; logs in `factory/logs/2026-10-24/`; reviews `review-<scope>-2026-10-24.md`.
  - **Rufus is steering this run** through the coordination sub-session, so `needs-user` questions can come here rather than only being batched.
  - The launch date is TBD (parameter, not hard-coded).
  - 18 reports are live.
  - Atomatic is read-only until it is decommissioned, then gone.
- Update `factory/README.md` Layout to mention `handoffs/<date>-coordination-plan.md` as a handoff kind, or rename this file to the `next-session-prompt-` convention when it is committed.
- AGENTS.md: once gweq lands, update the poster timing line, and update the "Cloudflare" paragraph after atomatic is decommissioned (drop `--profile default` / `RTM_CF_ACCOUNT=atomatic`).
