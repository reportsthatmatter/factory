# Next session: reportsthatmatter (written 2026-10-04, 09:20 CEST)

This replaces `next-session-prompt-2026-10-04.md`. Read that one only for history; its appended "Status, session 1" section covers the night. Start in `~/src/reportsthatmatter/reportsthatmatter`:
1. `bd dolt pull`, then `bd ready`.
2. Read `AGENTS.md` ("Working conventions" and "Decisions and open questions").
3. Read this file.

## How to work (short)

Unchanged from 2026-10-04. Supervise 5–7 agents, with worktrees and PRs only. A reviewer agent writes `review-*.md`, and one integrator alone ships per that review. Agents append to `docs/design/lessons.md` and end with a retro. Questions of direction go in `docs/decisions/` plus a `decision` bead. Conventions go in AGENTS.md, not memory.

**New since then:**
- **Shared agent preamble:** a copy of the one used today is in this session's scratchpad. Rewrite it from the 2026-10-02 protocol if it's gone. Name worktrees `*-<MMDD>` so housekeeping can tell them apart.
- **D1:** check `pnpm d1-usage` before and after any production step. Its analytics lag by a few minutes; the ledger is at `~/.local/state/rtm/d1-usage.jsonl`. Since #287, a release reads about 50–200k rows, not about 2M.
- **Usage limit:** the account hit its weekly usage limit at about 02:00 CEST on 2026-10-04, and work stopped until 06:00. Prefer Sonnet for specced work, and keep Opus for reviews, new heuristics and imports.
- **Integrator pitfalls** (see lessons):
  - Ship's pin bump fails on a frozen lockfile, so bump all 16 report repos by hand with `--no-frozen-lockfile` (proposal: `pnpm ship --bump-pins`).
  - New report repos lack `node_modules`.
  - The ratchet dirties `main`; the reviewer should run `pnpm quality ratchet` in the trial.
  - Pipeline state must be `reached: publish, state: waiting`, never `done`.
  - `pnpm release` must run on ingest `main`.
  - A ship state left from an older pin needs `pnpm ship --reset`.
  - `pkill -f scripts/verify.sh` kills other agents' runs, so don't use it.

## State at handoff

**Live: 16 reports, ingest v0.24.0.** Shipped on 2026-10-04:
- **v0.22.0:** the Challenger vision hybrid, Deepwater and Columbia endnotes, page-break joins, adjudicated breaks, and `pnpm ship`.
- **Batch A (site):**
  - #279 sharing and highlights: "Editor's highlight", quote cards, OG tags; decision 0014 accepted.
  - #283 highlighting on phones and the `?h=` landing panel.
  - #285 SEO: canonical URLs, JSON-LD, citation meta, report-name titles.
  - #287 D1 read budget: migration 0004, read layouts, rank-ordered search, `pnpm d1-usage`, ship budgets reads.
  - #278 and #282 lessons and marketing; marketing#1 outreach drafts.
- **v0.23.0:**
  - ingest #61–#67: unknown passes throw, folio passes and `folioReport`, Post Office passes, note-order fixes, `EditionNote.after`, short-section split.
  - site #281 `try --base` and `ingest link`; #288 `pnpm ingest folios`; #286 note-order; #280 anchors; #289 cards prune and the ship `cards` step; #291 strict ship flags; #292 edge cache keyed by Worker version.
  - **New report:** Post Office Horizon Vol 1.
- **January 6th Committee report** (site #290; `/reports/us-jan6-committee`). Its text comes from the committee's own HTML in govinfo's ZIP. Graded about B+.
- **v0.24.0:** Grenfell Phase 2 Vol 1 (ingest #68, site #293), graded about B. All 2,019 notes are linked, and figure and chart text is dropped. The 7 tables drawn as pictures are not shown (bnqs).

**D1 on 2026-10-04:** about 2.0M of 5M reads and about 10k of 100k writes, judging by the lagging analytics.

**Open PRs:**
- #294, the marks edge-cache: **waits for Rufus** (see below).
- Duelfer #182 and ingest#22, parked.

**Worktrees:** about 100 `*-1004` worktrees and dirs are under `~/src/reportsthatmatter/`. Most are merged. Run a housekeeping pass (rules as on 2026-10-04; a bead exists for a `worktrees prune` tool).

## Suggested next steps

1. **Launch, Tue 13 Oct (`y2t.6`).**
   - Blocked on Rufus: `y2t.1`, which covers Bluesky (create the account **by Tue 6 Oct**, since Bridgy Fed needs one at least a week old), Search Console and Bing.
   - Then rehearse the poster in dry-run (`docs/poster.md`).
   - `y2y8.9` licences on public repos (P1) needs Rufus's licence choice.
   - The launch-week batch is in the marketing repo, `plans/launch-week-batch.md`.
2. **Quality:**
   - **Glued paragraphs:** the new advisory signal `numbered-paragraph-glued` found 3 real cases in Leveson (1iz4).
   - **Page breaks:** sh1b and ni9o, a page-break rule for unnumbered continuations.
   - **Leveson:** a running head left mid-sentence (jr1t).
   - **9/11:** 4 paragraphs a page late (smof).
   - **Jack Smith:** the letterhead footer splits a paragraph (5sf1).
   - **Jan 6 captions:** photo captions that include credit lines (2t3t), and caption markers (bqu0).
   - **Grenfell tables:** take them from the HTML edition (bnqs; close to decision 0011).
3. **Tooling, from the retros:**
   - One report-directory resolver for every script, plus `pnpm ingest aggregate <id>` (j6ld, ai23, 461y).
   - `pnpm ship --bump-pins`.
   - `ship d1-estimate` calling `d1-usage` (r52n).
   - `pnpm posts --report <id>`. `pnpm posts` exits 1 on main for other reports' over-long quotes and stale Jack Smith items (5qmm).
   - `try --at <site ref>` and `--report <id>=<branch>`.
   - A `--check` mode for `editorial` and `prerender`.
   - A vitest helper that asserts rows read per request.
   - A schedule for `pnpm d1-usage --alert 80`.
   - Repeated `--redo` (pvzd), and the ingest-link bead (irfs).
4. **More reports (epic `gqsy`):**
   - Mueller (gqsy.5) and the Senate CIA torture summary (gqsy.6) are next.
   - Follow-ups: Grenfell Vols 2–7 and Phase 1 (gqsy.4.2), Post Office hero (gqsy.2.2), Grenfell hero (gqsy.4.1), Jan 6 hero (wrjn), Jan 6 supporting materials (vem3), Jan 6 score reference (865n).
5. **Reconciling budgets:** the January 6th furniture budget fell from 9 to 5 and lost its `# why:` line.

## Waiting on Rufus

- **Hosting:** epic `4g4d` (move to the Datopian Cloudflare account, for paid Workers) and decision `2oz` (P1). The first step, `4g4d.1`, needs Rufus to confirm the wrangler login from `~/src/datopian`.
- **#294, the marks cache:** readers other than the author would see new marks 2–30 minutes late. Rufus wanted a mark to show "the moment one reader has marked it", so which does he want? The alternative is a counts table plus the cache. It matters at launch traffic: a report with 2,000 marks would otherwise use the whole daily read budget.
- **Launch accounts:**
  - `y2t.1`: Bluesky by Tue 6 Oct, Search Console, Bing. A colleague is setting up the Bluesky account, tracked in https://github.com/life-itself/onboarding/issues/140; read that issue for its state.
  - Does he have a personal Mastodon account? The Mastodon drafts assume he does.
- **Licences:**
  - The repo licence choice (`y2y8.9`).
  - Confirm the text licence for Jack Smith, Columbia, Deepwater and Philip Morris.
  - Correct Leveson's README, which says "Public domain" for Crown copyright.
- **Reports:**
  - The Post Office plate uses the Inquiry wordmark: is that acceptable?
  - Should AGENTS.md set a report-id convention?
  - The Jan 6 PDF is 98.9 MB, just under GitHub's limit. Is that acceptable, against a 36 MB copy with printer's slugs on every page?
  - Should tables that the PDF draws as pictures be served from an HTML edition (bnqs)?
  - The Grenfell score was tuned on held-out data; this is noted in `score-sets.yaml`.
- **Phones:** try highlighting and the `?h=` landing panel on a real iPhone and Android phone.
- **Earlier decisions still open:**
  - 0001 / `qap0`: page numbers
  - 0002 / `i8en`: annotation format
  - 0009 / `0ino`: say AI agents did the work?
  - 0011 / `fc3x`: serve vision-model words for scans
  - 0012 / `efso`: reserve published ids
  - 0013 / `9u69`: dev/held-out split
  - `38s.17`: grade thresholds
- **TRC (gqsy.1):** the link to his earlier research issue.
- **Housekeeping, optional:**
  - Force-remove the derived-dirty worktrees: `gapfill-reports/`, `rtm-pbj2`, `ingest-{b89,pbj2,oracle}`.
  - Decide on the three `salvage/*` ingest branches and the three stashes.

## Key documents (new on 2026-10-04)

- Reviews in `~/src/reportsthatmatter/`:
  - `review-site-pr279-2026-10-04.md`
  - `review-batchA-2026-10-04.md`
  - `review-v0.23.0-2026-10-04.md`
  - `review-jan6-2026-10-04.md`
  - `review-v0.24.0-2026-10-04.md`
- `docs/design/2026-10-03-sharing-and-highlights.md` §7, on phones and the landing panel.
- `docs/design/lessons.md` sections: "D1 quotas (t4al)", "Comparing two ingests", and each ship's integrator entry.
- The marketing repo: `drafts/README.md` (send order), `plans/weekly-routine.md`, `plans/launch-week-batch.md`, `scripts/check-drafts.py`.

- **Late, from the note-order agent:** the remaining Leveson note findings now have a proposed fix each in the `u00i` bead: split raised fragments like "8 9 10", accept statute-reference notes, and the unlinked "office.70" cases. Garbled Leveson chapter headings are filed as reportsthatmatter-1ptg.
