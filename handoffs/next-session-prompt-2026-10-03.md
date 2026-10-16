# Next session — reportsthatmatter (written 2026-10-03)

This replaces `next-session-prompt-2026-10-02.md`. Start in `~/src/reportsthatmatter/reportsthatmatter`:
1. Run `bd ready`.
2. Read `AGENTS.md`, especially "Working conventions" and "Decisions and open questions". They hold how Rufus wants work done.
3. Read this file.

The beads hold the specs; this file gives the order and the context.

## How to work (short)

- **Supervise and delegate.** Use Sonnet for specced work and integrators, Opus for research, new heuristics and writing, and Fable only for big design synthesis. Run 5–7 agents at a time.
- **Session protocol:** copy `~/src/reportsthatmatter/.agent-protocol-2026-10-02.md` to a new dated file. It covers worktrees for everything, PRs only, one integrator, the retro (rule 12), lessons (13) and decisions.
- **Stage skills:** the preparation pipeline is in `.claude/skills/report-*` (stages 1–8) and `docs/design/2026-10-03-report-preparation-pipeline.md`. The stage record is `reports/pipeline.yaml`; check it with `pnpm pipeline status` once #248 is merged.
- **Releases:** follow `docs/release-checklist.md`. Publish with `--no-reindex`, then reindex only changed reports. D1 is on the free tier, 100k row writes a day.
- **Questions of direction** get a record in `docs/decisions/` and a bead labelled `decision`, never a chat answer. Conventions go in AGENTS.md, not agent memory.
- **Shared checkouts:** never write into one. Use `pnpm ingest worktrees <ids>` once #250 is merged.

## State at handoff (2026-10-03, ~12:00 UTC)

- **Live:** ingest **v0.19.0** across all 13 reports; production verify passed.
  - 9/11 is served from the Commission's HTML (the hybrid, ivg.1), with printed pages from the PDF.
  - Paragraph-id aliases are live (q8c), so old `?p=` links redirect.
  - The oracle budgets are live (b78.9).
- **On main, not served:** tooling (`pnpm ingest try`, `preflight`, `score --diff`, `publish-report --all --status`, `--preflight`, incremental reindex), the release scorecard (`pnpm scorecard`, `docs/scores.json`, dev/held-out sets) and `docs/design/lessons.md`. Also:
  - Wikisource page references for 9/11 and Chilcot: page-level WER 0.79% / 0.60%.
  - The blog (`/blog`, `content/posts/`, two drafts).
  - The pipeline design and skills.
  - The annotation-format design (decision 0002, proposed).
  - The decision log (`docs/decisions/0001`–`0010`).
- **This session's results, for the write-ups:**
  - Five ingest releases (v0.15.0–v0.19.0).
  - Page-break join accuracy went from 84.7% to 95.6% on the dev set (hand rules from the aligned-pairs study).
  - 9/11 severed and marker signals went to 0 with the hybrid.
  - Golden pages: 78 across 13 reports.
  - Alignment scorer and references for 7 reports, with adjudicated page breaks.
  - See `content/posts/getting-the-text-right.md` (draft).

## In flight at handoff

See the "Agents running at handoff" list at the bottom, which is updated as they finish. Everything else is a PR waiting for one integration:

**P0, `reportsthatmatter-a94n`: integrate ingest v0.20.0.** The bead has the full ordered steps. It covers:
- ingest #44 (page-break residuals), #45 (Saville cleanEdition), #46 (layoutMarkers: Leveson unlinked markers 7,497 → 142, wrong-page sidenotes → 0)
- 15 report-repo PRs, and site #244, #249, #251, #252
- **#250 last** (the shared-checkout guard changes `verify.sh`: integrators need `VERIFY_SHARED=1`)
- Saville goes live from its HTML: all 1,460 notes linked, golden 5/5, 0 wrong pages
- ids move in about 9 reports, so generate aliases

Site **#248** (pipeline status) merges in the same batch if it's still open. Site **#182** and ingest **#22** (Duelfer, old approach) stay held. They are superseded if the Duelfer hybrid (ivg.4) works.

## Sequence for the next session (beads, in order)

0. **`n9em` (P1, live bug):** 9/11 chapter openings are filed under the previous section (hybrid). Fix it first, or inside `a94n`; Saville must not ship with the same defect.
1. **`a94n`**: the v0.20.0 integration above. Sonnet integrator. Also merge #254 (draft post) in the same batch.
2. **Close out this session's agents** (see the bottom list). Integrate their PRs into v0.20.0 if ready in time, otherwise into v0.21.0:
   - `mv1t`: printed numbered paragraphs as a default, plus a signal (Hillsborough 384 paragraphs, `woke`; Valukas `xqu`)
   - `y0w9`: the `note-off-page` signal, plus layoutMarkers for Litvinenko and Philip Morris
   - `kyj3`: vision-model structure pass for Jack Smith and Challenger, design note only
   - `0z1o`: draft post on markdown vs HTML
3. **Launch, Tue 13 Oct (`y2t.6`).**
   - Blocked on Rufus for `y2t.1` (Bluesky account, Search Console, Bing).
   - Then `y2t.4`, the scheduled poster.
   - `y2t.2`/`y2t.3` are in progress.
   - Posts per report come from the pipeline's stage 8 (`report-promote`).
4. **Hybrid rollout, `ivg`.**
   - `ivg.4` Duelfer from the CIA HTML: structure only, because the HTML is the 2004 text and the PDFs are the 2005 printing. Notes are in `d0h`/`9br`. It unparks Duelfer.
   - `ivg.3` Hillsborough: needs a gap-fill mode for pages the HTML lacks.
   - `ivg.5` Philip Morris version, `ivg.6` tags as source.
   - Sonnet per report, Opus for gap-fill design.
5. **Get better faster, `38s`.**
   - `79ze`: record the shadow score for hybrid reports.
   - `s24x`: an independent page-anchor check.
   - `38s.11`: cached LLM referee for ambiguous page breaks.
   - `38s.13`/`38s.14`: per-report quote font and headings.
   - `38s.15`: adjudicated breaks for PDF-only reports.
   - `38s.3`/`38s.4`/`38s.5`: vision gold, synthetic benchmark, extractor bake-off.
   - Remaining parts of `38s.6`: error clustering, throughput.
6. **Harness and tooling.**
   - `b78.10`: notes-defined-not-referenced signal and the page-label scheme.
   - `b78.6`: sampled LLM review.
   - `hmqk`: mid-paragraph page anchors. It depends on decision 0002.
   - `p4h6`: `pnpm marks check`.
   - `jsk3`: bump-pin, content-based search staleness, dist check in CI.
   - `0c6i`: `pnpm ship`.
   - `14su`/`1w3` leftovers.
   - `fbgx` and `wl0.*`: PROCESSING.md and repo metadata gaps waived in `pipeline.yaml`.
7. **Pipeline skills, `ifb5`.**
   - `ifb5.10` source probe, `ifb5.11` report new/lint, `ifb5.12` report ready, `ifb5.13` compare pass lists.
   - Then run a new report through all stages as the proof. Candidates: Valukas Volume 5 unredacted (`94u`), Duelfer II–III (`d0h`, stage 1 done), Chilcot volumes (`0a7`).
8. **Report defect backlog** (park hard cases):
   - Leveson research session (`pwc`: `r0w`, `fkx`, `qai`, `odm6`, `mizx`).
   - Notes not parsed: Deepwater, Columbia, Hillsborough (`kgz8`, `b89`, `izw`, `a8l`). This blocks layoutMarkers there.
   - `mv0j` (PSI p.368), `48o`, `nk3` (Repo 105 one page), `liv`, `x0n`, `4yp`, `a3y`, `2hn` leftovers.
9. **Sources (`zphc` follow-ups):**
   - `egsa`: Deepwater EPUB.
   - `kyj3`: scans.
   - `e5dv`: sourcing checklist, folded into the `report-source` skill.
   - `e693`/`6nkk`: request letters, for Rufus to send.

## Waiting on Rufus

- **`y2t.1` (P0):** Bluesky account, Search Console, Bing, and the app password.
- **Decisions** (records in `docs/decisions/`):
  - 0001: page numbers. Proposed: anchor to the canonical edition's own locators.
  - 0002: annotation format. Proposed; 5 questions in the design doc.
  - 0005: Workers Paid vs. a Datopian move. Less urgent now that reindexing is incremental.
  - 0009: whether to say on the site that AI agents did much of the work.
  - 0011 (`fc3x`, in site #257): whether to serve vision-model words for scanned reports where the model reads better than the text layer.
- **Review the blog drafts** (`content/posts/`, `/blog?draft`): `getting-the-text-right` (check its v0.18.0 numbers against what's live), `where-better-source-texts-come-from`, and `0z1o`'s when it lands.
- **Send** the six request letters drafted in `docs/research/better-source-texts.md` (beads `e693`, `6nkk`).

## Key documents

- **Conventions and decisions:** `AGENTS.md`, `docs/decisions/`.
- **Release:** `docs/release-checklist.md`.
- **Quality:**
  - `docs/quality-harness.md`
  - `docs/scoring.md`
  - `docs/design/2026-10-02-quality-harness-{catalogue,plan}.md`
  - `docs/design/2026-10-02-alignment-scorer.md`
  - `docs/design/2026-10-02-learning-from-aligned-pairs.md`
- **Sources:** `docs/design/reference-editions.md`, `docs/research/better-source-texts.md`.
- **Pipeline and format:**
  - `docs/design/2026-10-03-report-preparation-pipeline.md`
  - `.claude/skills/report-*`
  - `docs/design/2026-10-03-annotation-format.md`
- **Lessons:** `docs/design/lessons.md`. Agents append to it.

## Agents running at handoff

(Updated as they finish; anything still listed here when the next session starts was in flight. Check its PRs.)

- ~~Batch integrator~~ **done.** It merged site #230, #231, #233, #236, #237, #238, #239, #243, #245, #246, #248 and #253, plus us-911-commission#10 and uk-chilcot-inquiry#8.
  - The blog is deployed; production verify passed; all 13 reports current.
  - Scorecard at v0.19.0: 9/11 F1 95.4% (+3.8), Saville 96.6%, Philip Morris 98.6%.
  - Follow-ups: `j3fw` (9/11 Wikisource page-marker score 0.2% after the hybrid; investigate), `r4q2` (lessons.md merge conflicts; retarget stacked PRs).
- **The session ended suddenly at 09:10 UTC (the machine rebooted, and `/tmp` was wiped).** `y0w9` had just finished, and `mv1t` and `kyj3` were cut off. Resume agents were launched on 2026-10-03 at ~09:30 UTC; see each item below.
- ~~`y0w9`~~ **done.** The `note-off-page` signal, plus `layoutMarkers()` declared in Litvinenko and Philip Morris; off-page notes 20 → 0 and 17 → 0.
  - PRs: ingest **#48** (stacked on #46), site **#255** (stacked on #251), uk-litvinenko-inquiry **#7**, us-v-philip-morris **#9**. The report PRs need the v0.20.0 pin bump, so merge them in `a94n` after the release.
  - Starting budgets: Leveson 121, Challenger 140, Jack Smith 71 (mostly over-merged paragraphs), PSI 18, Saville 10. Notes-at-back reports are excluded.
  - Follow-up `uren` (Litvinenko Appendix 7 restriction notices).
- ~~`mv1t`~~ **done (resumed).** Printed paragraph numbers are escaped by default.
  - PRs: ingest **#47**, site **#256** (the `numbered-prose-list` signal and budgets; tighten the budgets after the release), plus 9 report PRs:
    - challenger-accident#7, us-columbia-shuttle-accident#7, uk-hillsborough-panel#8
    - uk-leveson-inquiry#7, uk-litvinenko-inquiry#8, uk-saville-inquiry#8
    - us-deepwater-horizon#6, us-lehman-examiner#8, us-psi-financial-crisis#11
  - Ids gained: Hillsborough 335, Challenger 329, Litvinenko 143, Leveson 92, Saville 28, Columbia 15, PSI 13. Deepwater gains 4 and loses 9 (wrongly split page-break continuations, now rejoined), so it needs aliases.
  - Score: no held-out drop. Valukas p.748 is fixed (xfail narrowed to `0bf`). Chilcot, 9/11, Jack Smith and Philip Morris are byte-identical.
  - **Integrator note:** these report PRs overlap the `a94n` report PRs (pagebreak, b94, y0w9, Saville) in the same `full.md` files. Merge the code PRs, then re-ingest each report once at the new pin rather than merging the `full.md` diffs.
  - Its retro proposes that `pnpm ingest run` print the ingest version and path it used, because a locally linked ingest was silently ignored.
- ~~`kyj3`~~ **done (resumed).** Vision structure pass; measure-only, no served text changed.
  - PRs: site **#257** (runner, verifier, tests, the design note, decision record 0011, lessons), jack-smith-report **#8**, challenger-accident **#8**.
  - Runtime on MLX: Jack Smith 21.7 min, Challenger 136 min. Challenger's 84 looping pages take 58% of that.
  - Recommendation, revised after the full run:
    - Challenger: adopt verified vision **per page**, only where at least 80% of a page's blocks are accepted (151 pages, 41% of the words). Adapter work is 1–2 days.
    - Jack Smith: don't switch. Run the verifier as a standing cross-check.
  - Decision bead **`fc3x`** (decision 0011, for Rufus): whether to serve the model's words where it reads better than the text layer.
  - Its retro proposes a repetition guard and a per-page time limit in the runner, and running the whole-report oracle before recommending anything (the ten-page sample flattered the pass).
- ~~`0z1o`~~ **done.** Draft post `content/posts/one-file-per-report.md` in site **#254** (draft; SVG diagrams; code-block CSS). It's safe to merge as a draft; Rufus reviews at `/blog/one-file-per-report?draft`. Claims to confirm are in its `review:` frontmatter.
  - It found **`n9em` (P1, live):** 9/11 chapter 1's opening paragraphs are filed under the preface page, a hybrid regression. Fix it, and check it doesn't recur for Saville before `a94n` ships it.

## Afternoon session, 2026-10-03 (resumed after the reboot)

Rufus: "I don't really review PRs… reviewed by another agent, and then get them shipped." Agent review is the gate; the integrator records this in AGENTS.md. Duelfer is out of scope (site #182 and ingest #22 stay held).

- **Agent review** of all v0.20.0 PRs: done. It is in `review-v0.20.0-2026-10-03.md`: everything ships, with 6 fixes.
- **`n9em`:** fixed in ingest **#49** (`splitSections` forward-fold) and site **#258** (the `chapter-intro-misfiled` signal). Both go into v0.20.0.
- **Running:**
  - the `a94n` integrator (ships v0.20.0 with everything above, plus the mv1t, y0w9, kyj3 and n9em sets)
  - `y2t.4` scheduled Bluesky poster (dry-run until `y2t.1`)
  - `ivg.3` Hillsborough hybrid with a general gap-fill mode
  - `79ze` + `j3fw` hybrid scoring
  - `s24x` independent page-anchor check
  - `p4h6` `pnpm marks check`

### Shipped: ingest v0.20.0 (`a94n`, closed)

Site main is `2eda138e`, and production verify passes.
- **Merged:** ingest #44–#49 and 30 report PRs, plus site #244, #249–#252, #254, #256–#259. Site #255 was closed because its commits came in through #251.
- **Published:** 13 reports. About 53k D1 writes for the reindex; 163 highlights seeded.
- **Live now:** Saville from its HTML, and the n9em fix with 16 section aliases written by hand. 9/11 `/11-inside-the-four-flights` redirects with a 302.
- **Closed:** 2hn, j6qm, ky1o, ivg.2, b94, qgf7, m7ga, y0w9, mv1t, kyj3, n9em, 79ze, j3fw.
- **Shipped regressions and follow-ups:**
  - `4ef1`: Litvinenko has about 40 bare markers after closing quotes (P2; agent running).
  - `hxo4`: aliases should record section-slug moves (agent running).
  - `rf4c`: Challenger reused id `findings-3`.
  - Budgets for the Leveson contents pages were raised.
- **Leftover worktrees** to remove: `int-reports/`, `rtm-int`, `ingest-int`, `ingest-v019`.

### Next release (v0.20.1 / v0.21.0): PRs waiting

- Site #260: scheduled poster, dry-run until Rufus adds secrets. See `docs/poster.md`.
- Site #261: `pnpm marks check`.
- Site #262: marks follow aliases (`j53o`); stacked on #261.
- Ingest #50 and site #263: page stamps inside tables (`8x9n`). 9/11 page WER 1.7% → 1.0%.
- **Running:**
  - `ivg.3` Hillsborough hybrid with gap-fill
  - `s24x` page-anchor check
  - `38s.11` LLM page-break referee
  - Challenger per-page vision hybrid
  - `4ef1`
  - `hxo4`

### Late afternoon: PRs for v0.21.0 (none merged yet)

The session hit its usage limit here. The v0.21.0 integrator has NOT been started.

- **Review of v0.21.0:** the reviewer agent was running. Its output goes to `review-v0.21.0-2026-10-03.md`; if that file is missing, re-run the review.
  - **In scope:**
    - ingest #50–#54
    - site #260–#269
    - uk-litvinenko-inquiry #9, uk-hillsborough-panel #9
  - **Not yet sent to the reviewer:** the Challenger vision hybrid (jsqw): ingest #55, challenger-accident #9, site #270.
    - Merge #55 together with #53: both add `Block.source`.
    - Integrator steps are in #270 / the jsqw bead.
- **Highlights:**
  - Hillsborough from HTML with gap-fill (golden 0/5 → 5/5, F1 76.1 → 87.3).
  - d662 unmarked pages: Leveson, Deepwater, Columbia and PSI go to 0.
  - Challenger hybrid: golden 0/7 → 2/7, paragraph-start F1 78.7 → 93.8 on the hand-checked pages.
  - 4ef1 Litvinenko markers fix.
  - Bluesky poster (dry-run).
  - marks check; marks follow aliases.
  - Independent page-anchor check in verify.
  - aliases with the old pin; decision 0012 proposed.
  - LLM referee tooling: the live measurement needs `ANTHROPIC_API_KEY`; ceiling is low (1 of 22 held-out).
- **Still running at cut-off; check their PRs:**
  - page-break joins across footnotes and captions (from 38s.11)
  - Deepwater and Columbia endnotes, plus layoutMarkers (b89, izw, kgz8)
- **Next:** finish the review including #55, #270 and challenger #9. Then start one Sonnet integrator for v0.21.0, as for v0.20.0. Rufus has authorised agent review followed by shipping.
- **For Rufus:**
  - decisions 0011 (`fc3x`, vision words; note labels from the model) and 0012 (id reservation)
  - `y2t.1` Bluesky secrets, see `docs/poster.md`
- **Update:** the v0.21.0 review is DONE: `review-v0.21.0-2026-10-03.md`. Nothing is held. Fix these at merge time:
  - add the help import to #260 and #261
  - rebuild `dist` for #52
  - resolve the #50/#53 conflict
  - after `aliases generate`, restore the deleted alias lines (file a bead)
  - Hillsborough anchor budgets need a reasoned raise
  - run `pnpm posts --drop-stale`
  - regenerate the lockfile
  - Hillsborough reused id: run `marks check`, then `--accept-reuse`
  - The Challenger hybrid set (#55, #270, challenger#9) is NOT reviewed; review it separately or ship it in v0.22.0.
- **Evening:** the usage limit reset. The v0.21.0 integrator is running (scope per the review; the Challenger set goes to v0.22.0). The page-breaks-across-interruptions agent and the Deepwater/Columbia endnotes agent were resumed. After v0.21.0 ships: review the Challenger set (#55, #270, challenger#9) plus those two agents' PRs, then ship v0.22.0.
- **v0.22.0 queue:**
  - Challenger vision hybrid: ingest #55, site #270, challenger#9.
  - Deepwater and Columbia endnotes: ingest #56, deepwater#7, columbia#8, site #271. Notes are linked by chapter; Deepwater loses 581 ids and Columbia 432, mostly old endnote paragraphs.
  - Page breaks across interruptions (y7ix): ingest #57, site #272. Held-out errors 15 → 12. Beads sbnn and 3ezs; 3ezs breaks Jack Smith verify until fixed or budgeted.
- **Running:**
  - v0.21.0 integrator
  - `0c6i` `pnpm ship`
  - `38s.15` adjudicated breaks for PDF-only reports
  - `3ezs` + `sbnn` + the note-order signal
  - `a8l` Hillsborough fused headings

### Shipped: ingest v0.21.0

Site main is `63ebf7de`, and production verify passes.
- **Live now:**
  - Hillsborough from the website HTML, with gap-fill; golden pages 5/5
  - unmarked pages filled in PDF builds
  - the Litvinenko marker fix
  - 9/11 table stamps
  - poster (dry-run)
  - marks check; marks follow aliases
  - anchors check in verify
  - aliases with the old pin
  - referee tooling
- **D1:** about 71k of 100k writes used today.
- **New beads:** the `aliases generate` base bug, and Hillsborough manifest vs score-sets.
- **About 30 stale `rtm-*` worktrees remain;** clean them up.
- **v0.22.0:** the review is running. Its output goes to `review-v0.22.0-2026-10-03.md`.
  - Scope: ingest #55–#59, site #270–#274 (#273 = `pnpm ship`), challenger#9, deepwater#7, columbia#8.
  - Ship with `pnpm ship`, probably after 00:00 UTC for D1 headroom.

### Rufus's four directions (2026-10-03 evening)

1. **More reports**, including the Truth and Reconciliation Commission (SA or Canada; the agent compares both). An epic "More reports (2026-Q4)" is being created.
2. **Low-touch marketing.** A private `reportsthatmatter/marketing` repo for research and plans; Reddit, HN and newsletter drafts for Rufus to post; Bluesky rehearsal (dry-run only).
3. **Sharing and highlights:**
   - audit of highlight, share, landing page and social previews
   - curated (editor's) highlights seeded so every report has some
   - an annotation plan
4. **Quality bar plus an agent-driven improvement loop.** A Fable design doc with grades per report and an epic.

Each agent creates its epic and children in beads. Ship v0.22.0 when the review lands, using `pnpm ship` (#273).
- **Corrections from Rufus (same evening):**
  - TRC means **South Africa**. It is **deferred**; the research already exists in GitHub issues, so capture it in a bead only.
  - Log a bead to **publish the backlog/pipeline on the site**.
  - **Bluesky is not ready.** Marketing is plan, research and drafted content only.
  - Sharing work is **testing the current state and priming the pump** with highlights.
  - **Quality bar: beads only, under 38s.** Don't repeat the 2026-10-02 quality-harness design pass.
