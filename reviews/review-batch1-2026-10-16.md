# Review: batch 1, 2026-10-16 (site #321, #322, #326, #327; marketing #3)

Reviewer: Claude (Opus) agent, `pr-review` skill. I did not touch production: no merge, release, tag, deploy, publish, `seed-highlights --remote`, `wrangler d1 execute --remote`, `POSTER_LIVE` or posting. I pushed fixup commits to four PR branches (listed below) and wrote beads.

**Verdict: all five MERGE, in the order below, after the fixups pushed in this review. #327 gates launch. Nothing blocks.**

## Scope

| PR | Branch | Head at start | Head after fixups | Beads |
| --- | --- | --- | --- | --- |
| site #327 Bluesky queue clean (P0, launch-gating) | `ay85-queue-clean` | `56d61f95` | `ce556392` | ay85 (also nm83, 63cd) |
| site #321 factory paths + decision 0021-factory-repo | `factory-1016` | `5bedd35c` | `f5a83236` | jcht, re74 (closed) |
| site #322 launch plan count-free copy (draft) | `reportcount-1016` | `fe998af6` | `01f326a1` | y2t.7 |
| site #326 Follow on Bluesky + decision on email capture (draft) | `follow-bluesky-1016` | `955d859b` | `a1537aad` | y2t.8, 3d41 |
| marketing #3 count-claim check + count-free drafts (draft) | `reportcount-1016` | `1e58259` | `a43a2b6` | b5td, y2t.7 |

All four site PRs branch from `origin/main` `54d314ba`, 0 commits behind.

## Trial

- Worktree `~/src/reportsthatmatter/rtm-reviewbatch1-1016`, branch `trialbatch1-1016` from `origin/main` `54d314ba`, `pnpm bootstrap`. Merged with `--no-ff` in the order #327, #321, #322, #326. **No textual conflicts**: the PRs touch disjoint files (#321 AGENTS.md, protocol, skills, `docs/decisions/0021-factory-repo.md`; #322 one plan; #326 templates, CSS, e2e, decision; #327 editorial, cards, `src/generated/cards.ts`, queue, `src/lib/posts.ts`). A fresh merge of the final heads onto `origin/main` gives the same tree as the trial (`84ead6c1`).
- Marketing: worktree `~/src/reportsthatmatter/marketing-review3-1016` (branch `review3fix-1016`, pushed to `reportcount-1016`).
- Logs in `~/src/reportsthatmatter/factory/logs/2026-10-16/`:

| Check (on the trial merge) | Result | Log |
| --- | --- | --- |
| `pnpm typecheck && pnpm test`, first run | **fail**: `tests/decisions.test.ts`, 4 tests, all from "number 0021 is taken by 2 records" | `rtm-reviewbatch1-1016-test.log` (overwritten by later runs) |
| same, after the 0022 renumber on #326 | pass, 1113 tests | `rtm-reviewbatch1-1016-test.log` |
| `pnpm editorial`, `pnpm cards` (tree stays clean: cards are reproducible), `pnpm posts --check`, `pnpm quality check`, `pnpm corpus check`, `pnpm aliases check`, `pnpm decisions check`, `pnpm lessons check` | all exit 0 | `rtm-reviewbatch1-1016-checks.log` |
| `VERIFY_SHARED=1 ./scripts/verify.sh` | exit 0, "All checks passed." (456 checks, including #326's follow-link e2e on iPhone 13 and Pixel 7) | `rtm-reviewbatch1-1016-verify.log` |
| Tier C fixup: `pnpm editorial && pnpm cards && pnpm posts --drop-stale`, then `pnpm posts --check`, `pnpm typecheck`, `pnpm test` | pass, 1111 tests (2 per-card cases went with the deleted cards). The plain `pnpm posts` run exits 1 because of the 17 skipped candidates (documented behaviour on main, 5qmm). | `rtm-reviewbatch1-1016-tierc.log` |
| `pnpm ship --plan --state build/review-ship-state.json` | exit 0. Guard stops only on "not main" (expected in a trial). 0 of 18 repos need a pin bump; no report text changes | `rtm-reviewbatch1-1016-shipplan.log` |
| marketing `python3 scripts/check-drafts.py` (RTM_SITE = trial) | exit 0, 18 live. Mutation test: "twelve reports", "12-report", "19 so far" each FAIL | (inline) |

Not applicable: ratchet (no report text or quality signal moved), `pnpm ingest try` (no ingest change), D1 (no publish; the `card:` flag changes don't change the highlights `seed-highlights` writes).

## Verdicts

| PR | Head | Verdict | Reason |
| --- | --- | --- | --- |
| site #327 | `ce556392` | **MERGE first** | Meets ay85: 9 items gone, rules in code with tests, `--check` enforces them, the two cards render without plates. The review added 10 more Tier C removals (below). Gates `POSTER_LIVE`. |
| site #321 | `f5a83236` | **MERGE second** | Paths consistent: no stale top-level coordination path is left in AGENTS.md, `docs/`, `.claude/` or `scripts/`. Keeps decision **0021**, now recorded as decided: public repo (Rufus, 2026-10-16). |
| site #322 | `01f326a1` | **MERGE third** (mark ready) | Docs only. The review fixed one missed count and a post over 300 graphemes. |
| site #326 | `a1537aad` | **MERGE fourth** (mark ready) | Plain link, no script, tested. DID checked independently. Decision renumbered to **0022**. |
| marketing #3 | `a43a2b6` | **MERGE** (independent of the site order; mark ready) | Check works as specified. The review widened it to catch "N of them", which found the launch thread itself still saying 16. |

## Findings, by severity

### Fixed on the branch

1. **#327: ten more Tier C `card: true` highlights** (`ce556392`). The PR removed the bead's nine items and said "not audited" for the rest. Checked against the strategy s8 Tier C test, eight more in affected-community reports fail it outright:
   - Hillsborough: `disclosed-documents-show-multiple-factors` ("the deaths of the 96 victims"), `2-10-7-documents-considered` ("those who died ... might have survived"; its card q-301a650b was still in `CARDS`), `2-12-31-greatest-prominence` (The Sun's "THE TRUTH" smear).
   - Saville: `3-35`, `3-70`, `3-106`, `3-109` (the dead and injured named and their shooting described).
   - Deepwater: `if-bp-s-response-capacity` (Peter Lutz, a named private individual, "had died").

   They were missing from the queue only because of the 300-grapheme and page-number skips, so a page-number fix would have put "the deaths of the 96 victims" back on a plain `pnpm posts` run. Their cards also still shipped. I also removed the two CIA torture-report items queued for **16 and 17 Dec** (`aggressive-phase-interrogation-continued-until`, the coffin box; `times-abu-zubaydah-described-hysterical`, waterboarding). The CIA report postdates the strategy's affected list, but both items name a detainee and describe his physical suffering, which makes them obviously Tier C in substance. This is a reversible default, recorded on 63cd. All ten stay as on-page highlights. Result: 10 cards deleted, queue 66 → 64, no other date moved.
2. **#326 and #321 both used decision 0021** (`41b957ad`, `a1537aad`). #321 was opened first, is not a draft and merges first, so it keeps 0021. On #326 I renamed `0021-email-capture.md` to `0022-email-capture.md` with its `# 0022.` title. No other file referenced it. I also updated the PR body and bead 3d41's description. `tests/decisions.test.ts` caught the clash on the trial, as R12 intends.
3. **#322: two counts missed, and a post over the limit** (`f1873e12`, `d89776db`, `01f326a1`).
   - Appendix B still said "13 of them" twice ("We rebuilt 13 of them as web pages instead.", "13 of them, rebuilt as web pages."). The checker's pattern did not match "N of them".
   - Appendix B 2/6 was 314 graphemes on the branch (308 on main), over Bluesky's 300, under a note saying 263. It is now 286.
   - Every grapheme note in the appendix was recomputed with `graphemeLength` from `src/lib/posts.ts`. All now match.
4. **marketing #3: the launch thread still had a count** (`a43a2b6`). `check-drafts.py` now also matches "N of them". It immediately found `drafts/2026-10/launch-thread-rufus.md` lines 18 and 36 ("I've rebuilt 16 of them"), now "some of them". The Bluesky post is 295 graphemes.

5a. **#321: decision 0021 recorded as decided** (`f5a83236`), on the supervisor's relay of Rufus's decision. I confirmed independently that https://github.com/reportsthatmatter/factory exists and is PUBLIC, and that re74 is closed.
    - Status is "decided (accepted: option (c), public)", dated 2026-10-16, decided by Rufus. The template's vocabulary is open/proposed/decided, so "accepted" goes in the parenthesis.
    - Option (c) no longer says "private" (it notes it was proposed private).
    - A consequence now says the repo is public, so it carries no secrets.
    - AGENTS.md Layout and `docs/agent-protocol.md` name the public repo and give the clone line `git clone git@github.com:reportsthatmatter/factory ~/src/reportsthatmatter/factory`.
    - `pnpm decisions check` and `pnpm test` (1111) pass on the trial with it merged.

### Not fixed, with beads

5. **The tier rule has no check** (ay85's acceptance field asks for "a test fails on card:true outside Tier A"; its description defers that to 63cd). Filed **reportsthatmatter-zh1w** (P1, specced): `pnpm editorial` warns now on Tier C words in `card: true` highlights of `affected_community` reports, and fails once 63cd lands. Integrator: close ay85 on ship with a note that the test moved to zh1w.
6. **Tier B items still queued for affected reports** (63cd, needs-user; noted there). By the strategy's test these have a non-institutional subject or a pronoun pointing outside the quote:
   - 9/11: Minneapolis "He said ...", Tenet "blinking red", "This calculus".
   - Deepwater: "It appears instead they ...".
   - Challenger: "Executive Summary. HELP! ...", which also carries a heading fused into the quote.
   - Post Office: "postmasters began to complain".

   Under s8, Tier B is never posted by the poster. That is Rufus's decision, not a launch blocker as the bead is scoped.
7. **Jack Smith is day 2 (21 Oct), and the queue holds 6 Jack Smith, 5 Jan 6th and 5 Chilcot items on ordinary days.** s8 says these are quoted "only on their own news days". This is noted on 80qf (needs-user).
8. **Plates on site pages** (Post Office wordmark, Hillsborough plan) are bead nm83, already filed by the #327 agent.
9. **Published posts say "thirteen/13 reports"** (`content/posts/getting-the-text-right.md`, `one-file-per-report.md`). These are dated posts; leaving them is right (noted on y2t.7).

### Checked, no finding

- #326: `did:plc:owtfs5pk265ppfmojghzr56t` is what `reportsthatmatter.bsky.social` resolves to (public API). The profile is "Reports That Matter" and `_atproto.reportsthatmatter.org` TXT holds the same DID. The link is not on `/` or `/full`; no third-party script is added.
- #327 code: `scheduleItems` counts posted items toward spacing, re-dating keeps posted items fixed, and appended items obey the blackout. `queueViolations` ignores posted items. The plate-less Post Office default card was looked at: no wordmark.
- #321 decision record uses the template's sections. jcht and re74 are filed.

## VERIFY_SHARED=1: is it right?

Yes, for an agent that owns no report repo, and it is safe. The docs are wrong about why.

- `verify.sh` runs `pnpm ingest aggregate`, which **only reads** `full.md`/`PROCESSING.md` from each report repo and writes into the site worktree's `reports/` (`runAggregate` in `scripts/ingest/cli.ts`).
- The shared-checkout guard (m7ga) was written for `run`/`baseline`, which do write into report repos. Its refusal message for `aggregate` ("a run here dirties it for everyone") is false.
- AGENTS.md calls `--shared` "the integrator's deliberate opt-in only", which leaves a site-only agent no documented way to meet the done condition.
- The real risk runs the other way: copying a peer's uncommitted `full.md` into your worktree and committing it. So check `git status --short reports/` before committing.

Filed **reportsthatmatter-fzdo** (P2, specced): `aggregate` reads shared checkouts without the flag but skips dirty or off-main ones; the docs (done condition, Shared checkouts, R6) say so. The fallback is a docs-only change. Until it lands, site-only agents should keep using `VERIFY_SHARED=1` and check `reports/` is clean.

## What moves

No report text, no ids, no pin bump, no D1. Site assets change: 19 card PNGs deleted or re-rendered by #327 and 10 more deleted by the fixup; `src/generated/cards.ts`; templates and CSS (#326). These need a **deploy** (cards and templates are app assets). Queue: 64 items, Challenger first on the placeholder 2026-10-20.

## Integrator steps

Run from a fresh integrator worktree on `main` (or the shared checkout per `integrate-and-ship`). The heads to merge are in the Verdicts table; if a head has moved, re-review the new commits first.

0. **Across batches:** ship batch 1 first (it holds the P0 launch gate), then batch 2 by its own steps (`~/src/reportsthatmatter/factory/reviews/review-batch2-2026-10-16.md`). Its reviewer found no cross-batch conflicts: #321 touches other lines of `agent-protocol.md` and `fix-agent/SKILL.md` than batch 2 does. Batch 2's #328 makes `verify.sh` fail on a stale `src/generated/editorial.ts`. Batch 1's editorial edits change only `card:`, and `pnpm editorial` on the batch 1 trial leaves `editorial.ts` unchanged, so the order between the batches does not matter for that file.
1. Mark the drafts ready: `gh pr ready 322 326`, then `gh pr ready 3 -R reportsthatmatter/marketing`.
2. Merge in this order, squash, waiting for CI on each:
   - `gh pr merge 327 --squash`
   - `gh pr merge 321 --squash`
   - `gh pr merge 322 --squash`
   - `gh pr merge 326 --squash`

   If #326's CI fails on `tests/decisions.test.ts` (something else took 0022 meanwhile), renumber on its branch to `pnpm decisions next` and fix the title. Then `gh pr merge 3 -R reportsthatmatter/marketing --squash`.
3. `git pull`, `pnpm bootstrap`, then `pnpm editorial && pnpm cards && pnpm posts --check`. Expect no diff from `pnpm cards` and "Queue check passed".
4. Run `VERIFY_SHARED=1 ./scripts/verify.sh 2>&1 | tee ~/src/reportsthatmatter/factory/logs/<date>/rtm-integrate-batch1-verify.log`; it must exit 0.
5. Deploy: `./scripts/deploy-cloudflare.sh`. Then `pnpm editorial` and `pnpm seed-highlights --remote`, which should write 0 rows (only `card:` changed). Then `VERIFY_BASE=https://reportsthatmatter.org ./scripts/verify.sh`. Spot-check `/about`, `/press` and `/reports/challenger-accident` for the footer line, and `/assets/cards/uk-post-office-horizon-inquiry/default.png` for no wordmark.
6. **Before `POSTER_LIVE`** (launch day only, not part of this ship):
   - Re-date from the real launch date with `pnpm posts --start <YYYY-MM-DD>`, then `pnpm posts --check`, and commit `marketing/queue.yaml` in a PR.
   - Confirm the first item is the Challenger "Committee found NASA's drive" excerpt on that date.
   - Do not set `POSTER_LIVE` while 63cd is open unless Rufus says to launch with the current queue (findings 6 and 7).
7. Close on ship:
   - `bd close reportsthatmatter-jcht` (decision 0021 decided; re74 is already closed).
   - `bd close reportsthatmatter-ay85`, with a note that the tier test moved to zh1w.
   - `bd close reportsthatmatter-y2t.7`.
   - `bd close reportsthatmatter-b5td`.
   - Leave y2t.8 open for the email half (decision 3d41, record 0022).
   - Leave 3d41, 63cd, 80qf, nm83, zh1w and fzdo open.

   Then run `bd dolt push`.

## Beads

- Filed: **reportsthatmatter-zh1w** (P1, stream:marketing, level:specced, discovered-from ay85): Tier C check in `pnpm editorial`.
- Filed: **reportsthatmatter-fzdo** (P2, stream:platform, level:specced): verify/aggregate for agents that own no report repo.
- Notes appended: ay85, y2t.7, y2t.8, b5td, jcht, 3d41 (description now says 0022), 63cd (CIA report on the affected list; Tier B items queued), 80qf (Jack Smith, Jan 6th and Chilcot cadence).

## Lessons

- A queue clean-up scoped to "the items listed" left eight more items of the same class that were hidden by unrelated skips (length, page number). When removing a class, audit the inputs (`card: true`), not the output (the queue).
- Count-free copy needs a pattern for "N of them" as well as "N reports". The launch thread's own count survived the first pass.

(Not added as lesson files: the reviewer does not own a site branch these fit; the integrator may add them with `pnpm lessons new` citing zh1w.)

## Getting better faster

- **What slowed me:**
  - Finding the tier problem needed a hand-written script joining editorial highlights, the strategy's word list and the skipped-candidates list. No tool shows "card highlights in affected reports, with tier signals".
  - Grapheme notes in plan docs are hand-maintained and were already wrong on main (263 vs 308).
  - A shell slip cost one re-push: `git mv`, then `sed`, then commit without `-a`. The decisions test caught it on the trial.
- **What would have caught it earlier:**
  - zh1w (Tier C lint in `pnpm editorial`) would have flagged all ten on the #327 branch.
  - `check-drafts.py` could also recompute `*(N graphemes)*` notes with the site's `graphemeLength` and fail on a post over 300.
- **One proposal:** have `pnpm posts` print, for each affected-community report, every `card: true` highlight with its skip reason. A highlight kept out only by a length or page skip is a latent queue item and should be visible in the PR that touches the queue.
