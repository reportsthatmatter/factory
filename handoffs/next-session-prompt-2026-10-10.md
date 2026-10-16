# Next session: reportsthatmatter (written 2026-10-10, ~04:15 CEST)

Replaces `next-session-prompt-2026-10-05.md`. Start in `~/src/reportsthatmatter/reportsthatmatter`: `git pull`, `bd dolt pull`, read AGENTS.md (now has **Skills**, **Work streams**, **Bead levels and handoff-ready beads**, **Converge and ship**), then this file.

## What changed in how we work (2026-10-09/10)

- **Streams:** every open bead has one `stream:*` label (marketing, reports, quality, product, platform). Triage: `triage-2026-10-09.tsv` (29 closed, 18 deferred to P4).
- **Levels and handoff:** every open task/bug has `level:specced|judgement|design`; 66+ beads are `handoff`-ready (Goal / Where / Acceptance / Verify / Out of scope). Queue for any agent: `bd ready --label handoff --label level:specced`. Report: `backlog-2026-10-09.md`.
- **Skills (15), cold-tested** and in the repo (`.claude/skills/`, symlinked as `.agents/skills` for Codex). New runbooks: `fix-agent`, `pr-review`, `integrate-and-ship`, `burn-down`, `bead-writing`. The per-agent protocol is now `docs/agent-protocol.md`. `tests/skills.test.ts` guards named scripts and paths.
- **Converge and ship** (AGENTS.md): stop adding passes when each fix surfaces a new quirk, after ~2–3 h with no PR, or 3+ new passes without a PR; ship the servable part, scope out or honestly mark the rest.
- **Codex test, ready:** run Codex with `workspace-write` in a site worktree; first ten: fjej, moej, irfs, v724, 3rj, evsg, q2ph, b5td, 5p44 (8ukk already fixed; pick one more from the specced list).
- **Shipping:** Rufus authorised shipping to production without him after an agent review. Reviewer → integrator, per the skills.

## Shipped

- **v0.25.0** (ingest `fa353e4`): page-break continuations for numbered paragraphs (dev 3/82→1/82, held-out 21/282→17/282); floats and page markers (Jan 6 markers-wrong 39→10, blocks-wrong 30→3; 9/11 4→0); Jan 6 captions italic; Jack Smith letterhead footer; Lehman/PSI/Philip Morris burn-down (PM marker recall 21%→90%); junk autolinks (Leveson 416→182 external links, Challenger 11→0); launch-audit fixes (contrast, /search overflow, landmarks); release tooling (#299: `pnpm bump-pin`, `aggregate <ids>`, `worktrees prune`, D1 refusal on unknown usage, content-based staleness); ingest CI (#69). 12 reports republished. D1 74k read / 1.1k written.
- **v0.26.0** (ingest `2797eb2`): **Mueller Report live** (17 reports; grade ~B; 74 scanned pages honestly marked in PROCESSING.md); Columbia/Challenger/Deepwater burn-down (line-end hyphens, 236 Columbia subheads, division labels, transcript turns); Leveson (glued paragraphs 14→0, bare markers 39→6, running heads, thumb letters). 6 reports republished. D1 208k read / 4.5k written that day.
- Records: site #313, #316; reviews `review-pr299-2026-10-09.md`, `review-v0.25.0-2026-10-09.md`, `review-v0.26.0-2026-10-10.md`.

## Open PRs

- **Senate Torture Report: shipped in v0.27.0** (ingest `c7ebd54`; site #312, #318–#320; review `review-v0.27.0-2026-10-10.md`), live at grade B- with an honest limitations line. 18 live reports. Follow-up: jc46 (severed paragraphs), fgle remainder.
- **Worktrees pruned** (2026-10-10): ~65 removed; uncommitted derived diffs saved in `old-logs/2026-10-10/patches/`; branches kept. Only #294 and Duelfer worktrees remain.
- **Merge friction: merged** (site #317, ingest #78; review `review-pr317-2026-10-10.md`). Lessons are now one file per entry (`pnpm lessons new`; old `lessons.md` frozen), the corpus baseline is per report (`reports/corpus-baseline/<id>.json`), decision numbers are tested unique and the index is generated (`pnpm decisions`), and ingest pass names are one per line (rides the next release). The Pixel 7 landing-panel e2e that failed on main is fixed. Still open: r4q2 remainder (verify.sh naming the drifting repo; `delete_branch_on_merge`), `src/index.ts` exports one per line (wwmg).
- **marketing#2: merged** (strategy and drafts on marketing main; its decisions are still open beads below). The README there now records where the Bluesky app password lives (`~/.config/bluesky/reportsthatmatter`, path only).
- #294 marks cache (waits for Rufus), Duelfer #182 / ingest#22 (parked).

## Questions for Rufus (batched; recommended default in bold)

Launch-gating:
1. **Licences** (y2y8.9): code licence for the public repos — **MIT for code; text status statement per report (public domain for US federal works, OGL/Crown copyright for UK)**. Also confirm text licences for Jack Smith, Columbia, Deepwater, Philip Morris, and fix Leveson's README ("Public domain" → Crown copyright).
2. **Marketing decisions** (marketing#2 and beads): AI wording 0ino — **one plain note on /about saying what models did and did not touch**; personal accounts x76z — **launch from your personal accounts, org account carries the poster**; affected-community rules 63cd — **adopt the strategy's tier rule (no death/wrongful-prosecution report leads or demos anything; context lines; removal route)**; which report leads 80qf — **Challenger (40th anniversary 29 Oct) / US investigations image post**; site fixes gating launch hbnj — **source line on the paragraph view + contact/removal route; the rest after**; long-read z7u2 and dataset jh6z — **yes to both, after launch week**; Covid rehearsal smnx — **skip**.
3. **P0 ay85:** clean the posting queue before POSTER_LIVE (remove Tier C items, fix Hillsborough "96"→97, the Post Office wordmark card) — waits on 63cd; then an agent does it.
4. **y2t.1:** Search Console and Bing verification (Bluesky is done).
5. **#294 marks cache:** other readers see new marks 2–30 min late vs. a counts table — **accept the cache for launch** (protects D1 reads at launch traffic).

Reports:
6. **Done (shipped at B- on Rufus's go-ahead, 2026-10-10).** Was: CIA report at B- (PROCESSING.md states ~270 merged notes and ~84% of markers linked): **ship it in v0.27.0 with the honest limitations line** — it's the only edition of its kind that's readable and searchable, the remaining defects lose no text, and jc46 can follow; or hold until jc46 lifts it to B. Related: grade bar 38s.17 — **B is the publishable bar; don't show grades on the site yet**.
7. **Redactions** (one decision: oh89 + 8fsl, record 0020): **`[Redacted: <printed codes>]` where codes are printed (Mueller), `[Redacted]` where not (CIA)**.
8. **Captions not shown** (12s2, 0015) — **keep italic citable captions for now**; page order over note order (hcam, 0016) — **accept**; /full previews (yvfr, 0019) — **head-only page for preview crawlers on /full over ~3 MB**; /about copy (pbkk, 0017) and phone nav tap targets (nupj, 0018) — **agent's recommendations**.
9. **Philip Morris source** (ivg.5): CAP text measured at 0.8% WER vs. the amended opinion — **close enough; keep CAP**.

Older, still open: hosting move 2oz / 4g4d.1 (needs your wrangler login in `~/src/datopian`), page numbers qap0, annotation format i8en, reserved ids efso, vision words fc3x, dev/held-out 9u69, Mastodon account, real-phone highlighting check.

## Suggested next steps

1. Answer the batch above (30 min), then an agent applies the decisions (licence files, queue clean-up ay85, decision records' status).
2. Review + ship: merge-friction PRs; CIA if it reaches ~B (v0.27.0).
3. Codex trial on the first ten specced handoff beads; compare with a Claude run on similar beads.
4. Launch week: rehearse the poster dry-run (`docs/poster.md`), the pre-launch notes (marketing#2 `drafts/2026-10/prelaunch-notes.md`).
5. More reports: Infected Blood (gqsy.7; the source skill's cold run found official per-volume HTML — a hybrid candidate), Grenfell Vols 2–7 (gqsy.4.2).
