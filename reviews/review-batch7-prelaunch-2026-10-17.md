# Review: batch 7, prelaunch (site #351, #352; marketing #5, #6), 2026-10-17

Reviewer: Claude Opus 5.5 agent. I did not touch production: no merge, deploy, publish, reindex, seed, `--remote` D1 call, `wrangler`, `pnpm ship` beyond `--plan`, no `POSTER_LIVE`, no workflow dispatch, no post. Production contact: anonymous GETs of `/about` and one Columbia paragraph link, plus read-only `gh run list`, `gh secret list` and `gh variable list`.

**Verdict: MERGE all four, site #351 then #352, marketing #5 then #6, each with `--merge`. Then deploy, because #352 changes `/about`, `site.ts` and the card assets. Production `/about` does not show the email address yet, and the Mon 19 gate check and the prelaunch notes both wait on it. Launch on Tue 20 Oct stands.**

## Scope

| PR | Branch | Head at start | Head reviewed (after fixups) |
| --- | --- | --- | --- |
| site #351 poster timing (gweq) | `gweq-poster-timing-1017` | f1ea3b26 | 5a62a36d |
| site #352 decisions batch (3g1h, fh6v, Columbia card) | `decisions2-1017` | 19f3736a | 19f3736a (no fixups) |
| marketing #5 checklist poster timing | `gweq-checklist-1017` | 291a4af | 0b55cff |
| marketing #6 Columbia swap, contact, campaign emails | `decisions2-1017` | 05d71bf | 43ae109 |

## Trial

- Site: worktree `~/src/reportsthatmatter/rtm-reviewbatch7-1017`, branch `trialbatch7-1017`. It is `origin/main` (f14b79f4) plus `git merge --no-ff` of #351 and #352, then #351's fixups (25032594). **No conflicts.** The two PRs share no file: #351 touches the workflow, `docs/poster.md`, `post-next.mjs`, `poster.ts` and the tests; #352 touches editorial, the registry, the queue, generated files, templates, cards and decisions.
- Site checks (logs `factory/logs/2026-10-17/rtm-reviewbatch7-1017-*.log`):
  - `pnpm typecheck` 0.
  - `pnpm test` 1282 passed, 1 skipped.
  - `pnpm editorial --check` 0.
  - `pnpm posts --check` 0. Its only warning is a PSI highlight over 300 graphemes, which is not queued.
  - `pnpm cards --check` 0 (95 cards match a fresh render).
  - `VERIFY_SHARED=1 ./scripts/verify.sh`: "All checks passed". `git status --short reports/` is empty afterwards.
  - After the #351 fixups: editorial, posts and cards re-run (all 0); `tests/poster.test.ts` and `tests/contact.test.ts` pass, 55 tests.
  - `pnpm ship --plan --state build/review-ship-state.json` 0. Its one guard problem is "not on main", which is expected in a worktree.
- Marketing: worktree `~/src/reportsthatmatter/marketing-reviewbatch7-1017`. I merged #5 then #6 at their final heads: **no conflicts**. The reverse order (#6 then #5) also merges clean, with an identical tree. Both PRs edit the checklist, but only on rows that are not adjacent. `python3 scripts/check-drafts.py` (against the site trial) reports "checked 13 files … 18 live; ok", exit 0 (log `marketing-reviewbatch7-1017-check-drafts.log`). Grapheme counts: org thread 5/5 is 299 (limit 300, the "5/5" label included; re-count after any edit), bio 235 (limit 256), UK thread last post 272.
- `gh pr view --json mergeable` at the final heads: all four MERGEABLE.
- Fixup worktrees: `rtm-reviewbatch7fix351-1017`, `marketing-reviewbatch7fix5-1017`, `marketing-reviewbatch7fix6-1017`. All three are detached, so the fix agents' worktrees were not touched.

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| site #351 | MERGE | The gate is right. It blocks a live run only between 00:00 and 12:59 UTC. A launch-day run that starts at 21:00, or even 23:59, still posts p. 146 that day. Dry runs are never gated. Four runs post once. The workflow checks out `ref: main`, so a later run sees the earlier run's `posted_at` commit. Fixed: the launch-day backup time is now 13:55, and the docs record the DID as `BLUESKY_HANDLE`. |
| site #352 | MERGE, then deploy | Litvinenko is flagged, its four `card: true` flags are gone, its three queue items are dropped, and the editor pick is regenerated. The Columbia p. 203 card is Tier A and queued for 2026-11-19 (Rufus confirmed on 17 Oct: keep it there). `/about#contact` leads with the mailto, which a test asserts. Decision records match what Rufus said. |
| marketing #5 | MERGE | The timing lines matched #351, but the backup was at 14:05, five minutes into the Show HN. Fixed (below). |
| marketing #6 | MERGE | The Columbia swap is complete: nothing left says p. 195 or "History is cause" except the line that explains the swap. The contact address is in the drafts. The campaign emails are sound. Fixed: the Peter Lutz spare and four other lines that name the dead or a private individual (below). |

## Poster gate (#351): what I checked

I simulated `runPoster` against the real queue with `notBefore: "13:00"` (script `/private/tmp/claude-501/-Users-rgrp-src-reportsthatmatter/d3fb3769-be08-4aee-a4de-cbd3b194341d/scratchpad/gate-sim.mts`, session scratchpad):

| Clock (UTC) | Mode | Outcome |
| --- | --- | --- |
| 20 Oct 12:59 | live | too-early (nothing read, nothing posted) |
| 20 Oct 21:00 | live | **posted** Challenger p. 146 |
| 20 Oct 23:59 | live | **posted** Challenger p. 146 |
| 21 Oct 00:30 | live | too-early (a 20 Oct cron that GitHub starts after midnight) |
| 20 Oct 08:00 | dry | dry-run, would post p. 146 (ungated) |
| 23 Oct 14:00 | live | nothing-due (kept-clear) |
| 21 Oct 13:47, 20 Oct missed | live | posts p. 146 (the overdue item first) |

So the launch-day post is lost only if every 20 Oct run starts after midnight, which is more than 8h43 late for the 15:17 cron (the worst delay seen was 7h53). The 13:55 manual backup covers launch day anyway. If a day is ever missed, the queue posts one day behind until a free day absorbs the delay. The gaps #352 leaves at 24, 28 and 31 Oct do exactly that.

The workflow's `POSTER_LIVE` expression maps `dry_run=true` to `false`, so a manual dry run is never live and never gated. `POSTER_NOT_BEFORE` is not set in the workflow, so the default 13:00 applies. Concurrency is `cancel-in-progress: false`, and a pending run can be replaced by a newer pending one. That does no harm: whichever run executes posts or finds the post.

Local dry runs on the trial: `POSTER_TODAY=2026-10-20 pnpm post-next` would post `challenger-accident:committee-concurs-dr-feynman-s:9b14d72b`. `POSTER_TODAY=2026-10-23` prints "kept-clear date: nothing is posted".

## First posts (queue on the trial)

- 20 Oct Challenger p. 146.
- 21 Oct Challenger p. 3.
- 22 Oct Senate PSI p. 330.
- **23 Oct nothing.**
- 24 Oct nothing (Litvinenko dropped).
- 25 Oct Leveson.
- 26 Oct Philip Morris.
- 27 Oct PSI.
- 28 Oct nothing.
- 29 Oct Leveson.
- 30 Oct Philip Morris.
- 31 Oct nothing.
- 1 Nov Leveson.

27 items in all; the last is Columbia p. 203 on 19 Nov. Nothing falls on 23 Nov or 18 Dec to 2 Jan.

## Fixups pushed

1. **#351, 5a62a36d.**
   - `docs/poster.md`: the launch-day backup was "if the 13:47 run has not started by 14:05". It now reads: at 13:55, before the Show HN at 14:00, unless the excerpt is already on the account, start one by hand. That is safe while the 13:47 run is still going, because runs queue and a run that finds today's post does nothing.
   - Step 2 now records that `BLUESKY_HANDLE` is the account DID `did:plc:owtfs5pk265ppfmojghzr56t` (set 17 Oct). It works as the login identifier, and `posted_url` becomes `bsky.app/profile/did:plc:…/post/…`, a valid link. So the pending handle switch does not touch the secret.
2. **marketing #5, 0b55cff.**
   - The checklist had the backup at 14:05, with Rufus already in the HN thread, and the agent check at the same minute. Now: agent check at 13:50, Rufus's backup at 13:55, the Show HN at 14:00, agent confirmation at 14:10.
   - Every leftover "14:00 excerpt" now reads "from about 13:47": the accounts table, the post-3 note, the Wed 21 row, `launch-thread-rufus.md` (When, No same-hour repeat) and `thread-us-investigations.md` (When).
3. **marketing #6, 37ffd97.** No launch draft now names the dead or a private individual.
   - `thread-us-investigations.md`: the Deepwater spare is now only the paragraph's second sentence ("BP listed seals and walruses…"). The first sentence names Peter Lutz, who had died, and the note no longer names him.
   - `passages-by-topic.md`: removed Litvinenko 10.11 ("intention of poisoning Mr Litvinenko") and 10.16 ("operation to kill Mr Litvinenko"), which are Tier C now that Litvinenko is affected-community. Added 8.87 (Lugovoy, Tier B, which is fine for a person's reply). Removed Saville 3.70, which names Gerald Donaghey, one of the dead. All three are recorded under "Not in this table".
   - `prelaunch-notes.md`: the Russia row was 10.16 and is now 8.87. **This is urgent: the notes go out Sat 17 to Mon 19.** If Rufus has already sent a 10.16 note, nothing more is needed: it is a one-to-one email, not a post.
   - `reddit-challenger-memo.md`: the facts for the first comment and the hook no longer name Ebeling or McDonald. Both are private individuals and both have since died. A flag is added that the official page image itself prints both names, which makes it Tier C under 63cd by the letter: Rufus decides (from 2 Nov, not launch week).
   - `show-hn.md`: the report list was missing the CIA torture report (2014) and Mueller (2019); both added. "Will you add the Mueller report" was stale (it is live).
4. **marketing #6, 43ae109.** The checklist row "add the repo secrets" now reads "Secrets: done 17 Oct", with the DID noted. The org bio is still to set.

I checked every launch draft. These lines are clean (officials in their role, public figures, or no names): the launch thread, the X post, the org thread, the pin and the bio; the UK thread (8.87 names Lugovoy, not the dead); the emails (Judge Kessler, in her role); the r/datasets and r/Journalism drafts; the campaign emails.

## Not fixed (for Rufus or later)

- **Deploy before the Mon 19 gate check.** Production `/about` has only the GitHub form; the address appears only after #352 is deployed. Send a test message to contact@reportsthatmatter.org after the deploy.
- **Litvinenko 8.87 in the UK thread is Tier B**, and Tier B wants a context line; the strategy has none for Litvinenko. This is week 3 or later, so there is time to write one.
- **Post Office 6.205 and Grenfell 2.19** are `tier: A` and `card: true`, and the campaign emails offer them as cards. The batch 6 review read both as B. Neither is queued. Rufus's "go with your recommendations" did not cover them; worth one line from him before the emails go out (by 2 Nov).
- **Campaign emails**: "send all four" names a count, but there are five groups plus the courtesy note. Cosmetic.
- **Thiokol memo image** (above): Rufus decides.
- **Off-peak cron delays are unmeasured.** Measure after the first week, as `docs/poster.md` says.

## Found only by the trial merge

Nothing conflicted. The one cross-PR mismatch was content, not git: #5's 14:05 backup against the checklist's own 14:00 Show HN.

## Integrator steps

1. Check heads: `gh pr view <n> --json headRefOid,mergeable`. Expect MERGEABLE at site 351 5a62a36d and 352 19f3736a, and marketing 5 0b55cff and 6 43ae109. A newer head needs a reviewer's word. Before each merge, re-run `gh pr view <n> --json mergeable` and wait while it says UNKNOWN: GitHub recomputes after every merge, and merging on UNKNOWN can fail or merge something stale.
2. Site:
   - `gh pr merge 351 --merge --match-head-commit 5a62a36d84d7d21d2df07bfc6fd2474a23d3d7b4`
   - `gh pr merge 352 --merge --match-head-commit 19f3736a2ebd3b11abc977ca33ae8553fb358cb2`

   Use this order: #351 first, because the crons are live from merge and it needs no deploy. The two PRs share no file, so the order does not matter for git.
3. Marketing:
   - `gh pr merge 5 -R reportsthatmatter/marketing --merge --match-head-commit 0b55cffd29743f4adc771dc7eec16bbfcf228982`
   - `gh pr merge 6 -R reportsthatmatter/marketing --merge --match-head-commit 43ae1098ece9278b2dba59be12a9b2c731779f3d`

   No conflicts in either order.
4. `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only`, then `VERIFY_SHARED=1 ./scripts/verify.sh`. `git status --short reports/` must be empty.
5. **Deploy** (`about.ts`, `site.ts`, generated files, a new Columbia card, four Litvinenko cards removed): `./scripts/deploy-cloudflare.sh` (Datopian). The drift table must list nothing. No pin bump, release, republish, reindex, seed or D1 migration: no report text or ids move.
6. Production check:
   - `VERIFY_SHARED=1 VERIFY_BASE=https://reportsthatmatter.org ./scripts/verify.sh`.
   - By eye: `/about#contact` leads with `contact@reportsthatmatter.org`.
   - Send a test email to that address and confirm it reaches Rufus.
7. Beads: append this verdict to gweq, 3g1h, fh6v and vx0u. Close gweq after the first week's run times are measured, not on merge. `bd dolt push`.

## Launch-day commands for Rufus (consistent with #351)

The secrets are set (17 Oct; `BLUESKY_HANDLE` is the DID). `POSTER_LIVE` is unset. Nothing below is for an agent to run live.

1. **Mon 19 (agent, dry run only):**
   - `POSTER_TODAY=2026-10-20 pnpm post-next` must show `challenger-accident:committee-concurs-dr-feynman-s`.
   - Then `gh workflow run post-next.yml -R reportsthatmatter/reportsthatmatter -f dry_run=true` and `gh run watch`. It must be green, log in with the DID, read the account, and print "DRY RUN: would post challenger-accident:committee-concurs-dr-feynman-s…". The dry run is ungated, so it works at any hour.
2. **Tue 20, by 12:45 UTC:** `gh variable set POSTER_LIVE --body true -R reportsthatmatter/reportsthatmatter`. This must be before 13:47. A live run before 13:00 posts nothing anyway.
3. **13:47 UTC:** the first scheduled run is due. The other crons are 14:17, 14:47 and 15:17; whichever starts first posts and the rest do nothing.
4. **13:50 (agent):** `gh run list --workflow post-next.yml -R reportsthatmatter/reportsthatmatter -L 3`, and check the org account for the p. 146 excerpt.
5. **13:55 UTC, before the Show HN:** unless the excerpt is already on the account, run `gh workflow run post-next.yml -R reportsthatmatter/reportsthatmatter -f dry_run=false`. This is safe while another run is going.
6. **14:10 (agent):** confirm the post and the `posted_url` commit on main.
7. **Off switch:** `gh variable set POSTER_LIVE --body false -R reportsthatmatter/reportsthatmatter`.
8. **Later days:** the scheduled runs post from 13:47 if GitHub starts them on time. 23 Oct posts nothing.

## Beads

Noted: gweq (verdict; measure first-week start times), 3g1h, fh6v (test message after the deploy), vx0u (emails reviewed; 6.205 and 2.19 tiers above). None filed.

## Lessons

- A launch schedule has two clocks, the cron's and the people's. #351 and #5 got the cron right and put the backup inside the Show HN's 75 minutes. Read the checklist as a timeline for one person, not as a list of correct steps.
- Changing a report's tier status (3g1h) changes every draft that quotes it, not only the queue and the cards. The prelaunch notes, the reply table and the spares all still carried Litvinenko lines that name the dead.

## Getting better faster

- **What slowed me:** finding lines that name the dead was a by-eye read of 865 lines of drafts. My first check loop also silently failed under zsh (`pnpm $c` with no word-splitting), so I had to re-run three checks.
- **What would have caught it:** `check-drafts.py` already maps each quote to its report. It could run the site's Tier C test (`src/lib/tier-c.ts`, the word list plus a names list) on every quote from an `affected_community` report and print the file and line. That would have found Lutz, Litvinenko 10.11 and 10.16, and Saville 3.70 in one run.
- **Proposal:** export `tierCFindings` as a small CLI (`pnpm editorial tier-c --text -`) and call it from `check-drafts.py` for affected-community quotes, warning on Tier C outside the "Not in this table" sections.
