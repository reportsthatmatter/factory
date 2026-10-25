# Review: ICA 2017 report (site #365, report repo #1) and the CIA hero photograph (site #364), 2026-10-25

Reviewer: Sonnet agent. I did not touch production: no `wrangler d1`, no publish, no seed, no reindex, no deploy, no merge, no release, no `pkill`. The D1 figure below is reasoned from the last ship, not read from production. My only process was a local `wrangler dev` on port 8893, stopped by pid.

**Verdict: MERGE all three, in the order report repo #1, site #365, site #364.** Two fixups pushed to #365 (2x cards that failed `tests/card-design.test.ts`; a ratchet) plus a rights note. Nothing blocks the merge. Publishing before 3 Nov additionally needs `pnpm posts` for five queue items (poster-live owns `marketing/queue.yaml`) and a Worker deploy.

## Scope

| PR | Branch | Head at start | Head after fixups |
|---|---|---|---|
| reportsthatmatter/us-ica-2017-russia-elections#1 | `first-ingest-1024` | 55fcbbda | 55fcbbda (unchanged) |
| site #365 | `ica2017-1024` | e3987e9f | **9aacddeb** |
| site #364 | `ciahero-1024` | b4464d61 | b4464d61 (unchanged) |

Base: origin/main 5e684489.

## Trial

- Worktrees: site `~/src/reportsthatmatter/rtm-review365-1025` (branch `trial365-1025`), report repos `~/src/reportsthatmatter/rtm-review365-1025-reports` (ICA checked out at `origin/first-ingest-1024`, then `CI=true pnpm install` there: without it the preflight says "@rtm/ingest is not installed"), fixup worktree `rtm-review365-1025-fix` (detached at the PR branch). `RTM_REPORT_DIRS=/Users/rgrp/src/reportsthatmatter/rtm-review365-1025-reports`.
- Merged #365 then #364 onto 5e684489 with `--no-ff`: no conflicts. `src/generated/editorial.ts` auto-merged and `pnpm editorial` keeps it unchanged (19 files, ICA and the CIA report both approved).
- `pnpm typecheck` pass. `pnpm quality check` all 19 within budget. `pnpm corpus check` 19 unchanged. `pnpm aliases check` pass. `pnpm editorial` pass.
- `pnpm test`: first run 2 failures (cards 2400x1260, queue). After the fixups: 1349 pass, 1 fails: `tests/cards.test.ts` "pnpm posts --verify": `marketing/queue.yaml` is out of date (5 new items, 0 stale). Expected; see integrator step 4.
- `VERIFY_SHARED=1 ./scripts/verify.sh` with the report worktree: everything passes except that one vitest. Log: `~/src/reportsthatmatter/factory/logs/2026-10-25/rtm-review365-1025-verify.log`. `git status --short reports/` empty.
- `pnpm cards --check` before the fix: only the six ICA cards differed. After `pnpm cards`: only those six changed, `src/generated/cards.ts` unchanged.

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| ICA report repo #1 | MERGE (first) | Same `full.md` as the site copy (cmp identical), fidelity and golden pages pass in verify, pinned v0.28.0, no ingest release needed. |
| site #365 | MERGE after #1, at head 9aacddeb | Landing page, quotes and facts check out; fixups in. Publish needs the queue regeneration and deploy (steps 4-6). |
| site #364 | MERGE | Rights are clean, alt text accurate, hero renders at desktop and 390px. Needs a Worker deploy (heroes are app assets). |

## Findings, by severity

1. **Fixed (commit 892bd635).** The five quote cards and the default card for the ICA were committed at 2400x1260 (the old 2x render); main's `scripts/cards.mjs` renders 1200x630 under 300 KB since #fdcfc11b. `tests/card-design.test.ts` failed and `pnpm cards --check` said all six were stale. Regenerated with `pnpm cards` (41-52 KB each). Plate left, quote right, as the Mueller cards.
2. **Fixed (commit c3c27f01).** Ratchet moved: `pnpm quality ratchet --dry-run` would lower `us-v-philip-morris` chapter-intro-misfiled 1 to 0 (the signal fix in this PR). Committed, so ship's `committed` gate is not tripped.
3. **Fixed (commit 9aacddeb), rights, not blocking.** `sources.yaml` calls the CIA, FBI and NSA seals "public domain". Copyright is fine, but statutes (18 U.S.C. 701 and 1017 for the FBI, 50 U.S.C. 3515 for the CIA) restrict seal use that implies agency approval. Here the seals show the report's own authors, on a page that says whose words they are, so I kept the plate and recorded the statutes and the fallback (NIC seal, PDF p.4). Not legal advice; a bead is not filed. If Rufus wants the plate gone, swap before the Tue 27 Oct launch.
4. **Not fixed, expected.** `marketing/queue.yaml` lacks the five ICA items (see the list below). The PR leaves it to poster-live; I ran `pnpm posts` in the trial only and reverted. `pnpm posts` also prints the known over-300-grapheme quotes of other reports (Horizon, Lehman, PSI), not new.
5. **Source check, partial.** The Senate Intelligence Committee press page returned 403 to every fetch, so "analysts were under no political pressure to reach specific conclusions" and "coherent and well-constructed intelligence basis" are confirmed only from Fox News' quotation of the committee, not the primary page. The editorial header says it quoted the primary page read via the Wayback Machine; I could not reproduce that. The CIA page was read directly: 2 July 2025, "The tradecraft review identified multiple procedural anomalies in the preparation of the 2016 ICA" (the page does not mention "aspired", as the file header says). Low risk; an integrator with a browser can read both in a minute.

## Product

- ICA landing page (`pnpm dev`, desktop, full page): plate, standfirst, background, seven findings with verbatim excerpts, reading guide, contents. Meta description and og:image (the new 1200x630 default card) are right. The Key Judgments excerpts are quoted from the p.1 copies so they label correctly (aflq).
- Editorial quotations are checked verbatim by `pnpm editorial` (passes). Findings 2 ("the only judgment ... where the agencies' confidence is stated separately") agrees with `full.md`: the NSA difference appears only in the aspired-to-help judgment (Key Judgments and p.1).
- Background facts: 29 December 2016 information cut-off, 6 January 2017 release, Obama's December 2016 order, the 2020 Senate and 2025 CIA reviews are each sourced in the file header; the CIA 2025 date and phrase confirmed above.
- CIA hero (#364): Hart Senate Office Building, Architect of the Capitol, 12 Dec 2011; Commons PD-USGov-Congress-AOC, FlickreviewR passed, Flickr page "United States government work"; credit line "Photo: Architect of the Capitol, public domain" is on the page, as the AOC asks; the AOC logo in the corner is cropped out. Alt text: "The Hart Senate Office Building in Washington, a white marble block with a grid of tall windows, seen from across the street. It houses the Senate Select Committee on Intelligence." Accurate, plain, no detainee imagery. Renders at 1280 and 390 px with the caption under the image.

## The card:true excerpts in editorial/us-ica-2017-russia-elections.yaml (five highlights, one card each)

1. `we-also-assess-putin-russian-2`: "CIA and FBI have high confidence in this judgment; NSA has moderate confidence." (the card quotes the p.1 copy)
2. `high-confidence-generally-indicates-judgments`: "High confidence in a judgment does not imply that the assessment is a fact or a certainty; such judgments might be wrong."
3. `dhs-assesses-types-systems-we`: "DHS assesses that the types of systems we observed Russian actors targeting or compromising are not involved in vote tallying."
4. `russia-collected-some-republican-affiliated-targets`: "Russia collected on some Republican-affiliated targets but did not conduct a comparable disclosure campaign."
5. `we-assess-moscow-will-apply-2`: "We assess Moscow will apply lessons learned from its campaign aimed at the US presidential election to future influence efforts in the United States and worldwide, including against US allies and their election processes."

The other six highlights (no card) are: we-did-not-make-assessment, we-assess-high-confidence-gru, we-assess-russian-intelligence-services, report-declassified-version-highly-classified, when-intelligence-community-analysts-use, simonyan-claimed-popular-arts-magazine. The five queue items `pnpm posts` would add use exactly the five above; the first is a highlight that spans two sentences' worth of context (`h=agree with this judgment. |CIA and FBI ...|`), which is how the Challenger items are built too.

## What moves

New report only: `us-ica-2017-russia-elections` (19th report in the trial; 18 live today). No other report's text, ids or cards move. The signal fix lowers one budget (us-v-philip-morris, 1 to 0). The CIA report moves only its editorial (hero), image files, and `src/generated/editorial.ts`.

## Ship rehearsal and D1 estimate

- `pnpm ship --plan --state build/review-ship-state.json`: plans cleanly, 22 steps (steps 17-21 are the production ones).
- `pnpm publish-report us-ica-2017-russia-elections --dry-run --offline`: 11 objects, 0.2 MB, content version acce7caef028a9a5; the offline estimate prints "1" row write because it cannot read D1 (lessons: do not trust that number).
- D1 writes reasoned from v0.22.0 (5.5k writes for 13 reports, about 100 to 400 per report): about 143 paragraphs times a few index rows, plus 9 sections, 1 version row and 5 to 11 seeded highlights at about 3 rows each: roughly 0.6k to 1k rows, under 1% of a day on the old free tier. Reads: 4,000 for verify-prod plus the reindex read. Not measured: `ship` step 16 does it properly.

## Integrator steps

Merge order: report repo #1, then site #365, then site #364. No conflicts expected; if `src/generated/editorial.ts` conflicts after one of the site PRs, take main's and run `pnpm prerender && pnpm editorial`.

1. `gh pr merge 1 -R reportsthatmatter/us-ica-2017-russia-elections --squash` (or the repo's usual merge), then `git -C ~/src/reportsthatmatter/us-ica-2017-russia-elections pull --ff-only` (the manifest reads `../us-ica-2017-russia-elections`).
2. Confirm #365 is at head 9aacddeb: `gh pr view 365 --json headRefOid`. Merge #365, then #364 (`gh pr merge 365 --squash`, `gh pr merge 364 --squash`).
3. In a fresh site worktree from main: `pnpm bootstrap`; `pnpm prerender && pnpm editorial`; `git status --short` must be empty.
4. With poster-live (owner of `marketing/queue.yaml`): `pnpm posts`, commit the five new ICA items in a PR, merge. Until then `tests/cards.test.ts` "pnpm posts --verify" fails on main; `pnpm ship`'s verify step will stop on it.
5. `pnpm ship --plan --state build/ship/state.json`, then `pnpm ship` for the PROD steps. Equivalent by hand: `RTM_PUBLISH_SECRET=$(cat ~/.rtm-publish-secret) pnpm publish-report us-ica-2017-russia-elections --base https://reportsthatmatter.org --yes` after the deploy (a new report needs the Worker deployed first), `./scripts/deploy-cloudflare.sh`, `pnpm seed-highlights --remote --yes`.
6. Verify: `curl -sD- -o /dev/null https://reportsthatmatter.org/reports/us-ica-2017-russia-elections/full | grep -i x-rtm-content-version` must be a hash (expect acce7caef028a9a5 before any later change); `pnpm publish-report --all --status --fail-on-drift --base https://reportsthatmatter.org` empty; open the CIA report to see the hero.
7. Close beads on ship: `reportsthatmatter-gqsy.13` (ICA), `reportsthatmatter-njr9` (CIA hero). `reportsthatmatter-gqsy.13.1` (ICA hero image) stays open: the plate is the seals, not a hero. Leave `aflq` (page label bug) and `l40o` (ingest#82, optional) open.
8. Optionally, before launch, read the two Senate Committee quotations on the primary page (finding 5).

## Beads

None filed. `aflq` and `l40o` noted open.

## Lessons

- A card committed from an older `scripts/cards.mjs` fails `pnpm cards --check` and the size test only after main moves: regenerate cards on the merged trial, not on the branch.
- A report's PROCESSING and editorial can be right while a derived asset is stale; `pnpm cards --check` is the one command that said so.

## Getting better faster

- Slowed by: the site worktree path (a relative path with `git -C` created it inside the repo; I removed and recreated it), and 403s on the Senate site.
- Would have caught it earlier: `pnpm cards --check` in the PR author's pre-push list.
- Proposal: `pnpm ingest worktrees` should check out the report repo's PR branch when asked (`--branch`), and run `pnpm install` in it.
