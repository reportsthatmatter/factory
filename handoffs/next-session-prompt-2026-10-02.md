> **Superseded by `next-session-prompt-2026-10-03.md`.** Kept for history.

# Next session — reportsthatmatter (written 2026-10-02)

Start with `bd ready` in `reportsthatmatter/` and `bd show` the beads named here. The beads hold the specs; this file only points at them. The agent protocol is `~/src/reportsthatmatter/.agent-protocol-2026-10-02.md`; rule 12 requires a "Getting better faster" retro from every agent.

## State at handoff

- **Supervisor run, 2026-10-03 07:14 UTC.** The session's login expired overnight, so the 02:12 Phase B didn't run.
  - Resumed: the v0.18.0 integrator, now doing Phase B (deploy and publish all 13); and the `zphc` agent, finishing its PR #229.
  - Started: `q8c` (id aliases), the tooling batch (`1w3`, `6px`, `14su`, the rest of `h6b`), and `b78.9` (oracle fixes and budgets).
  - Waiting to integrate: `ivg.1` (ingest#41, us-911-commission#9, site#228), after `q8c`.
  - Waiting on Rufus: the post (site#227).
- **Later the same morning:**
  - **Live:** v0.18.0 on all 13 reports; prod verify passes. `38s.10` closed. `h6b` closed, with the remainder now `ewm0`. `zphc` merged as `docs/research/better-source-texts.md`.
  - **Integrator running:**
    1. `q8c` aliases (site#232), deploy, then republish all 13 with `--no-reindex`.
    2. v0.19.0, carrying ingest#41 (9/11 hybrid) and #42 (oracle fixes), then site#228 and #235.
  - **Supervisor run, ~10:30 local:** ingest v0.19.0 is tagged; its integrator is still working through site#228 and #235. Running: integrator, `b94`, page-break leftovers, `kyj3` (vision pass), and `ivg.2` (Saville hybrid), started this run. Waiting to integrate: #230, #231, #233, #236, #237, #238, #239, us-911-commission#10, chilcot#8.
  - **Earlier, also running:** the tooling batch (`1w3`, `6px`, `14su`, `ewm0`, plus `pnpm ship`), `7d4y` (Wikisource refs), `38s.6` (scorecard and `docs/design/lessons.md`), and `b94` (unlinked markers, Opus).

- **FIRST: finish the v0.17.0 publish (bead `h6b`).** On 2026-10-02 the D1 free-tier daily write limit (spent by the search reindex) blocked 8 reports: Philip Morris, Leveson, PSI, `litvinenko-inquiry`, Saville, Chilcot, 9/11 and Challenger. They serve their old text. After 00:00 UTC, publish them with `--no-reindex`, reindex them smallest first, then run prod verify. Ask Rufus whether to move to Workers Paid (about $5/month).
- **Later releases at handoff:**
  - Ingest v0.16.0 (the `apk` note-pairing fix) and v0.17.0 (`quoteListRunOns` joins, 316 of them; the layout API and oracle `b78.5`).
  - Scorer label quality (`38s.12`): adjudicated references, measuring Hillsborough 0% and Chilcot 4.5% wrong.
- **Agents running at handoff:** `38s.10` (layout-gated join pass, Opus), `b78.4` (golden pages plus `pnpm ingest page`), and the `38s.12` merger. Read their PRs if they're unmerged.

- **Live:** ingest v0.15.1. Production verify passes, including the new quality budget gate. There are 13 reports; Valukas now includes Volume 3 (Repo 105), and its introduction covers it.
- **Shipped this session:**
  - Fixes across all 13 reports (9 ingest PRs → v0.15.0).
  - Chilcot's 90 headings, with the Lessons in its reading guide.
  - Litvinenko's running heads removed (227 → 12) and its severed quotes reduced (244 → 87).
  - 9/11 contents and endnotes.
  - Challenger's Conclusions.
  - Leveson's quote-tail joins.
  - The Deepwater, PSI and Jack Smith bugs.
  - `pnpm release`.
- **Harness (`b78`):**
  - The catalogue and plan are in `docs/design/2026-10-02-quality-harness-*.md`.
  - `pnpm quality` checks 17 signals against per-report budgets in `reports/quality-budget.yaml`, inside `verify.sh`. Budgets only go down (`b78.2` and `b78.3` are closed).
  - Next: `b78.4` golden pages, `b78.5` pdftohtml oracle, `b78.6` sampled LLM review, `b78.8` the list-start signal.
- **Open worktrees:**
  - `rtm-duelfer` and `ingest-duelfer` (Duelfer, parked).
  - `rtm-valukas-repo105`, which is detached and safe to remove.
  - Whatever the scorer agent left (see below).

## Autonomous queue (for scheduled supervisor runs)

**Each run (or fresh session), in order:**
1. **Check before starting anything.**
   - `ListAgents`: agents still running.
   - `gh pr list` in the site, ingest and report repos: unmerged PRs.
   - `bd ready`: what's available.
   - Never start a second agent on a bead or report repo already being worked.
2. **Publishing rule.**
   - Never deploy or publish unless D1 writes work: test one report with `publish-report --no-reindex` (bead `h6b`).
   - Deploy only together with the matching publishes, because editorial ids change.
   - Always publish with `--no-reindex`, then reindex changed reports smallest first.
3. **Integrate finished work.** One Sonnet integrator per batch:
   - releases ingest with `pnpm release`
   - bumps pins, then runs `pnpm install` in each report repo (bead `14su`)
   - re-ingests and reads every diff, then accepts
   - runs `pnpm quality ratchet`, `pnpm score --all`, and verify
   - deploys and publishes only per rule 2
4. **Capacity.** Keep 4–6 agents running. Pick from the queue below in order.
   - Models: Sonnet for specced work, Opus for research and new heuristics.
   - Every agent works in worktrees (site, ingest and report repos) and ends with a retro.
   - Put retro proposals into beads.
5. **Update this file's state lines** at the end of each run.

**Queue (top first; skip items already done or running):**
1. **Phase B of v0.18.0** (scheduled 02:12 local). Resume the v0.18.0 integrator, or follow bead `h6b`.
2a. **Integrate `7d4y`** (Wikisource page references): site#236, us-911-commission#10, uk-chilcot-inquiry#8. Measure-only, no text change. It goes in the next integrator batch, after the v0.19.0 integrator finishes, together with site#238 (`38s.6` scorecard; `scripts/score.mjs` conflicts with #236, so merge #238 first and rebase #236 onto it, filling the WER hook). After the merge, run `pnpm score` and `pnpm scorecard --record`.
2d. **Merge site#250 (`m7ga` shared-checkout guard) at the END of the v0.20.0 integration.** After it, `verify.sh` refuses to aggregate from shared checkouts unless `VERIFY_SHARED=1`, so integrators must set it; agents use `pnpm ingest worktrees <ids>`. Tell running agents when it lands.
2c-ter. **Also in v0.20.0:** site#252 (`qgf7`, quote-normalised anchoring). Merge it before the Saville hybrid publishes.
2c-bis. **Also in v0.20.0:** `b94` layoutMarkers.
   - PRs: ingest#46, Leveson#6, PSI#10, Chilcot#10, Lehman#7, site#251.
   - Effect: Leveson unlinked markers 7,497 → 142; wrong-page sidenotes → 0.
   - Raise the two Leveson budgets per `odm6`.
   - Ids move: Leveson 249, PSI 8, Chilcot 2, Lehman 2. Generate aliases.
   - Chilcot#9/#10 and Lehman#6/#7 touch the same repos as ingest#44's report PRs; merge them in sequence.
2c. **Next release (v0.20.0):** ingest#44 + ingest#45 (Saville cleanEdition `notes: "page-foot"`; moves 9/11 by 4 page markers) + uk-saville-inquiry#7 + site#249 (Saville hybrid; ship only once qgf7, quote-normalised anchoring, is merged or bundled) + ingest#44 (page-break leftovers 2hn/j6qm/ky1o), its 10 report PRs (9/11 #11, PM #8, JS #7, Lehman #6, Challenger #6 (corrections change), Chilcot #9, Hillsborough #7, Leveson #5, Deepwater #5, PSI #9) and site#244. Generate aliases (ids move in 7 reports), seed highlights (a Jack Smith id moved), publish with --no-reindex, then reindex the changed reports.
2b. **Integrate the tooling batch** in the same batch:
   - #230 (`14su` preflight), #231 (`1w3` ingest try), #233 (`6px` status), #237 (`ewm0` incremental reindex and `--preflight`), #239 (release checklist).
   - #233 and #237 conflict in `publish-report.mjs`; keep both sides.
   - Incremental reindex: a typical release is about 4.6k D1 writes, against 253k for a full rewrite.
   - Next after it: `0c6i` (`pnpm ship`).
2. **Merge the data-wrangling post** (site PR #227) only after Phase B is live. Hold it if Rufus hasn't answered on the AI-agents sentence; note it in the run summary.
3. **`ivg.1` is done:** integrate ingest#41, us-911-commission#9 and site#228.
   - The 9/11 severed and marker signals go to 0 and all 1,742 notes are linked.
   - All 22 cited ids still resolve, and `aliases.yaml` holds the 337 changed ids.
   - Ship after `q8c` (id aliases), or accept that 128 uncited old ids won't redirect.
   - Gap-fill mode for Hillsborough and Duelfer is the open design question.
4. **`q8c`** (paragraph-id aliases), Sonnet. Must land before any hybrid report ships.
5. **`zphc` research PR:** merge the docs, and turn its recommendations into beads (already part of its task).
6. **Hybrid rollout after the pilot:** `ivg.4` Duelfer (unparks the report), `ivg.2` Saville, `ivg.3` Hillsborough. Sonnet, one report each.
7. **Tooling batch** (Sonnet, one agent):
   - `1w3`: `pnpm ingest try`
   - `6px`: publish `--status`
   - `14su`: stale-install preflight
   - `h6b`'s reindex-only-changed and `--preflight`
8. **`b78.9`:** oracle fixes and per-report budgets. Sonnet.
9. **`38s.6`:** release scorecard; `pnpm score` prints the headline numbers to `docs/scores.json`. Sonnet.
10. **`b94`:** unlinked footnote markers corpus-wide (about 9,100). Design with the oracle and the scorer. Opus.
11. **`2hn`, `ky1o`, `j6qm`:** page-break leftovers. Sonnet, with golden pages.
12. **`38s.11`:** cached LLM referee for ambiguous page breaks. Sonnet.
13. **`pwc`:** Leveson research session. Opus, its own agent.
14. **`jedz`:** blog section (running at 10:40 on 2026-10-03). Sonnet.
15. **`ifb5`:** report preparation pipeline (design agent running); then build the stage skills.
16. **`i8en`:** annotation format design, inline vs. sidecar. Opus.
17. **`0z1o`:** post on consolidated markdown vs. HTML, with diagrams. Opus. It goes into the blog posts folder as a draft.

**Never without Rufus:**
- Launch accounts (`y2t.1`).
- The Workers Paid upgrade and the Datopian move (`2oz`).
- Sending FOI letters or emails to publishers.
- Editorial claims flagged for his confirmation.

## The main thread: get better faster (epic `38s`, P1)

Rufus: integrator and research sessions should learn how to improve faster, not only fix bugs. Bugs keep getting through because we have no clean text to score against.

- **`38s.1` (done):** `docs/design/reference-editions.md`.
  - **Clean HTML:** 9/11 and Saville (score 5).
  - **Court text:** Philip Morris (version caveat).
  - **Tagged PDFs:** Hillsborough (97% match), Columbia, Chilcot, Leveson and Litvinenko.
  - **No reference:** Jack Smith, PSI, Challenger, Deepwater and Lehman.
  - **Hybrid design:** take structure from the clean edition and page anchors from the PDF. Keep the PDF ingest as a shadow, scored against the clean text, because clean-edition reports are the training set for PDF-only reports.
- **`38s.2` (built):** the alignment scorer.
  - It provides `pnpm score <report>` and `pnpm score --all`. See `docs/scoring.md` and `docs/design/2026-10-02-alignment-scorer.md`.
  - It mirrors references into each report repo's `reference/`. Site PR #213 and the report-repo `reference-edition` PRs were being merged at handoff; check they landed.
  - Boundary F1: 9/11 91.0, Saville 96.4, Philip Morris 96.5.
  - Footnote marker recall on Philip Morris is 2%.
  - Joins across page breaks are the weakest decision: 74% right on Philip Morris, 88% on 9/11, 92% on Saville, against 97–99.6% within a page.
  - The b78.2 signals catch only about 55% of spurious splits at 68% precision. `note-text-in-body` and `severed-paragraph` are nearly useless; `severed-paragraph-capital` is 82% precise and deserves a budget.
  - It exports `decisions.jsonl` (about 71k labelled rows with pdftohtml layout features) for `38s.8`.
  - New beads: `apk` (9/11 wrong-note cascade: a spurious `[^20]` makes later `[^20]`s open the previous chapter's note; fix it in one place with chapter-qualified labels or section-scoped matching), `7go`, `mkm`, `17v`, `a8l`, `wck`.
- **`38s.8` (done):** `docs/design/2026-10-02-learning-from-aligned-pairs.md`.
  - Page-break join accuracy:
    - pipeline: 84.7% dev, 80.8% held-out
    - layout-gated hand rules: 95.6% dev, 88.9% held-out (93% on PDF-checked held-out rows)
    - depth-4 tree: 87.3% held-out
  - The tree only rediscovered the rules, so don't ship learned models.
  - A cached Sonnet referee on the ambiguous 9% scored 86% against 77% for the rules, at $0.0024 per decision.
  - Quote and heading style don't transfer across publishers: declare them per report (`quoteFont()`, typographicHeadings).
  - References are noisier than assumed: about 10% of held-out labels were wrong. Adjudicate 30 page breaks per reference.
  - Follow-ups:
    - `38s.9`: quote and list joins, text only. Agent running at handoff.
    - `38s.10`: layout-gated join pass; needs `b78.5`, whose agent was running at handoff.
    - `38s.11`: cached referee.
    - `38s.12`: label and reference fixes. Agent running at handoff.
    - `38s.13` to `38s.15`.
- **Then:**
  - `38s.3`: vision-model gold pages.
  - `38s.4`: synthetic benchmark.
  - `38s.5`: extractor bake-off.
  - `38s.6`: dev/held-out discipline, error clustering, release scorecard, retros into `docs/design/lessons.md`.
  - `38s.7` (Fable or Opus): synthesis design doc for Rufus.
- **Decision to make after `38s.2`:** whether to switch 9/11, Saville and Hillsborough to clean-edition sources (the hybrid design).

## Other priorities

1. **Launch (`y2t.1`, P0, needs Rufus):** the Bluesky account, Search Console and Bing. Also review the `pnpm posts` preview.
2. **Research sessions, one report each, Opus, outside general sessions:**
   - Leveson (`pwc`: `r0w` headings, `fkx` gutter, `qai` edge letters).
   - Duelfer (`9br`, `osv`, `1l4`; ingest PR #22 and site PR #182 are held).
3. **Corpus-wide unlinked footnote markers (`b94`):** about 9,100, Leveson 6,450. This needs design, and the scorer's footnote-link metric should guide it.
4. **Leftovers from this session:**
   - Still-open fix beads: `nen` (Philip Morris, Challenger and 9/11 capitalised splits), `djy`, `48o`, `izw`, `a3y`, `xqu`, `nk3`, `liv`, `x0n`, `4yp`.
   - The 9/11 false note link and the Deepwater notes appendix (filed by the integrator).
5. **Tooling from retros:**
   - `461` (`pnpm prerender` in worktree setup).
   - `6px` (publish `--status` drift table).
6. **More volumes:** `0a7` (Chilcot), `d0h` (Duelfer), `94u` (the rest of Valukas).

## How to work (unchanged, plus)

- **Supervisor pattern:** pick the model per task.
  - Sonnet: specced code, rollouts, integrators.
  - Opus: new heuristics, research, introductions.
  - Fable: only for big design synthesis.
- **Fix agents** open PRs; one Sonnet integrator releases with `pnpm release`, tags after the squash-merge, bumps pins, reads every diff, deploys, publishes and verifies production.
- **Capacity:** 5–7 agents at a time.
- **Model your plan on measurement:** prefer work that produces a number (scorer, ground truth, error clusters) over another one-off pass.
- **If Wrangler auth expires,** ask Rufus to run `! pnpm wrangler login` in the site repo.
