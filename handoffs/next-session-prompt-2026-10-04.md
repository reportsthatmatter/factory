# Next session: reportsthatmatter (written 2026-10-03, late)

This replaces `next-session-prompt-2026-10-03.md`, which is now a long running log. Read that only for history. Start in `~/src/reportsthatmatter/reportsthatmatter`:
1. `bd dolt pull`, then `bd ready`.
2. Read `AGENTS.md` ("Working conventions" and "Decisions and open questions").
3. Read this file.

## How to work (short)

- **Rufus does not review PRs.** Workers open PRs. A separate reviewer agent (Opus) trial-merges everything in scratch clones and writes `~/src/reportsthatmatter/review-vX.Y.Z-<date>.md`. One integrator agent (Sonnet) then merges and ships per that review. This is recorded in AGENTS.md. Today's three releases used this pattern; see the reviews `review-v0.2{0,1,2}.0-2026-10-03.md`.
- **Supervise and delegate.** Run 5–7 agents at a time. Use Sonnet for specced work and integrators, Opus for research, new heuristics and reviews, and Fable only for big design synthesis. Ask which variant Rufus means when a name is ambiguous (the TRC lesson).
- **Don't repeat design passes.** Check the 2026-10-02 docs and the 38s/b78 beads first. Quality-bar work is beads only (38s.16–38s.21) until Rufus asks.
- **Ship with `pnpm ship`** (first real use was v0.22.0; read its retro in `lessons.md`). Run `--plan` first, and type flags carefully, because unknown flags are ignored. D1 is on the free tier: 100k row writes a day, resetting at 00:00 UTC.
- **Protocol:** `~/src/reportsthatmatter/.agent-protocol-2026-10-02.md`. Use worktrees (`pnpm ingest worktrees <ids>`), never write into shared checkouts, and open PRs only; the integrator alone ships. Append to `docs/design/lessons.md`, and end every agent with a retro.
- **Questions of direction** go in `docs/decisions/` plus a bead labelled `decision`. Conventions go in AGENTS.md, not agent memory.

## If you are starting while 2026-10-03 agents may still be running

The old session ran two background agents at handoff: the v0.22.0 integrator, which waits for the 00:00 UTC D1 reset before running `pnpm ship --yes`, and the marketing agent. They live only as long as that session. Closing it kills them. Check what landed (see "v0.22.0 outcome" and direction 2), and finish whatever didn't.

## State at handoff

- **Live:** ingest v0.20.0 and v0.21.0 shipped 2026-10-03. v0.22.0: see "v0.22.0 outcome" below.
- **Live highlights:**
  - **HTML-sourced reports:** 9/11, Saville and Hillsborough are served from their official HTML (Hillsborough with PDF gap-fill).
  - **Fixed:**
    - n9em: chapter openings now sit under their own chapter.
    - Page markers are filled in on unmarked pages.
    - Litvinenko and Leveson footnote markers.
  - **New checks:**
    - an independent page-anchor check in verify
    - `pnpm marks check`; marks follow aliases
    - aliases generated with the old pin, and id-reuse detection
  - **Poster:** the scheduled Bluesky poster is merged but dry-run only. Bluesky is NOT ready; `y2t.1` is open.
- **v0.22.0 contents:**
  - the Challenger per-page vision hybrid
  - Deepwater and Columbia endnotes linked by chapter
  - page-break joins across footnotes and captions
  - Hillsborough typographic headings
  - the note-order signal
  - `pnpm ship`
  - adjudicated page breaks for 6 PDF-only reports
  - the 3ezs and sbnn fixes

### v0.22.0 outcome

**Not live yet.** Everything is merged and pushed: ingest v0.22.0 is tagged, all report and site PRs are merged, re-ingest, aliases, budgets and the editorial fix are done, and `verify.sh` passes. But D1's free-tier daily **read** limit ran out at about 18:50 UTC on 2026-10-03.
- **Effect:**
  - Search returned 404 and marks/highlights were unavailable until 00:00 UTC.
  - Publishing was refused.
  - Pages still served from the deploy copy, so the live text is v0.21.0.
- **Plan:** the integrator was waiting for the reset, then running `pnpm ship --redo status --redo d1-estimate` and `pnpm ship --yes`.
  - Check first: `pnpm publish-report --all --status`, and whether `/search` works on production.
  - If v0.22.0 isn't live, run that yourself as the integrator.
- **Bead `t4al` (P1):** the cause, and the fixes:
  - decide 0005 / `2oz` (Workers Paid), which removes the limit
  - make `pnpm ship` budget reads as well as writes
  - make the read-heavy steps incremental

## Rufus's four directions (2026-10-03)

1. **More reports.**
   - Epic `gqsy` "More reports (2026-Q4)": a scored shortlist. Top picks: Post Office Horizon Vol 1 (`gqsy.2`), January 6th Committee (`gqsy.3`), Grenfell (`gqsy.4`), Senate CIA torture summary (`gqsy.6`), Mueller (`gqsy.5`).
   - **TRC means South Africa's**, deferred (`gqsy.1`). Rufus has earlier research in a GitHub issue that no agent could find; ask him for the link.
   - Parked: Valukas Vol 5 (`94u`), Duelfer II–III (`d0h`), Chilcot volumes (`0a7`).
   - `as31`: a public `/progress` backlog page from `reports/pipeline.yaml` (spec only).
2. **Low-touch marketing. Done.**
   - Private repo https://github.com/reportsthatmatter/marketing holds the research, `plans/operating-plan.md`, the anniversary calendar and drafts (Show HN fact sheet, 5 Reddit posts, a newsletter pitch). Epic `y2y8`; lessons in site PR #278 (not merged).
   - HN and many subreddits ban AI-written text, so those drafts are fact sheets that Rufus rewrites in his own words.
   - **Launch blockers (P1):**
     - `y2y8.4`: canonical tags and structured-data dates, an agent PR
     - `y2y8.9`: licences on the public repos
     - `y2y8.7`: Rufus's launch week
   - **For Rufus by Tue 6 Oct:** create Bluesky (domain handle, plus a profile picture; Bridgy needs the account a week old), set the secrets, and verify Search Console and Bing. None of these exist yet.
3. **Sharing and highlights.** Epic `bght`; audit in `docs/design/2026-10-03-sharing-and-highlights.md`.
   - **Site PR #279 is open and not shipped.** It needs an agent review, then a release, next session.
   - **What the audit found:**
     - There are 163 editor's highlights on all 13 reports. They are already live, but each was mislabelled "Highlighted by 1 reader".
     - Quote previews show the report's title card, not the quote, so on X a shared quote shows no quote at all.
     - Jack Smith's card quoted words that aren't in the report (`bght.9`).
     - No `og:url` or image size, so Facebook and LinkedIn may show no image.
   - **What #279 does:**
     - labels the highlights "Editor's highlight"
     - adds verbatim quote cards for 72 highlights (2400×1260)
     - adds the missing OG tags
     - `pnpm highlight add '<link>' [--card]`, so Rufus can add his own
     - makes `seed-highlights` write only the difference (0 writes today, against 326 before)
   - **Still missing:** highlighting on phones (`bght.3`); a landing panel for a highlight (`bght.4`); cards for any reader's selection (`bght.2`, probably a Worker, tied to 0005).
   - Decision 0014 / `bght.6` is **accepted**: "Editor's highlight". Mark `docs/decisions/0014` accepted when #279 ships. Idea: a dedicated editor account, separate from Rufus's personal one, so he can highlight and annotate as himself.
   - Annotation (`bght.5`) is **deferred** to later.
4. **Quality bar plus an agent-driven improvement loop.** Beads only, under `38s`:
   - 38s.16: the grade
   - 38s.17: decision
   - 38s.18: `try --base`
   - 38s.19: `pnpm grade`
   - 38s.20: `grade next`
   - 38s.21: the loop
   A rough v0 grade: 9/11 A; Lehman, Chilcot, Saville and Philip Morris B; PSI and Hillsborough C; Leveson, Litvinenko, Jack Smith, Columbia and Challenger D; Deepwater F, before v0.22.0's endnotes fix.

## Suggested next steps

1. **Launch, Tue 13 Oct (`y2t.6`).**
   - Blocked on Rufus for `y2t.1`: Bluesky, Search Console, Bing. Then rehearse the poster in dry-run per `docs/poster.md`, and Rufus sets `POSTER_LIVE`.
   - Also `y2y8.9` (licences on public repos, P1) and `y2y8.7` (Rufus's launch-week posting batch).
2. **Next report through the pipeline:** Post Office Horizon Vol 1 (`gqsy.2`). It's small, tagged and OGL, so it's a good proof of the stage skills.
3. **Quality backlog:** the tooling first, because every agent asks for it: 38s.18 `try --base` and findings diffs, an `ingest link`, and `resolvePasses` throwing on unknown passes. Then the defects the new signals found (note-order beads gq4j, 4kfr, kvxj, n7fb, kgpr, qsfc, u00i, m827; anchors uw50, ssfk; plus qzix, n891, bt5d).
4. **Housekeeping:** about 30 stale `rtm-*`/`*-reports` worktrees under `~/src/reportsthatmatter/` from 2026-10-03 agents. Check each for uncommitted work, then remove the merged ones. The shared site checkout had a dirty `.beads/issues.jsonl` (bd export).

## Waiting on Rufus

- **Hosting and quotas (now urgent):** 0005 / `2oz`, Workers Paid or the Datopian account. Three releases in one day exhausted D1's free read limit and took search down for about 5 hours (`t4al`).

- **Launch:** `y2t.1`: Bluesky account and app password, Search Console, Bing.
- **Decisions** (records in `docs/decisions/`, beads labelled `decision`):
  - 0001 / `qap0`: page numbers
  - 0002 / `i8en`: annotation format
  - 0005 / `2oz`: hosting account
  - 0009 / `0ino`: say AI agents did the work?
  - 0011 / `fc3x`: serve vision-model words for scans (also note labels)
  - 0012 / `efso`: reserve published ids
  - 0013 / `9u69`: dev/held-out split for the mini-references
  - `38s.17`: grade thresholds and publishing grades
- **TRC:** the link to his earlier research issue.
- **Optional:** an `ANTHROPIC_API_KEY` in the session, to measure the page-break referee live. Its ceiling is low (1 of 22 held-out errors), so this is not urgent.
- **Blog drafts** in `content/posts/` (`/blog?draft`), and the request letters (`e693`, `6nkk`).

## Key documents

- Conventions and decisions: `AGENTS.md`, `docs/decisions/`.
- Release: `docs/release-checklist.md`, `pnpm ship` (`scripts/ship*`), the `report-publish` skill.
- Quality: `docs/quality-harness.md`, `docs/scoring.md`, the `docs/design/2026-10-02-*` docs, `docs/design/lessons.md`.
- Today's design notes:
  - `docs/design/2026-10-03-vision-structure-pass.md`
  - `docs/design/2026-10-03-hybrid-gap-fill.md`
  - `docs/design/2026-10-03-pagebreak-referee.md`
- Pipeline: `docs/design/2026-10-03-report-preparation-pipeline.md`, `.claude/skills/report-*`.

## Status, session 1 (2026-10-04, 00:21–06:00 CEST)

Ran 12 agents (worktrees `*-1004`). Paused about 02:00–06:00 CEST: the account hit its weekly usage limit, which reset at 06:00. The old session's v0.22.0 integrator never ran (it was rate-limited too). The old session (`reportsthatmatter-2d`) has confirmed it will not ship. Session 2 started at 06:01 and took over as integrator.

**Reviewed, ready to ship:**
- **Batch A, site only:** #279, #278, #287, #285, #283, #282, plus marketing#1.
  - Reviews: `review-site-pr279-2026-10-04.md`, `review-batchA-2026-10-04.md`.
  - D1 cost: about 42k reads and under 100 writes.
  - #287 needs migration 0004 and a one-off `reindex-search --record-layouts`.

**Open PRs for the ingest v0.23.0 release (batch B):**
- **Ingest:**
  - #61: `resolvePasses` throws on an unknown pass.
  - #62: folio passes for anchors.
  - #65: `folioReport`, stacked on #62.
  - #63: Post Office passes, including two shared changes. Leveson moves 6 paragraphs.
  - #64: note-order fixes.
- **Site:**
  - #281: `try --base`, the findings diff and `ingest link`.
  - #288: `pnpm ingest folios`, on top of #281.
  - #280: anchor budgets.
  - #286: the note-order signal and its derived files.
  - #284: Post Office Horizon Vol 1 (new report, graded about B+).
- **Report repos:** challenger#11, jack-smith#10 and #11, post-office#1, 911#12, litvinenko#10, hillsborough#11, lehman#10, psi#13, leveson#9.

**Results:**
- **D1 (t4al, #287):** the 5.48M reads on 2026-10-03 were measured through the analytics API. Most came from reindex and probe full scans. A release drops from about 2.06M rows read to about 79k, and a search from up to 70k rows to about 20. Adds `pnpm d1-usage [--alert 80]`.
- **Anchors (#62):** Challenger wrong markers 20 → 0; Jack Smith 2 → 0.
- **Note order (#64, #286):** the signal is fixed, and 7 reports are fixed. 9/11, Litvinenko, PSI, Lehman and Hillsborough are now at 0.
- **Post Office Horizon:** stages 1–5 done. Repo `reportsthatmatter/uk-post-office-horizon-inquiry`. The stage skills are improved in #284.
- **Housekeeping:** removed 95 merged worktrees. Branches with unsaved work were pushed as `salvage/*` in ingest. Loose logs are in `old-logs/2026-10-03/`.
- **January 6th (gqsy.3):** the agent was cut off by the limit, then resumed in session 2. Ingest #66 is in progress.

**New beads:** u09x, q2ph, 6kky, ib2b, vwqr, j6ld, 5p44, rw1q, ai23, 461y, 5qmm, ud34, 54li, xkmh, gqsy.2.1, gqsy.2.2, and a P3 for `ingest link` and the worktrees-prune check.

**For Rufus:**
- **Bluesky:** create the account by **Tue 6 Oct**. Bridgy Fed needs an account at least a week old to bridge to Mastodon by launch.
- **Mastodon:** does Rufus have a personal account?
- **Licences:** confirm them for Jack Smith, Columbia, Deepwater and Philip Morris, and correct Leveson's "Public domain" (it is Crown copyright).
- **Licence files:** no public repo has one yet (y2y8.9, P1).
- **Post Office plate:** is the Inquiry wordmark acceptable as the plate? Should AGENTS.md set a report-id convention?
- **Phone check:** try highlighting (#283) on a real iPhone and Android phone.
- **Housekeeping, optional:** force-remove the derived-dirty worktrees (`gapfill-reports/`, `rtm-pbj2`, `ingest-{b89,pbj2,oracle}`), and decide on the 3 `salvage/*` branches and 3 stashes.
