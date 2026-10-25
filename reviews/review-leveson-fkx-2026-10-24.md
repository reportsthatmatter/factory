# Review: Leveson facingPageMargins (reportsthatmatter-fkx), 2026-10-25

Reviewer: Claude (Opus 5.5) agent. I did not touch production: no merge, tag, release, deploy, publish, reindex or seed; no `wrangler d1 execute --remote`. The one production read was `pnpm marks check uk-leveson-inquiry --yes` (reads stored marks, writes nothing).

**Verdict: MERGE all three, in the order ingest#88 → release v0.32.0 → uk-leveson-inquiry#12 (after its pin bump) → site#398. One non-blocking regression filed (reportsthatmatter-joxf).**

## Scope

| PR | branch | head at start | head after review |
|---|---|---|---|
| [ingest#88](https://github.com/reportsthatmatter/ingest/pull/88) | `leveson-quality-1024` | `18d8e23` | `e5d74ca` (one reviewer fixup, no output change) |
| [uk-leveson-inquiry#12](https://github.com/reportsthatmatter/uk-leveson-inquiry/pull/12) | `leveson-quality-1024` (base `master`) | `a517067` | `a517067` |
| [reportsthatmatter#398](https://github.com/reportsthatmatter/reportsthatmatter/pull/398) | `leveson-quality-1024` | `ed5623e7` | `ed5623e7` |

Site origin/main at review: `9853540f` (#392 landed after the PR's base; merges cleanly, see below).

## Trial

- Site: `~/src/reportsthatmatter/rtm-review398-1024`, branch `trial398-1024` from origin/main `9853540f`, `git merge --no-ff origin/leveson-quality-1024`: clean (auto-merged `marketing/queue.yaml` with #392's 164 new lines). `pnpm bootstrap`.
- Ingest: `~/src/reportsthatmatter/ingest-review88-1024`, branch `trial88-1024` from origin/main `33f139d` (v0.31.0) + #88: `pnpm build` leaves `dist` clean; `pnpm typecheck` clean; `pnpm test` 951/951. Same at `e5d74ca` (`~/src/reportsthatmatter/ingest-fkxnit-1024`).
- Report repo: `~/src/reportsthatmatter/rtm-review398-1024-reports/uk-leveson-inquiry`, branch `trial12-1024` from origin/master `335c1af` + #12.
- **Corpus effect** (`pnpm ingest try ../ingest-review88-1024 --no-score`, all 20 reports, log `factory/logs/2026-10-24/rtm-review398-1024-try-all.log`): all 19 other reports "rendered text unchanged", no quality signal moved, no finding appeared or vanished, no page changed source. Leveson shows only the expected drift (repo HEAD is master, site copy is the PR's). Opt-in claim holds.
- **Reproducibility**: with ingest at `18d8e23` and again at `e5d74ca` linked into the report worktree, `pnpm ingest run uk-leveson-inquiry` reproduces the PR's `full.md` byte for byte; site copy equals the report repo's.
- Checks on the site trial (log `factory/logs/2026-10-24/rtm-review398-1024-checks.log`): `typecheck`, `test`, `quality check`, `corpus check`, `editorial --check`, `aliases check`, `posts --verify` all exit 0; `quality ratchet --dry-run`: "Nothing to lower". `pnpm marks check uk-leveson-inquiry --yes`: 51 references replayed, none stops anchoring (3 aliased: the three repointed highlights).
- `VERIFY_SHARED=1 ./scripts/verify.sh` with `RTM_REPORT_DIRS` = the trial report dir and ingest `e5d74ca` linked: exit 0 (`factory/logs/2026-10-24/rtm-review398-1024-verify.log`). `git status --short reports/`: clean.
- `pnpm ingest verify uk-leveson-inquiry` (linked): exit 0; golden 2/8 match, 6 known failures, all r0w (subheads) — vol i p.19 now passes, p.21 xfails only on its heading (`factory/logs/2026-10-24/rtm-review398-1024-leveson-verify.log`).
- `pnpm ship --plan --state build/review-ship-state.json`: exit 0 (`…-ship-plan.log`). `pnpm publish-report uk-leveson-inquiry --dry-run --offline`: 85 objects, 21.6 MB.

## Verdicts

| PR | verdict | reason |
|---|---|---|
| ingest#88 | MERGE | Opt-in, no other report moves; pass is small and tested; reviewer fixup pushed. One quotation regression on vol iv annex pages, non-blocking (joxf). |
| uk-leveson-inquiry#12 | MERGE after the pin bump to v0.32.0 | On its v0.31.0 pin `ingest.ts` cannot import `facingPageMargins`; master would not build. |
| reportsthatmatter#398 | MERGE (then ship before the 2026-10-26 Leveson post) | Checks green on the trial merge with current main; editorial and queue repoints verified against the PDF. |

## Author's claims, checked

- **Oversplit flags real / cause**: spot-checked against `pdftotext -layout`: vol i p.21 ("I selected" / "Robert Jay QC" now one paragraph, 1.4 / 1.5 / 1.6 / 1.7 whole), vol ii p.21 (3.6 at col 15 on an odd page, wrongly quoted before, prose now). The flagged shape (capital-opening lines cut, the gutter's 8 vs 12) matches my own count of numbered-opener columns per parity: vol i even 8 / odd 12, ii 8/12, iii 7/12, iv 8/12. I did not redo the 25-flag sample.
- **Other 19 byte-identical**: confirmed by my own `try` run (above).
- **Golden vol i p.21 fails on v0.31.0**: master's `full.md` has no "I selected Robert Jay QC" (it is cut across two paragraphs), so its `must_contain` fails there; passes now except the xfailed heading.
- **Budget raises**: `quote-share-parity` 1 → 2 (20.9% even vs 19.5% odd, gap 1.4; a side-of-spread defect reads ~90): justified. `blocks-wrong` 3 → 4 (recommendations box over printed pp.1112-1113): justified, the three old ones unchanged.
- **Editorial repoints land on the same words**: each quote is verbatim in its new paragraph, and the PDF confirms the join is the real paragraph: "This legislative proposal does no more…" and "This is not, and cannot be characterised as, regulation of the press." are inside 7.4 (vol iv PDF p.316); "First, the scale of the problem…" inside 1.5 (vol ii PDF p.556); "Suffice to say…" inside 7.10; "The fact remains that the NoTW hacked…" inside 2.22. `pnpm editorial --check` clean.
- **Queue items still anchor**: `pnpm posts --verify` clean on the trial merge (with #392's items); the two Leveson items keep `scheduled` 2026-10-26 and 2026-11-06; both `?p=` ids exist after the re-ingest, and the old ids are aliased.

## Findings, by severity

1. **Not fixed, non-blocking: quotations turned into prose on vol iv's privacy-law annex (reportsthatmatter-joxf).** In vol iv PDF pp.373-483 recto and verso run the other way from the rest of the volume (odd pages at 8). Pages with numbered openers take their own margin and are fine. Pages with none take the odd side's 12. On pp.413-431 the openers are "3.115 In McKennitt…", with one space after a three-digit number, which `NUMBERED_OPENER` (2+ spaces) never reads. So text sits at 6, quotations at 13, the margin is 12, and each quotation reads as prose. My count: 26 whole quotation blocks became prose. 8 of them are real quotations: "stories may be in the public interest…" (p.415), "It is true that, until early 2010…" (p.419), "… the discussion or criticism of sexual conduct…" (p.477), and others on printed pp.1884-1894. The other 18 are recommendation boxes, lettered items and furniture, which are correctly prose now. In the other direction, about 85k words went prose → quote and 21k quote → prose, and samples of both are corrections. I tried two fixes and pushed neither, because each regresses elsewhere. (a) Take a no-opener page's commonest indent when it is ≥3 left of its side: this restores these quotes but quotes the bullet continuations in the vol iv evidence-base annex (pp.485-507). (b) Take the nearest same-parity page's own margin: this restores them but turns about 1,500 words of quotation on printed pp.388-398 and 1204-1208 into prose. The bead names a candidate: read one-space three-digit openers.
2. **Not fixed, minor, existing class r0w: stacked subheads now weld into one line.** Pairs of short subheads that were separate paragraphs now join. Examples: "5. The problems of friendship Andy Hayman", "Who should adjudicate on complaints? Internal complaint handling", "Unlawful or unethical acquisition of private information Phone hacking", "Ombudsman History and background". About 20 such pairs. They were already paragraphs, not headings (r0w). Noted on r0w.
3. **Inconsistent, minor:** boxed "I recommend…" recommendations are quotations in vol iii (e.g. printed 1111, 1452) but prose in vol iv (1754-1796). Neither is a quotation, so vol iv's reading is the right one. Folded into r0w/joxf scope as an observation, no bead.
4. **Single numbered opener inside a quotation misleading `openerMargin`: none found.** I listed every page whose own opener margin differs from its side by ≥3 (172 pages, all four volumes). Every one is a real paragraph opener: parity-flipped sections (vol i pp.207-256, vol iv pp.373-483) or the column-15 pages such as vol i p.25. The regex allows at most 8 leading spaces, and quotation insets sit at 13+, so a quoted "2.1" does not match. Single-opener pages (vol i p.18, vol iii p.437) are real openers.
5. **Fixed on the branch (`e5d74ca`), no output change:** removed two needless `as never` casts in `facingMargins`. `pageMargin` now calls `openerMargin` (the counting loop was duplicated). The `facingPageMargins` doc said a page's own margin counts only "where that is one of its volume's two sides", but the code takes it whatever the column (vol i p.25 at 15): the doc now says so, and that the pass overrides `geometry("per-page")` and `shiftedPages` when both are declared. Re-ingest with `e5d74ca` is byte-identical.

## What moves

Only uk-leveson-inquiry: paragraphs 12,695 → 10,230, quotes 2,036 → 2,600, headings 88 → 100 (11 chapter openers split out), ids lost 429 / new 2,890 (aliases 3,499, unmatched 2,390 → 3,391, mostly paragraphs now quotations; author filed a bead). Editorial: 4 cited ids repointed. Queue: 2 Leveson items repointed. Cards: 2 regenerated.

## Found only by the trial merge

#392 (main) added 164 lines to `marketing/queue.yaml`; git merges it with #398's repoints cleanly and `pnpm posts --verify` is clean on the merge. `marketing/queue.yaml` is poster-live's file: tell them the two Leveson items' ids changed.

## D1 estimate

One report republished (Leveson, 85 objects). Reindex is incremental over about 3,500 changed paragraphs, so a few thousand row writes. Get the exact figure from `pnpm publish-report uk-leveson-inquiry --dry-run --yes` at ship time (the offline run cannot read the diff).

## Integrator steps

1. `bd dolt pull`; `pnpm d1-usage`. Check that origin/main of the site is still `9853540f` (otherwise use the new head as `--old-ref`), ingest#88's head is `e5d74ca`, #12's is `a517067` and #398's is `ed5623e7`. Check `git -C ~/src/reportsthatmatter/ingest tag -l 'v0.3*'`: the latest tag is v0.31.0, so this release is **v0.32.0**. If another tag has landed, take the next minor.
2. **Ingest**: squash-merge ingest#88 on GitHub (based on current main, no conflicts). In an ingest worktree at the new main: `CI=true pnpm install && pnpm check-dist && pnpm typecheck && pnpm test` (expect 951), then `pnpm release 0.32.0 --dry-run`, then `pnpm release 0.32.0`. Then `git -C ~/src/reportsthatmatter/ingest pull --ff-only`.
3. **uk-leveson-inquiry#12**: in a worktree on `leveson-quality-1024`, set `"@rtm/ingest": "github:reportsthatmatter/ingest#v0.32.0"`, run `pnpm install --no-frozen-lockfile`, and check that `pnpm-lock.yaml` names the v0.32.0 commit. From a site worktree run `RTM_REPORT_DIRS=<parent> pnpm ingest run uk-leveson-inquiry`. Expect no change to `full.md` or `baseline.json`. Commit "Bump @rtm/ingest to v0.32.0", push, merge #12 (merge commit), then `git -C ~/src/reportsthatmatter/uk-leveson-inquiry pull --ff-only && pnpm -C ~/src/reportsthatmatter/uk-leveson-inquiry install`.
4. **Other report repos' pins**: from the shared site checkout, `pnpm bump-pin 0.32.0 --shared --dry-run`, then `pnpm bump-pin 0.32.0 --shared` (or by hand: in each shared checkout on main/master set `#v0.32.0`, `pnpm install --no-frozen-lockfile`, commit "Bump @rtm/ingest to v0.32.0", push). `pnpm ingest preflight` should list every repo at 0.32.0. None of them should move: they do not declare the pass.
5. **Site**: merge #398 (merge commit; it merges cleanly with `9853540f`). Pull the shared checkout `--ff-only`, set the site pin to `#v0.32.0`, run `CI=true pnpm install --no-frozen-lockfile`, commit "Bump @rtm/ingest to v0.32.0", push, then `pnpm prerender`.
6. `pnpm ship --plan`: expect 0 repos needing a bump, every repo clean, every text equal to its site copy.
7. `VERIFY_SHARED=1 pnpm ship --shared --old-ref 9853540f`. Expected stops:
   - **baseline**: nothing moves. Leveson's baseline is the PR's, and the other 19 are byte-identical.
   - **aliases**: committed in #398.
   - **corpus, editorial, cards, posts**: these pass.
   - **ratchet**: nothing to lower.
   - **status / d1-estimate**: exactly 1 report to publish (uk-leveson-inquiry). Stop if it lists more.
8. **Timing**: queue item `uk-leveson-inquiry:7-4-order-give-effect:6f4f7e41` is scheduled **2026-10-26**. Ship before it posts, so its `?p=7-4-order-give-effect` link resolves on production. If the ship slips, ask poster-live to hold that item. The old id stays aliased after ship either way.
9. Close on ship: reportsthatmatter-fkx. Leave reportsthatmatter-joxf and r0w open.

## Beads

- Filed: **reportsthatmatter-joxf** (bug, stream:quality, level:judgement, discovered-from fkx): vol iv annex quotations read as prose; both measured fix variants and their side effects are in the bead.
- Noted: fkx (verdict and this file), r0w (stacked subheads welded).

## Lessons

- A quote-flip diff by page (words that were quotation and now prose, and the reverse, aligned per `%%page%%`) found the one regression that the oracle totals hid behind a 585-flag net gain. It took about 30 lines of Python. `pnpm ingest try` could print it per report.

## Getting better faster

- Slowed by: `try` holds the site worktree for about 15 minutes, so I could not run Leveson experiments beside it; a second site worktree would have cost a bootstrap.
- Would have caught it: a per-page quote↔prose flip list in `try`'s join/move output, beside "joined/split".
- Proposal: add that flip list (top pages each way, with excerpts) to `pnpm ingest try` (bead to file by the integrator if agreed).
