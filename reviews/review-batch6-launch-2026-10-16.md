# Review: batch 6, the launch gate (site #345, #346, #348, #349, with #347; marketing #4), 2026-10-16

Reviewer: Claude Opus 5.5 agent. I did not touch production: no merge, deploy, publish, reindex, seed, `--remote` D1 call, `wrangler` against either account, `pnpm ship` beyond `--plan`, no `POSTER_LIVE`, no workflow dispatch, no post. Production contact: anonymous GETs of the first twelve queue links and of `/reports/<id>/processing`, plus read-only `gh run list`, `gh secret list` and `gh variable list`.

**Verdict: MERGE all five site PRs and marketing #4, in the order below, with `--merge`; deploy afterwards (Worker templates and assets changed). Launch can go ahead on Tue 20 Oct if Rufus adds the two Bluesky secrets and starts the launch-day poster run by hand at 14:00 UTC.**

## Scope

| PR | Branch | Head at start | Head reviewed (after fixups) |
| --- | --- | --- | --- |
| site #345 decisions clerk | `decisions-1016` | eaa0904c | 12c79a1a |
| site #346 launch dates in docs | `launch-dates-1020` | 074f8c57 | 975c73c1 |
| site #348 queue rules | `launchqueue-1016` | 5996fd8b | 78f7ad72 |
| site #349 source line, contact, /about | `hbnj-source-line-contact` | ac836954 | 7cf0db6a |
| site #347 licences (reviewed by another agent; trial-merged here) | `licence-1016` | 0edb5449 | e641857a (not mine; that reviewer's fixups) |
| marketing #4 launch drafts | `launch-1020` | 01079810 | 2936132 |

## Trial

- Worktree `~/src/reportsthatmatter/rtm-reviewbatch6-1016`. The final branch `trialbatch6final-1016` is `origin/main` (1678807c) plus `git merge --no-ff` of 345, 346, 348, 349, 347 at the heads above. No conflicts. (At the start heads, #346 and #348 conflicted on one line of `docs/poster.md`; fixed on both branches, see below.)
- Final checks (logs `factory/logs/2026-10-16/rtm-reviewbatch6-1016-final-*.log`): `pnpm prerender` 0; `pnpm typecheck` 0; `pnpm test` 1273 passed, 1 skipped; `pnpm editorial --check` 0; `pnpm posts --check` 0; `pnpm decisions check` numbering ok; `pnpm ship --plan --state build/review-ship-state.json` 0 (its only guard problem is "no --shared", expected in a worktree); `VERIFY_SHARED=1 ./scripts/verify.sh` "All checks passed".
- Earlier passes: the four PRs without #347, at the start heads and after the first fixups (`-verify.log`, `-verify2.log`); with #347 (`-347-*.log`). All green.
- `git status --short reports/` after verify shows `uk-hillsborough-panel/PROCESSING.md` and `us-lehman-examiner/PROCESSING.md` modified. That is a finding (below), not peer noise.
- Fixup worktrees: `rtm-reviewbatch6fix-1016` (345, 346, 349), `rtm-reviewbatch6q-1016` (348), `marketing-reviewbatch6-1016`.

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| #345 | MERGE | Every record states the accepted default as its bead and the 10 Oct / 16 Oct handoffs give it. 0017 now records the shipped wording; 0024 takes in #347's draft record. 0018 is flagged for Rufus (below). |
| #346 | MERGE | Dates right. Its poster.md switch-on line moved into #348 so the two no longer conflict. |
| #348 | MERGE | Rules are in code with tests; queue obeys them. Fixed: a post on 23 Oct (kept-clear date) and a poster that could post an overdue item on a kept-clear day. First ten posts checked by eye. |
| #349 | MERGE | Source line and contact route work at 375 and 1280 with no overflow. Two /about#ai claims overstated the code; fixed. |
| #347 | MERGE (per its own review) | Trial-merges clean on top of the other four at e641857a; checks green. Its duplicate 0024 was already dropped (16d46c9c); its substance is now in #345's 0024. Needs the Hillsborough and Lehman report-repo licence PRs merged before it (below). |
| marketing #4 | MERGE | First automated post corrected to p. 146; three posts over their platform limits fixed; launch-day poster timing fixed. `check-drafts.py` exits 0. |

## Findings, by severity

### Fixed on the branches

1. **#348: a post on Fri 23 Oct, a date the strategy keeps clear** ("Dates we keep clear (nothing from us: no post…): 23 Oct", Soldier F). Litvinenko p. 246 was scheduled that day. Added `KEPT_CLEAR_DAYS` (23 Oct, 23 Nov, 6/28/30 Jan) to `isBlackout`, so scheduling skips them and `--check` fails an item on one; test added. Re-dated with the PR's own `pnpm posts --drop-stale --start 2026-10-20`: items from 23 Oct on move one day later (no text changes), and the queue now ends 18 Nov. f818b5c4.
2. **#348: the poster could still post on a kept-clear day.** `selectNext` posts overdue items, so a failed 22 Oct run would have meant a post on 23 Oct. It now returns "none" on a kept-clear date; test added. 6c299d2e.
3. **Launch-day timing (#348 poster.md, marketing checklist).** The "14:00 UTC" scheduled run has started between 17:43 and 21:53 UTC on every day from 4 to 16 Oct (`gh run list --workflow post-next.yml`). Left alone, the first excerpt lands in the evening, after the Show HN and the 14:15 repost. Both docs now say to start it by hand at 14:00 with `gh workflow run post-next.yml -R reportsthatmatter/reportsthatmatter -f dry_run=false`. With `dry_run=false` the workflow follows `POSTER_LIVE`, and the late scheduled run then sees "already-today" and does nothing. 78f7ad72, marketing 2936132. Lasting fix: bead gweq.
4. **#349 /about#ai overclaimed twice.** (a) "For some scanned reports, a vision model's reading … used only where its reading agrees word for word". Only `challenger-accident/ingest.ts` declares `visionStructure`, and in its default lenient setting a block is accepted when 75% of the model's words are in the text layer, with runs of up to 8 unmatched (`ingest/src/vision/hybrid.ts:214`, `verify.ts`). The words used are always the layer's, which the note keeps. Now: "For one scanned report, the Challenger report, … used only where its reading closely matches the document's own text". (b) "every quotation in it is checked automatically". `pnpm editorial` checks the `quote:` excerpts, but the background and finding prose carries inline quotations it does not check (Columbia's "normalization of deviance"; Philip Morris's "A Frank Statement to Cigarette Smokers", which is not from the report). Now: "every excerpt it shows from the report is checked". The other claims hold. The text is extracted by deterministic code. No model writes or rewrites report text: vision output is used for structure only. The code is public. Introductions are AI-assisted and labelled. 7cf0db6a.
5. **#345 0017** said "unedited"; the shipped copy (#349) says "as published", which is accurate because the pipeline repairs layout and OCR slips. Amended to record the shipped wording and the reason. **0009** carried "Published blog posts keep their wording as dated posts". That sentence belongs to the "thirteen" ruling, already in 0029, so I removed it. dc0f435f.
6. **#345 0024 vs #347's 0024.** #347 had already dropped its record (16d46c9c). I folded its substance into #345's 0024: the options, what the licence PRs do, Hillsborough and Lehman marked not confirmed, Leveson's README fix. #345 keeps 0024, and #347 needs no record. bf3c8296; aligned with the licence review's text (beads 9kn4, hx7j, Saville note) in 12c79a1a.
7. **#346/#348 conflict** on the poster.md switch-on line. #348's line now carries both wordings: before 14:00 on 20 Oct, first item Challenger p. 146, `posts --check`, the checklist pointer. #346 no longer edits that line. 975c73c1, 417275f9.
8. **Marketing #4:**
   - The checklist and the thread said the first automated post is "Challenger p. 3"; it is p. 146 (#348's `OPENING_AVOID`). The Wed 21 row still warned of a Jack Smith item (now Challenger p. 3), and the Fri 23 row said "the poster still runs" (it now posts nothing). The gate check demanded a "contact email" that does not exist; it now asks for the route (`/about#contact`).
   - Thread post 5/5 had a "[email on the site]" placeholder; it now points to `reportsthatmatter.org/about#contact` and says "within a day".
   - Thread post 2/5 left out the CIA study and Mueller (18 reports are live) and broke its own date order (Grenfell 2024 before January 6th 2022). Fixed, at 294 graphemes.
   - Org bio: 261 graphemes, over Bluesky's 256. Now 244.
   - X post: 292 characters, over 280. Now 274.
   - The r/datasets title stated "16 … reports"; the count is removed. `check-drafts.py` missed it: bead y8t6.
   - The repo has no Actions secrets yet; the checklist says so.
   - Commits 90e6238, 2936132.

### Not fixed (beads or Rufus)

- **The removal route is GitHub-only** (#349): a public issue that needs an account. It meets 0028 ("a contact and removal route"), but for a bereaved family a public GitHub form is a barrier. An email address (needs-user bead fh6v; Cloudflare Email Routing on the zone) should exist before the prelaunch notes. For Rufus.
- **Hillsborough (12 Nov) and Grenfell (14 Nov) are queued, but the campaign-group review emails have not been sent.** Strategy §8: one email per group "before any affected-community report enters the queue". Drafting is bead vx0u. Send them by about 5 Nov or drop those two items. For Rufus.
- **Litvinenko**: three posts in the first 11 days ("operation to kill", Putin's "not Lazarus", "responsible for … death"), with the hotel-plan plate. It is not flagged `affected_community`; the strategy calendar uses Litvinenko for 1 Nov, and 3g1h proposes adding it. If it were flagged, all three would be Tier C. Left to Rufus (3g1h).
- **Columbia card "History is cause"**, used as an image in Rufus's personal post, org thread post 4 and the Wed 21 image post. Columbia is on the affected list, and the sentence's subject is "History", not an institution: Tier B by the letter of §8 (no card). The draft argues for it (23 years past, a teaching text). This sits in a wider tension between 0026 ("no report about deaths … leads, demos") and 0027 (Challenger leads; Challenger and Columbia are the demo pair), both accepted. For Rufus.
- **Mueller**: the "Original document" link is Volume I's PDF on Volume II pages (one `source_url`; this predates #349, which only exposes it). Bead ba23.
- **Hillsborough/Lehman `PROCESSING.md` revert.** `verify.sh`'s aggregate copies each report repo's PROCESSING.md into the site. Until report PRs uk-hillsborough-panel#13 and us-lehman-examiner#12 merge and the shared checkouts are pulled, it rewrites #347's site copies back to "Crown copyright … OGL" and "no copyright restricts it". Integrator step 2 handles it.
- **Cosmetic**: on a card whose quote opens with its own straight quotes, the outer curly quotes are dropped (queue items 7, 8, 10). Item 1's page reads "l n" above the paragraph, a pre-existing text artefact.
- **Minor, 0027**: "Leveson and Chilcot lead UK material" alongside "Chilcot only on its own news days". The record copies the bead, and #348 treats Chilcot as news-day-only. Worth a line from Rufus.

### Tier A marks (#348) against strategy §8

The 11 marks: Challenger ×2, Grenfell ×2, Hillsborough, Post Office ×2, 9/11 ×2, Deepwater, CIA. I would mark two differently, and neither is queued (both are over 300 graphemes with the context line), so the queue does not change:

- **Grenfell 2-19** ("One very significant reason why Grenfell Tower came to be clad … was systematic dishonesty on the part of those who made and sold…"): the grammatical subject is "reason", not an institution. **B**.
- **Post Office 6-205** ("delivery of financial redress to many claimants … has not been prompt"): the subject is the redress to affected people, not an institution's act. **B**.
- **Hillsborough 24** ("On opening Gate C there was no instruction given to the SYP officers…"): passive, with no institutional subject. Defensible as A (what SYP failed to do), and it stands alone. Keep it, but it is the most sensitive item in the queue and waits on the families' groups email (above).

The rest are clean A: the institution is the subject, the verb is what it did or knew, and the sentence stands alone.

### First ten scheduled posts (after the re-date)

20 Oct Challenger p. 146 (Feynman concurrence); 21 Challenger p. 3; 22 Senate PSI p. 330 (Lippmann "dupe someone"); 23 nothing; 24 Litvinenko p. 246; 25 Leveson p. 1781; 26 Philip Morris p. 55; 27 Senate PSI p. 297 ("house of cards"); 28 Litvinenko p. 52; 29 Leveson p. 1515; 30 Philip Morris p. 1537.

Every link resolves on production, through the `src=` 302, to the right section page. The `?p=` paragraph exists, the quote is in it, and the `h=` exact text equals the quote (script and output in the session scratchpad). Every card exists at 2400×1260 and under 170 KB (Bluesky's blob limit is about 1 MB), shows the right page, and carries no inquiry logo or picture of a place, except the Litvinenko hotel plan (above). Post text is the quote, then the source line, then the context line where the registry has one.

## Found only by the trial merge

- The #346/#348 one-line conflict in `docs/poster.md`.
- The PROCESSING.md revert by `aggregate` once #347 is in.

## Integrator steps

1. `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only`; check each head first with `gh pr view <n> --json headRefOid,mergeable`. Expect MERGEABLE at 345 12c79a1a, 346 975c73c1, 348 78f7ad72, 349 7cf0db6a, 347 e641857a. A newer head needs a reviewer's word.
2. Report repos first (this is what #347's reviewer sends; at minimum these two, or verify reverts #347): `gh pr merge 13 -R reportsthatmatter/uk-hillsborough-panel --merge`, `gh pr merge 12 -R reportsthatmatter/us-lehman-examiner --merge`, then `git -C ~/src/reportsthatmatter/<repo> pull --ff-only` for both. Follow the licence review for the other report-repo PRs.
3. Site, in this order, each with a merge commit (`--match-head-commit` pins the reviewed head):
   - `gh pr merge 345 --merge --match-head-commit 12c79a1a2f694bb17d53c061cf475aa3517c9e9d`
   - `gh pr merge 346 --merge --match-head-commit 975c73c1830e0d28872203d2094b65f9a8cf5593`
   - `gh pr merge 348 --merge --match-head-commit 78f7ad72132fe38be1a9a471cadf25268d4db600`
   - `gh pr merge 349 --merge --match-head-commit 7cf0db6a3fbd3e32aff573ed423d21a580ac9f96`
   - `gh pr merge 347 --merge --match-head-commit e641857acbb98a0a4a68c90456a6a021e4f02397`

   Before each merge, re-run `gh pr view <n> --json mergeable` and wait while it says UNKNOWN, since GitHub recomputes after every merge. No conflicts are expected (simulated). Method: these branches carry no resolution merges and are each based on 1678807c, so `--squash` would also apply cleanly. I recommend `--merge` to match main's recent history and keep the reviewed heads reachable.
4. Marketing: `gh pr merge 4 -R reportsthatmatter/marketing --merge` (head 2936132).
5. In the site: `git pull --ff-only`, then `VERIFY_SHARED=1 ./scripts/verify.sh`; `git status --short reports/` must be empty.
6. **Deploy** (Worker templates `about.ts`, `layout.ts`, `report.ts`, `section.ts`, `site.ts`, `src/index.ts`, and the assets `highlight.js`, `passage-panel.js`, `styles.css` changed): `./scripts/deploy-cloudflare.sh` (Datopian profile; it pre-renders first). The drift table must list only these files. No pin bump, ingest release, republish, reindex, seed or D1 migration: no report text, ids or highlight rows move (`tier:` is not seeded). With `pnpm ship`, `--plan` must show the same.
7. Production check: `VERIFY_SHARED=1 VERIFY_BASE=https://reportsthatmatter.org ./scripts/verify.sh`. By eye: `/about#ai`, `/about#contact`, the footer "Contact", and `https://reportsthatmatter.org/reports/challenger-accident?p=committee-concurs-dr-feynman-s` (a "Linked paragraph" panel with "Original document ↗ · Corrections and removal").
8. Create the label the removal template applies: `gh label create removal-request -R reportsthatmatter/reportsthatmatter`. Someone must watch those issues daily for the within-a-day promise.
9. Close on ship (`bd close … --reason "Shipped in site batch 6 (review-batch6-launch-2026-10-16.md)"`): reportsthatmatter-hbnj, -0ino, -pbkk, -63cd, -80qf, -y2y8.9 (with #347's reviewer), -y2t.6 and -y2y8.7 (if #346 and marketing #4 complete them). Leave open: pp7b (runway), 5k36 (homepage order), 3g1h, vx0u, fh6v, gweq, ba23, y8t6. `bd dolt push`.

## Poster go-live (after step 7; Rufus only, nothing here is for an agent to run live)

From #348's report and `docs/poster.md`. The workflow input is `dry_run` (boolean, default true) in `.github/workflows/post-next.yml`.

1. By Sat 17: Rufus adds the secrets. On 16 Oct the repo had none: `gh secret set BLUESKY_HANDLE -R reportsthatmatter/reportsthatmatter --body reportsthatmatter.org` and `gh secret set BLUESKY_APP_PASSWORD -R reportsthatmatter/reportsthatmatter` (paste the app password).
2. Mon 19 (agent may run, dry run only): `POSTER_TODAY=2026-10-20 pnpm post-next` locally, which must show the Challenger p. 146 item. Then `gh workflow run post-next.yml -R reportsthatmatter/reportsthatmatter -f dry_run=true` and `gh run watch`: green, logs in, "DRY RUN: would post challenger-accident:committee-concurs-dr-feynman-s…" (with secrets set it also reads the account).
3. Tue 20, before 14:00 UTC: `gh variable set POSTER_LIVE --body true -R reportsthatmatter/reportsthatmatter`.
4. Tue 20, 14:00 UTC: `gh workflow run post-next.yml -R reportsthatmatter/reportsthatmatter -f dry_run=false` (the scheduled run would start hours late). Check with `gh run list --workflow post-next.yml -L 1` and the `posted_url` commit on main.
5. Off switch: `gh variable set POSTER_LIVE --body false -R reportsthatmatter/reportsthatmatter`.
6. Later days post when GitHub starts the run (usually 18:00 to 22:00 UTC) unless someone dispatches it by hand; gweq fixes this. 23 Oct posts nothing.

## Questions for Rufus

1. **0018** (phone header nav): the record gave no recommendation; the clerk chose option 2 (three links on phones, the rest in the footer) and wrote it as "accepted: the recommended default". Confirm or pick option 1 (a menu button).
2. **Removal by email**: a GitHub-only removal route meets 0028, but an address (fh6v) before the prelaunch notes would make the promise usable by the people it is for.
3. **Campaign-group emails** (vx0u) before Hillsborough (12 Nov) and Grenfell (14 Nov) post, or drop those items.
4. **Litvinenko** as affected-community (3g1h): three death-centred posts in the first 11 days.
5. **Columbia "History is cause" card** in the launch posts: Tier B by §8's letter.

## Beads

Filed:
- reportsthatmatter-gweq (P1, the scheduled run starts 4 to 8 hours late)
- reportsthatmatter-ba23 (Mueller Volume II source link)
- reportsthatmatter-y8t6 (check-drafts count regex)

Noted:
- pp7b (kept-clear days done)
- each PR's bead (verdict and this file)

## Lessons

- A schedule is a claim about time: read `gh run list` for the actual start times before writing "posts at 14:00" into a launch plan.
- A copy check against code ("what the AI did") needs the code's thresholds read, not its comments: "agrees" in a docstring was 75% in the default options.

## Getting better faster

- **What slowed me:** the poster.md conflict and the 0024 collision were both known before review, but surfaced only at trial merge; the launch drafts and the queue were written by different agents against different snapshots (p. 3 vs p. 146, Jack Smith day 2, "poster still runs").
- **What would have caught it earlier:** a `check-drafts.py` rule that reads `marketing/queue.yaml` from the site and checks any "first automated post"/"p. N at 14:00" claim in the checklist against the queue's first item; and a test that the cron's actual start times are within an hour (from `gh run list`), run by the morning check.
- **Proposal:** make `launch-day-checklist.md` generated in part: `pnpm posts --checklist` prints the first week's poster rows (date, report, page, kept-clear days) for the marketing repo to paste, so the two can never disagree.
