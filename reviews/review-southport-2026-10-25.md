# Agent review of the Southport Inquiry Phase 1 Volume 1 report (2026-10-25)

Reviewer: Sonnet agent. I did not touch production: no `wrangler d1`, no `publish-report --yes`, no seed, no reindex, no deploy, no merge, no release, no tag. `publish-report --dry-run --offline` and `pnpm ship --plan` only. I pushed no fixups.

**Verdict: SHIP both, report PR first.** Merge reportsthatmatter/uk-southport-inquiry#1 (head **af65897b**), then site #409 (head **0d56bb0e**), then `pnpm ship`. No pin bump (both at ingest v0.33.0). Only the new report moves.

## Scope

| PR | Branch | Head at start | Head after review |
| --- | --- | --- | --- |
| site #409 | `southport-vol1-1024` | 0d56bb0e | 0d56bb0e (no fixups) |
| uk-southport-inquiry#1 | `vol1-1024` | af65897b | af65897b (no fixups) |

Base: site main `36eddff5`. The report repo's main is a README stub (`912df6e`).

## Trial

- Site worktree `~/src/reportsthatmatter/rtm-reviewsouthport-1024` (branch `trialsouthport-1024`): origin/main + `git merge --no-ff origin/southport-vol1-1024`, no conflicts; `pnpm bootstrap` ok. Report worktree `~/src/reportsthatmatter/rtm-reviewsouthport-1024-reports/uk-southport-inquiry` moved to `origin/vol1-1024` (`pnpm ingest worktrees` makes it from main), `CI=true pnpm install` there (pin v0.33.0).
- `RTM_REPORT_DIRS` set to the reports dir. Logs in `~/src/reportsthatmatter/factory/logs/2026-10-24/`:
  - `pnpm typecheck` pass; `pnpm test` 1474 passed, 1 skipped (`southport-review-test.log`).
  - `pnpm quality check`: all 22 reports within budget; `pnpm quality ratchet --dry-run`: nothing to lower, so no ratchet commit.
  - `pnpm corpus check`: 22 reports unchanged (the 21 others did not move). `pnpm aliases check`: southport 1029 ids, 0 unmatched. `pnpm editorial`: 22 files, quotes verbatim, `src/generated/editorial.ts` kept (not stale).
  - `VERIFY_SHARED=1 ./scripts/verify.sh`: **All checks passed** (`rtm-reviewsouthport-1024-verify3.log`; verify.log and verify2.log are earlier runs that failed only because my report worktree had no `node_modules` / aggregate refused the shared checkouts). `git status --short reports/` empty afterwards.
  - Re-ingest: `pnpm ingest run uk-southport-inquiry` reproduces the report repo's `full.md` byte for byte, equals the site copy, report repo clean (`southport-review-reingest.log`): 96.3% of source words retained, 1 output word not in source (a bare number), no severed sentences.
  - `pnpm ship --plan` (scratch state) ok; `publish-report uk-southport-inquiry --dry-run --offline`: 46 objects, 1.8 MB.
- No GitHub checks are configured on either branch.

## Affected-community checks (decision 0026, bead 6j4h)

- **Posting queue:** `pnpm posts --check` passes and its output never mentions southport. `pnpm posts --report uk-southport-inquiry` gives "43 item(s) in the queue (0 new)", the same 43 as main. `marketing/queue.yaml` is not in the PR. No highlight carries `card: true` (the pipeline row waives `editorial.card-highlights` to 6j4h). The registry gives a `context_line` and `affected_community: true`; `tests/posts.test.ts` is updated to expect it.
- **Landing page** (`pnpm dev`, `/reports/uk-southport-inquiry`, read as a cold reader): hero is the Town Hall, no photograph of the venue or attack. The page opens with who, what, when and where in a sentence, then Background names the three girls once by first name as the Foreword does, never names the perpetrator, defines "AR" before using it, says it is not the court's liability finding, and says Volume 2 is not on the site. The eight findings are balanced (parents, venue security from the families' side, preventability cited where stated). Nothing is quoted from Chapter 3 or the account of the attack. Dates, the 52-year minimum term, the three Prevent refusals, the missing parental controls, "wholly unpredictable" and the 24 numbered recommendations all match `full.md`. The 67-recommendation figure, the Phase 2 dates and the government response rest on the YAML header's cited sources; I did not re-fetch them (see Not checked).
- **Section pages** read: `/chapter-1-fundamental-problems` (paragraphs 3 to 19, notes, folios, the Chair's "single most important conclusion" para 19) and `/chapter-6-online-harms` (paragraph 2 onward, notes in order, sensitive-content notes shown as printed). Numbering, notes and page markers are clean; `/foreword` returns 200. No oddities.
- Imagery rights are recorded in `docs/design/2026-09-12-imagery/sources.yaml` (plate: the cover's title type only, OGL v3) and `2026-09-27-hero/sources.yaml` (Superchilum, CC BY-SA 4.0).

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| uk-southport-inquiry#1 | SHIP (merge first) | Reproducible ingest, all gates pass, rights and PDF hash recorded. |
| site #409 | SHIP (merge second, after #1) | Trial merge clean, all checks and full verify pass, queue untouched. |

## Findings

No blocking findings and no fixups. Notes, none needing action before ship:

1. Known and beaded by the author: chronology anchors (mws2), p.236 order (k2ro), blank header rows in the chronology tables (ucp9), no quality signal yet for tag-paragraph oversplit (x6gu), adapter lift into @rtm/ingest (pbou). Anchors 97.1% right of located, 7 wrong, all chapter openings.
2. Share, Copy quote and Save controls render on this report's pages as on every report. They do not post anything and the queue excludes the report; whether an affected-community report should offer them is a question for 6j4h, not this ship.
3. The shared checkout `~/src/reportsthatmatter/uk-southport-inquiry` holds only a README stub on `main`. After merging #1 the integrator must `git pull --ff-only` there before `pnpm ship`, or ship reads a stale checkout.

## What moves

Only the new report `uk-southport-inquiry` (1029 citable ids, 44 sections, 260 pages). `pnpm corpus check` reports 22 unchanged (21 existing reports plus the new baseline). No ids lost. Other site changes: manifest, registry, pipeline row, budgets (quality, anchor, oracle, score-sets), one line in `scripts/score/reference.py`, generated `affected.ts`, `cards.ts`, `editorial.ts`, `marks.ts`, a default card image, hero and mark images, two lessons, a catalogue paragraph.

## D1 estimate

Not measured against production (I did not run `--dry-run --yes`). Offline dry run: 46 objects, 1.8 MB, 1 row write for the publish itself. The reindex for a new report writes one row per paragraph (about 1,029 ids, so roughly 1,000 to 3,000 rows by comparison with Thirlwall's 474 ids), which is small against the Workers Paid budget (`PAID_DAILY`). `pnpm ship`'s d1-estimate step will confirm and refuses on its own if it cannot read usage.

## Found only by the trial merge

Nothing. The merge was clean and no derived file conflicted.

## Integrator steps

1. `gh pr merge 1 -R reportsthatmatter/uk-southport-inquiry --squash` (confirm head is still af65897b first; if it moved, re-review the delta).
2. `git -C ~/src/reportsthatmatter/uk-southport-inquiry pull --ff-only` (the shared checkout is a README stub until then).
3. `gh pr view 409 --json headRefOid` (expect 0d56bb0e or a rebase of it) then `gh pr merge 409 --squash` in the site repo. No pin bump: both at v0.33.0.
4. In the site checkout on main: `git pull --ff-only`, `pnpm bootstrap`, `pnpm ship --plan --state build/ship-plan.json`; expect 0 repos needing a pin bump and the southport text equal to the site copy.
5. `pnpm ship --shared`, and `--ack` the baseline, corpus and editorial gates only for uk-southport-inquiry (a new baseline, the new editorial file). The report is never published, so ship deploys before reindex. Then `--yes` for the production steps, which include `pnpm seed-highlights`.
6. After ship, check `pnpm posts --check` still queues nothing for southport and that `marketing/queue.yaml` is unchanged.
7. Commit the `--record` outputs in a PR, add the changelog entry, then close reportsthatmatter-gqsy.15 and .15.1 to .15.5. Leave 6j4h, mws2, k2ro, ucp9, x6gu and pbou open.

## Beads

Noted on gqsy.15 (verdict and this file path). No new beads: every defect found is already beaded by the author.

## Not checked

The external facts in the editorial header (Inquiry website dates, the 2 July 2026 government response, 67 recommendations) and the Wikimedia photo credit were not re-fetched; they rest on the author's cited sources. I did not read the PDF page by page against `full.md`; the fidelity evidence is the oracle, golden 7/7 and the 30-break adjudication, which I read as reported and which `ingest check` and verify reproduce.

## Lessons

The two lessons in the PR (GOV.UK HTML `<p>` is not a paragraph; tagged-PDF paragraphs fix edition splits) are enough. One of mine: `pnpm ingest worktrees` creates the report worktree from `origin/main`, so for a not-yet-merged report repo you must check out the PR branch in it by hand.

## Getting better faster

- Slowed by two false verify failures: a report worktree without `node_modules`, and a plain `verify.sh` refusing shared checkouts. `pnpm ingest worktrees` could take `--branch` and run the install.
- A `pnpm posts --check --report <id>` that prints "0 candidates, 0 queued, N excluded as affected-community" for one report would have made the no-queue check one line.
- Proposal: add the shared-checkout-is-a-stub warning to `pnpm ship --plan` so step 2 above cannot be forgotten.
