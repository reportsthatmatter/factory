# Next session — reportsthatmatter (written 2026-09-28)

Start with `bd ready` in `reportsthatmatter/` and `bd show` the beads named here. The beads hold the specs; this file only points at them.

## State at handoff

- Site main is clean at `abe3f0f`, and ingest is at tag `v0.14.1`. Only one worktree is left open: `rtm-duelfer`, which is parked (see below).
- There are now **13 reports live**. Chilcot (Executive Summary) and Valukas (Volume 1, id `us-lehman-examiner`) were added this session, and both have an approved introduction and a hero photograph.
- `verify.sh` passes locally and in production.
- Shipped this session, in three ingest releases:
  - **v0.13.0:**
    - Jack Smith page-break splits (GitHub jack-smith-report#1 closed; `pageBreakContinuations`).
    - Deepwater even-page quotes and headings.
    - All 4,088 Philip Morris findings are now citable.
    - Columbia moved off its v0.11.0 pin, with its structure fixed.
    - Paragraph ids with ʼ are now in the section index.
  - **v0.14.0:**
    - 9/11 endnotes and chapter levels.
    - PSI run-over notes.
    - Hillsborough and Challenger headings.
    - Chilcot and Valukas text.
  - **v0.14.1:** Chilcot's numbered paragraphs are citable (64 → 956).
- Also shipped:
  - `pnpm posts` builds the queue in `marketing/queue.yaml`; its preview is for Rufus.
  - The launch-thread draft is in `docs/plans/2026-08-02-launch-and-seo.md`; Rufus has OK'd the demo posts.
  - A **paragraph-density gate** in `pnpm corpus check`: a report fails with fewer than 4 ids per 1,000 words. It is the first concrete check for the harness epic.

## Parked, needing its own research session

- **Duelfer (`9br`, `osv`, `1l4`):**
  - The side-banner splice is fixed in ingest PR #22, which adds a per-volume `crop` option; it is unmerged.
  - Site PR #182 and the report repo's `banner-fix` branch are held.
  - Headings still collapse to a single section.
  - Rufus: don't churn on it inside a general session; research it separately.

## Priorities

1. **Launch (`y2t`):** `y2t.1` is P0 and needs Rufus (Bluesky account, Search Console, Bing). `y2t.3` is merged; Rufus reviews the preview.
2. **Harness epic (`b78`):** `b78.1`'s notes now hold many examples collected by this session's agents. Next step is to turn them into the examples catalogue, then plan systematic checks. The density gate is an example of the kind of check wanted.
3. **Rollouts** of passes that are currently opt-in:
   - `nen`: `pageBreakContinuations` to the other reports. It has per-report counts. Leveson (36) needs care: some are real quotations.
   - `tqv`: `numbersTrackPages`.
4. **Report text bugs filed this session:**
   - Deepwater: `xay`, `cbr`, `0ij`.
   - Philip Morris: `yun`.
   - Columbia: `6zo`.
   - Jack Smith: `2dw`.
   - PSI: `74p`.
   - Ingest: `8oc`.
   - 9/11: `w1n`, `5u2`, and `5fn` (still open).
   - Valukas: `0bf`, `1xr`, `djt`.
   - Chilcot: `ixe`, with 19 headings still missing, which is why its reading guide skips the Lessons.
   - Challenger: `m2y` (partly fixed) and `48o`.
5. **More volumes:** `dnv` is Valukas Volume 3 (Repo 105); Rufus: do this next. Also `0a7` (Chilcot), `d0h` (Duelfer) and `94u` (the rest of Valukas).
6. **Tooling:**
   - `pte` (P3): one `pnpm release` script for ingest. v0.14.1 was first tagged without rebuilding `dist/`.
   - `f2e` (P2): one share card per highlight.

## How to work

- **Pick the model per task before spawning** (Rufus pushed back on all-Opus this session):
  - Opus only for novel, risky design, such as a new heuristic pass or an id scheme, and for writing introductions.
  - Sonnet for applying existing passes, stage-1 ingests, specced code, copy, heroes and release chains.
- **The pattern that worked:**
  - Fix agents open PRs and never release. Each report repo has exactly one owning agent.
  - One Sonnet integrator merges the ingest PRs in order, tags after the squash-merge, bumps pins, re-ingests, runs verify, then deploys and publishes.
  - The protocol file is `~/src/reportsthatmatter/.agent-protocol-2026-09-27.md`.
- **The site's ingest pin runs everything:** `pnpm ingest run` and `pnpm ingest check` use the site's pinned `@rtm/ingest`, not the report repo's. A site pin bump re-ingests every report.
- **Usage limits:** the session hit its usage limit twice with about 13 agents running. Run 5–7 at a time and resume stopped agents with SendMessage; they keep their context.
- Keep running without stopping for input, and batch real questions at the end.
