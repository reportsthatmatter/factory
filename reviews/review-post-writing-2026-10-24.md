# Review: site PR #363, post hooks (post-writing-1024), 24 Oct 2026

Reviewer: Claude Opus agent. I did not touch production: no merge, no Worker deploy, no live `post-next`, no Bluesky call, no D1, no R2. The only `post-next` run was a dry run with no credentials.

**Verdict: MERGE (before 13:40 UTC Sun 25 Oct). One docs fixup pushed; one follow-up bead for the 9/11 hook (posts 6 Nov).**

## Scope

- Site PR #363, branch `post-writing-1024`, head at start `789a1e58`, after fixup `994fba20`. Supersedes PR #362 (close it unmerged).
- Base: `origin/main` `2eca3b9b`; the branch already contains main (fast-forward merge possible, `mergeable: MERGEABLE`).

## Trial

Scaled down: tooling and queue only, no report text, ingest or editorial change, so steps 3 (corpus), 5 (ratchet), 6 (dev server) and 7 (ship rehearsal) do not apply. Checks run in `/Users/rgrp/src/reportsthatmatter/rtm-postwriting-1024` (bootstrapped, branch is main plus the PR).

| Check | Result |
| --- | --- |
| `pnpm posts --check` | pass ("Queue check passed"; the over-limit lines are skipped candidates, as on main) |
| `pnpm posts --verify` | pass, exit 0 ("marketing/queue.yaml is up to date") |
| `pnpm typecheck` | pass |
| `pnpm test` | pass: 84 files, 1335 passed, 1 skipped (`tests/posts.test.ts` green again) |
| `pnpm vitest run tests/skills.test.ts` | pass |
| `POSTER_TODAY=2026-10-25 pnpm post-next` (dry run) | selects `us-mueller-report:russian-government-interfered-2016-presidential:f8e0ffaa`, text is the hook + "— Mueller Report, p. 1", link and card unchanged |
| `--check` catches a hand-edited hook (text not rebuilt) | yes: "1 hooked item(s) whose text is not built from the hook", exit 1 |
| `--check` catches an over-300 hook | yes: "hook makes the post 374 graphemes", exit 1; old text kept |

Both negative tests were made on the worktree and reverted with `git checkout marketing/queue.yaml`.

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| #363 | MERGE | Poster-safe (no Worker change needed), checks green, 14 hooks fact-checked against the report text, posted item untouched. |
| #362 | close unmerged | Its test fix is in #363. |

## Safety of the poster (Worker and workflow)

- **Worker unchanged and compatible.** It reads `marketing/queue.yaml` from main and posts `item.text` (`src/lib/bluesky-client.ts` `publish`). The extra `hook` key is ignored by `PosterItem` consumers. `validateItem` checks only text length, link origin, `src=bsky` and card: all 14 hooked items pass (`--check` enforces ≤300 on build).
- **`cardMeta`** takes the last non-empty line and matches `^—\s*(.+?),\s*p\.\s*(\d+)\s*$`. "Challenger Accident Report (House Science Committee), p. 3" is covered by a new test; "United States v. Philip Morris (Kessler Opinion), p. 55", "Levin–Coburn Financial Crisis Report, p. 330" and "9/11 Commission Report, p. 21" match the same way (lazy group, only the final ", p. N" anchors). None of the 14 has a context line, so the source line is last for all of them. (Where a context line exists the card title falls back to "Reports that Matter"; that is main's behaviour too, not new.)
- **No double post.** `findPosted` matches on link or exact text. Links are unchanged by the PR and unique across the 27 queue items (`uniq -d` empty; the two Philip Morris p. 1537 items share a paragraph but differ in `h=`). The new texts match nothing already on the account. `selectNext`'s one-per-UTC-day rule is untouched.
- **Posted item byte-identical to main.** Lines 1-26 of `marketing/queue.yaml` (header plus Challenger p. 146 with `posted_url` and `posted_at: 2026-10-24T20:09:56.138Z`) diff clean against `origin/main`. `buildQueue` never rewrites a posted item (new test).
- **Workflow** runs `pnpm posts --check` before `pnpm post-next`, so a stale or over-long hook fails loudly and posts nothing.
- **Timing.** The Worker reads main via raw.githubusercontent.com (about 5 minutes' cache). Merging any time before about 13:30 UTC on 25 Oct is safe; if it misses, the Worker posts the old quote-form Mueller text, which is not wrong, only the old format.

## Scheduling

`queueViolations` passes in `--check`. Challenger p. 3 moves to 29 Oct (House Report 99-1016 letters of submittal and transmittal are dated "October 29, 1986" in the report; "Forty years ago today" is right), Leveson p. 1781 to 26 Oct, Mueller IRA to 30 Oct. `scheduled` dates are hand-moved, which `report-promote` says not to do; this is a deliberate, documented anniversary move and the rules still hold.

Not new, for Rufus's eye: four Mueller posts fall in the week before the 3 Nov midterms (25, 27, 30, 31 Oct; main had 25, 27, 29, 31). The hooks themselves are neutral (below).

## The 14 hooks: fact check

Every claim checked in `assets/generated/reports/<id>/full-body.html` (plain-text search), or in the report's `editorial/<id>.yaml` background where the report has nothing. None repeats its card quote or its key phrase.

| Date | Item | Claims and source | Notes |
| --- | --- | --- | --- |
| 25 Oct | Mueller p. 1 | DNC "announced in mid-June 2016 the Russian government's role in hacking its network, the GRU began disseminating stolen materials" (Vol I intro) | Supported. Neutral. |
| 26 Oct | Leveson p. 1781 | NotW closure (report, Part I ch. on Hunt/OFT); "168 years" from the editorial background (sourced there); statutory recognition of the self-regulator (the card's own paragraph) | Supported. |
| 27 Oct | Mueller p. 2 | 24 Op. O.L.C. 222 (2000); "this Office accepted OLC's legal conclusion" | Supported. Neutral: explains the mechanism, no verdict. |
| 28 Oct | PSI p. 330 | "the bank's top CDO trader, Greg Lippmann"; short position profit "$1.5 billion"; "lost nearly $4.5 billion from its mortgage related proprietary investments" | Supported. |
| 29 Oct | Challenger p. 3 | 73 seconds, crew of seven (editorial background); "NASA actually flew four Shuttle flights in fiscal 1984 and 8 in 1985"; report dated 29 Oct 1986 | Supported. States the deaths once, as the skill asks. |
| 30 Oct | Mueller IRA p. 4 | "In mid-2014, the IRA sent employees to the United States ... photos ... '[v]acant houses' with 'broken windows'"; "The IRA was based in St. Petersburg" | Supported. |
| 31 Oct | Mueller McGahn p. 4 | "On June 17, 2017, the President called McGahn at home and directed him ... the Special Counsel ... must be removed" | Supported. "The President", as the report says; the card's "Saturday Night Massacre" is not pre-empted. |
| 1 Nov | Philip Morris p. 55 | Plaza Hotel, 14 Dec 1953; Frank Statement 4 Jan 1954 in "448 newspapers"; "The 'Frank Statement' in its entirety stated as follows" | Supported. |
| 2 Nov | PSI p. 297 | "Analysts have determined that over 90% of the AAA ratings given to subprime RMBS securities originated in 2006 and 2007 were later downgraded ... to junk"; email chain "December 2006" (12/15/2006, S&P) | Supported, and attributed to analysts as the report does. |
| 3 Nov | Leveson p. 1515 | PCC "established in 1991" (to 2011: 20 years); voluntary membership (Northern and Shell "withdrew its membership in January 2011") | Supported. Falls on US election day; UK subject, no issue. |
| 4 Nov | Philip Morris p. 1537 | 1964 Surgeon General report "a Scientific Consensus that Smoking Causes Disease"; "an undated internal B&W document titled 'Smoking and Health Proposal'" | Supported ("undated" is the opinion's own word at p. 1537; elsewhere it places the memo "at the same time" as 1967, not contradicted). |
| 5 Nov | Leveson p. 1782 | Self-regulation recognised in law; backstop if the industry would not join (card paragraph) | Supported. |
| 6 Nov | 9/11 p. 21 | "F-15 fighters were scrambled at 8:46 from Otis Air Force Base. But NEADS did not know where to send the alert fighter aircraft"; American 11 hit at 8:46:40 | Supported, but see finding 1. |
| 7 Nov | Philip Morris p. 1537 | "an April 1968 memorandum to Earle Clements, President of the Tobacco Institute, William Kloepfer ... the industry's strategy of constant and consistent denial of smoking's harm was untenable"; "Vice President of Public Relations" | Supported. |

No partisan framing: the four Mueller hooks name no current politician, election or party, and use the report's own terms.

## Findings, by severity

1. **Minor, not fixed (bead `reportsthatmatter-gvyo`, due 6 Nov).** The 9/11 hook does not say how many died. `us-911-commission` is `affected_community` with no `context_line`, and the PR's own skill says such a hook states what happened and how many died, once (the report: "the largest loss of life—2,973—on its soil"). Editorial rewrite, not a mechanical fixup, and 13 days out, so left for a follow-up PR.
2. **Nit, not fixed.** The two Philip Morris p. 1537 hooks (4 and 7 Nov) both close on "the ruling quotes ... [memo/his reason]"; the skill asks for varied closings within ten days.
3. **Nit, fixed (`994fba20`).** The format blocks in `docs/posts-queue.md` and the skill showed the context line straight under the source line; `formatHooked` puts a blank line between them.

## Fixups pushed

- `994fba20` review fixup: show the blank line before the context line in the hooked-post format (posts-queue.md, post-writing skill). Docs only; no queue or code change.

## Integrator steps

1. `gh pr view 363 --json headRefOid` shows `994fba20...` (or later, if the author adds commits: re-run step 2).
2. In the PR worktree: `pnpm posts --check && pnpm posts --verify && pnpm test`.
3. `gh pr merge 363 --merge` before about 13:30 UTC on Sun 25 Oct 2026. No ship, deploy or Worker redeploy needed: the Worker reads the queue from main.
4. `gh pr close 362 --comment "Superseded by #363"`.
5. After 13:40 UTC: check the post on the account carries the hook text and the "Mueller Report" card title; the GitHub run records `posted_url` (it runs late; docs/poster.md).

## Beads

- Filed `reportsthatmatter-gvyo` (9/11 hook and the death toll; P2, `stream:marketing`, `level:judgement`). `bd dolt push` done.

## Lessons

- A long-red test on main (posts.test.ts since Challenger posted) hid nothing here, but it meant two PRs (#362, #363) carried the same fix. A rule that "assert the first open item" stops being true the day the first post goes out; tests on the live queue should assert on the rule's own scope (posted or not).
- `--check` drift detection compares against the committed `text`, so a hook edited by hand without `pnpm posts` fails before anything posts. Good design; worth keeping when the queue grows more hand-written fields.

## Getting better faster

- The fact-check took a plain-text dump of each `full-body.html` and a regex search per claim; a `pnpm posts --sources <id>` that prints each hook next to the card paragraph and its neighbours would halve it.
- The post-writing skill's affected-community rule was missed by its own author on one of 14 items; `--check` could warn when an affected report without `context_line` has a hook with no number of deaths in it (cheap heuristic: a digit followed by "died", "killed" or "dead").
