# Agent review: Thirlwall Volumes I-III, Covid Module 2 Volume II, Duelfer hybrid Volume I, Grenfell Phase 2 Volumes 2-7 (2026-11-13)

Reviewer: Opus agent. I did not touch production: no merge, no release or tag, no pin bump on any main, no publish, no deploy, no seed, no reindex, no `wrangler d1`, no `marks check --yes`. The only `publish-report` run was `--dry-run --offline`.

**Verdict: MERGE all four, with the ingest side shipped as one combined PR (ingest#97) that supersedes #90, #91, #93 and #94.** The four ingest PRs were built on `fffbd15` (before v0.35.0) and do not merge on today's main as they stand: two of them conflict with main's #92 in `src/typographic-headings.ts`. #97 is the four merged onto v0.37.0 with that conflict resolved and `dist/` rebuilt. With it, all four reports reproduce their report PRs' `full.md` byte for byte, and the other 20 reports' rendered text does not move, Leveson included. Release v0.38.0.

Rufus accepted before the review: Covid M2 at grade C, Duelfer at grade B (own-golden precision). This review checks correctness and composability only.

## Scope

| PR | branch | head reviewed | after review |
|---|---|---|---|
| ingest#91 Thirlwall | `thirlwallvols-1112` | b4d921d | superseded by #97 |
| ingest#90 Covid M2 | `covidv2-headings-1112` | 6f5255f | superseded by #97 |
| ingest#93 Duelfer | `duelfer-hybrid-1112` | 69058cf | superseded by #97 |
| ingest#94 Grenfell | `hyphen-majority` | 38c6ee7 | superseded by #97 |
| **ingest#97 combined** (new, mine) | `combined-1113` | **32b577f** | |
| uk-thirlwall-inquiry#3 | `thirlwallvols-1112` | e7ebf44 | unchanged |
| uk-covid-inquiry-module-2#2 | `vol2-1112` | 6078a07 | unchanged |
| us-duelfer-report#4 | `duelfer-hybrid-1112` | 2ac7da1 | unchanged |
| uk-grenfell-tower-inquiry#4 | `phase2-vols-2-7` | 6b3c5a0 | unchanged |
| site#431 Covid | `covid-m2-vol2-1112` | 7d26864c | unchanged |
| site#434 Thirlwall | `thirlwallvols-1112` | bf15b74e | unchanged |
| site#436 Grenfell | `grenfell-vols-2-7` | 7c43e6b3 | unchanged |
| site#435 Duelfer | `duelfer-hybrid-1112` | 20b620e7 | unchanged |

I pushed no fixups to the PR branches. The only fixes needed are the ingest merge resolution (in #97) and two site conflicts with main (recipes below).

## Trial

Logs are in `factory/logs/2026-11-13/`.

### Ingest

- **Worktree:** `ingest-review-1113` from origin/main `8ee298f` (v0.37.0). I merged #91, #90, #93 and #94 in that order.
- **Conflicts:** #91 conflicted with main in `src/typographic-headings.ts`. #90 conflicted with main and #91 in the same file, and in its `dist/` outputs. #93 merged cleanly. #94 conflicted in `dist/` only.
- **Resolution:** the union of the options. `semicolonWraps` (#91), `faceText` (#90), and `punctuationAside`/`keepDivisions` (main, #92) are all declared in the options type. All four are omitted from `Required<>` in `candidate()` and `layoutHeadings()`. Both per-line checks run: the `semicolonWraps` continue, then the `faceText` pattern. I then rebuilt `dist/`.
- **Checks:** `pnpm build && git status --porcelain -- dist` was empty. `pnpm test`: 75 files, 1,017 tests pass.
- **Pushed** as `combined-1113` → **ingest#97** (draft).

### Site

- **Worktree:** `rtm-review-1113`, branch `trial-review-1113`, from origin/main `ddfe5c90`, then `pnpm bootstrap`. I merged #431, #434, #436 and #435 in that order.
- **Report worktrees:** `rtm-review-1113-reports/<id>` for the four reports, each with its report PR merged into main.
- **Two conflicts, both with main** (the four site PRs do not conflict with each other):
  - **#436:** `reports/uk-grenfell-tower-inquiry/published-sections.txt`. Main added `chapter-4-the-development-of-the-regulatory-regime` (the owre re-sectioning) after the PR's base. Resolution: take the union of the lines, sorted with `LC_ALL=C sort -u`.
  - **#435:** `reports/anchor-budget.yaml`, at the end of the `uk-stern-review` block. Resolution: keep main's Stern `blocks-wrong: 1  # why: …gqsy.9.2…` line, then add the PR's `us-duelfer-report` block.
- **Shared derived files that merged without conflict:** `marketing/queue.yaml` (#431 and #436 each re-attributed their own items), `src/generated/editorial.ts`, `src/generated/cards.ts`, `src/generated/marks.ts`, `reports/registry.yaml`, `reports/pipeline.yaml`, and the quality and oracle budgets. `pnpm editorial --check` passes: the merged `editorial.ts` is current.

### Corpus effect of #97

**Whole corpus:** `RTM_REPORT_DIRS=… pnpm ingest try ../ingest-review-1113 --no-score` (log: `rtm-review-1113-try.log`). The site side was the merged trial.

- For the 20 non-target reports, rendered text is unchanged, no quality count moved, no finding appeared or vanished, and no page changed source or folio.
- **uk-leveson-inquiry does not move.** #94's year-range heading change is a no-op for Leveson now: since v0.35.0 (#92) the aggregate already reads "Prime Minister Blair: 1997 – 2007" and "Prime Minister Brown: 2007 – 2010". **No `pnpm aliases generate uk-leveson-inquiry` is needed** beyond the checklist's `--all`.
- #93's slash split changes only `losslessCheck` counts, which are not rendered. jan6, 911 and CIA showed no finding change.
- **Caveat about the log:** `try` branches each report from its *shared* checkout's HEAD, which is main without the report PRs. Its rows for the four target reports therefore compare the PR text against the old single-volume definition and show huge "losses". Ignore those rows. The next check covers the four target reports.

**Four target reports:** I linked #97 with `pnpm ingest link` into the trial and the four report worktrees, then ran `pnpm ingest run <id>` for each. Logs: `rtm-review-1113-run-<id>.log`.

- Each report repo's `full.md` came out **byte-identical** to its PR head (`git status` clean in all four).
- `pnpm ingest aggregate` of the four left the site trial unchanged.
- So main's v0.35.0-v0.37.0 changes (owre sections, efso reserved ids) do not change the text these PRs built.

### Checks on the trial (#97 linked)

Log: `rtm-review-1113-checks.log`.

- **Passing:** `pnpm prerender`, `pnpm typecheck`, `pnpm test`, `pnpm quality check`, `pnpm editorial --check`, `pnpm ingest check` (with `RTM_REPORT_DIRS`).
- **`pnpm corpus check`: 2 reports flagged. Both are expected, and accepting them is safe:**
  - **uk-grenfell-tower-inquiry:** `chapter-31-structure-and-governance` is now split out of `part-4-the-tenant-management-organisation`. This is main's owre rule ("a Part with its own introduction stands alone"), which the PR predates. It affects Volumes 2-7 text only, and no published id moves.
  - **uk-covid-inquiry-module-2:** `a-new-normal` and `chapter-11-government-decision-making-2` keep the same counts, but the id hashes differ. Two unpublished Volume II back-matter ids swap their suffix under efso's reserved ids: the trial has `e03296643-2` in a-new-normal and `e03296643` at the end of chapter 11 (2). The id *set* is unchanged: `published-ids.txt` does not change after `aliases generate`. No Volume I id moves.
  - After `pnpm corpus accept` of both, the check passes.
- **`pnpm aliases check`: failed before `aliases generate`, as expected.** The PRs predate main's `reserved-ids.txt` (efso, #448). Running `pnpm aliases generate` for the three updated reports and `seed-reserved` + `generate` for Duelfer made it pass.
  - **No published id moved.** Covid has 0 aliases and 0 unmatched. Grenfell has 2 aliases (the existing ones) and 0 unmatched. Thirlwall has 20 aliases and 1 unmatched, which is the PR's own `father-o-p-r-i` case (bead l68o).
  - The only generated changes are `reserved-ids.txt` (3 reports, plus a new one for Duelfer) and Grenfell's `published-sections.txt` gaining `chapter-31-structure-and-governance`.
  - The checklist's step 5 (`pnpm aliases generate --all`) does this at ship.
- **`RTM_REPORT_DIRS=… VERIFY_SHARED=1 ./scripts/verify.sh`:** **exit 0** (log: `rtm-review-1113-verify.log`). The one warning is `reports/pipeline.yaml` drift: `pnpm pipeline status` reads us-duelfer-report's shared checkout, whose `golden.yaml` is still empty. It clears once us-duelfer-report#4 merges.
- **`pnpm quality ratchet --dry-run`:** one budget would fall, uk-covid-inquiry-module-2 `contents-entry-without-heading` 124 → 109. Ratchet also re-sorts the `us-duelfer-report` block (#435 appended it at the end of `quality-budget.yaml`). I did not commit this to the PR branch: 109 is measured with main's code, and the branch alone measures 124. `pnpm ship` runs the ratchet. Leave it to that.
- **`pnpm ship --plan`** (log: `rtm-review-1113-ship-plan.log`):
  - It stops only on "not on main" and "tree not clean", as expected on a trial.
  - us-duelfer-report needs a pin bump. Its report repo holds v0.18.0 on main, and #4 sets v0.34.0.
  - The four target repos' text "differs from the site's copy" until they merge.
- **D1:** `pnpm publish-report us-duelfer-report --dry-run --offline` gives 37 objects, 3.0 MB. The first publish reindexes about 1,853 paragraphs. The other three reindex their new volumes: about 3,700 (Thirlwall), 1,400 (Covid) and 6,100 (Grenfell) rows. That is about 13,000 row writes in all, far under `PAID_DAILY`.

I did not read the rendered pages or check the editorial quotations in a browser (the brief was correctness and composability, fast). verify.sh's HTTP and browser checks pass for all four.

## Verdicts

| PR | head | verdict | reason |
|---|---|---|---|
| ingest#97 (combined) | 32b577f | **MERGE** (then release v0.38.0) | The four PRs on v0.37.0, with the `typographic-headings.ts` union resolved. Tests 1,017/1,017. The four reports' text is reproduced byte for byte, and the other 20 do not move. |
| ingest#91 | b4d921d | **close, superseded by #97** | Does not merge on main (typographicHeadings conflict with #92). |
| ingest#90 | 6f5255f | **close, superseded by #97** | Same. |
| ingest#93 | 69058cf | **close, superseded by #97** | Merges cleanly, but ships in #97. |
| ingest#94 | 38c6ee7 | **close, superseded by #97** | Conflicts in `dist/` only. Ships in #97. Its Leveson note is now moot. |
| uk-thirlwall-inquiry#3 | e7ebf44 | **MERGE** after v0.38.0 | Output byte-identical under #97. Pin bumped by `pnpm ship`/`bump-pin`. |
| uk-covid-inquiry-module-2#2 | 6078a07 | **MERGE** after v0.38.0 | Same. |
| us-duelfer-report#4 | 2ac7da1 | **MERGE** after v0.38.0 | Same. Its `package.json` pins v0.34.0, which the pin bump overwrites. |
| uk-grenfell-tower-inquiry#4 | 6b3c5a0 | **MERGE** after v0.38.0 | Same. |
| site#431 Covid | 7d26864c | **MERGE** (1st) | Clean on main. Corpus accept for the suffix swap (above). |
| site#434 Thirlwall | bf15b74e | **MERGE** (2nd) | Clean. |
| site#436 Grenfell | 7c43e6b3 | **MERGE after the published-sections conflict fix** (3rd) | Conflicts with main in `published-sections.txt` (union). Corpus accept for the chapter-31 split. |
| site#435 Duelfer | 20b620e7 | **MERGE after the anchor-budget conflict fix** (4th) | Conflicts with main in `anchor-budget.yaml` (keep both). Needs `seed-reserved`. |

## Found only by the trial merge

1. **The four ingest PRs do not compose on main as built.** Their base `fffbd15` predates v0.35.0-v0.37.0. #91 and #90 each add an option to the same `Omit<>` lists that #92 changed. Merging all four onto main in any order leaves a conflict in `src/typographic-headings.ts`. Resolving it by hand also drops the `/**` opener of `faceText`'s doc comment, which then fails the build (`TS1161`). #97 carries the correct resolution.
2. **The Leveson heading change claimed by #94 no longer happens.** Since v0.35.0 the Leveson report already keeps those years. No Leveson aliases step is needed.
3. **efso reserved ids:** none of the four site PRs updates `reserved-ids.txt`, and Duelfer has none. `aliases check` fails until `aliases generate --all` and `seed-reserved us-duelfer-report` run. That is the normal ship step, and not a moved id.
4. **owre re-sections Grenfell Volume 4's Part 4** (chapter-31 stands alone). Corpus accept is needed.

## Integrator steps

1. **Ingest:**
   - In `~/src/reportsthatmatter/ingest`, merge **ingest#97** (`gh pr ready 97 && gh pr merge 97 --squash`).
   - Close #90, #91, #93 and #94 with "superseded by #97".
   - From ingest's `main`: `pnpm release 0.38.0`. The latest tag is v0.37.0, and this is a minor release: new opt-in passes and options.
2. **Report repos:** merge uk-thirlwall-inquiry#3, uk-covid-inquiry-module-2#2, us-duelfer-report#4 and uk-grenfell-tower-inquiry#4 into each repo's `main`.
3. **Site integration branch** (in a worktree from origin/main), merging in this order:
   - `git merge --no-ff origin/covid-m2-vol2-1112`: clean.
   - `git merge --no-ff origin/thirlwallvols-1112`: clean.
   - `git merge --no-ff origin/grenfell-vols-2-7`: conflict in `reports/uk-grenfell-tower-inquiry/published-sections.txt`. Strip the conflict markers, then `LC_ALL=C sort -u -o <file> <file>` (keeps both sides' lines; step 4's `aliases generate --all` rewrites it anyway).
   - `git merge --no-ff origin/duelfer-hybrid-1112`: conflict in `reports/anchor-budget.yaml`. Keep HEAD's `uk-stern-review` `blocks-wrong: 1  # why: …` line and append the PR's `us-duelfer-report:` block (markers-wrong 37, markers-stacked 0, pages-unmarked 0, blocks-wrong 29).
   - **Generic conflict recipe** for any other conflict in a derived file if main moves again:
     - `marketing/queue.yaml`: take main's, then `pnpm posts`.
     - `src/generated/editorial.ts`: take main's, then `pnpm prerender && pnpm editorial`.
     - `src/generated/cards.ts` and `src/generated/marks.ts`: take main's, then `pnpm cards` / `pnpm marks`.
     - `reports/corpus-baseline/<id>.json`: take main's, then `pnpm corpus accept <id>`.
     - `reports/registry.yaml`, `reports/pipeline.yaml` and the budget files: keep both sides' entries. They are per-report keys.
4. **Same branch:**
   - `pnpm bump-pin 0.38.0`. This bumps the site and every report repo, us-duelfer-report included, from v0.18.0. Pass `--shared` if you use the shared checkouts.
   - `pnpm ingest check` should show no movement in any report.
   - `pnpm ingest aggregate --shared`.
   - `pnpm aliases seed-reserved us-duelfer-report`, then `pnpm aliases generate --all`. Expect 0 new aliases and only `reserved-ids.txt` changes, plus Grenfell's `chapter-31-structure-and-governance` section.
   - `pnpm prerender && pnpm aliases check`.
   - `pnpm corpus check`, then `pnpm corpus accept uk-covid-inquiry-module-2 uk-grenfell-tower-inquiry` after reading (above).
   - `pnpm quality ratchet` (Covid 124 → 109).
   - `pnpm editorial`, `pnpm cards` and `pnpm posts`.
   - `VERIFY_SHARED=1 ./scripts/verify.sh`.
   - Commit, open the PR, merge it.
5. **Before the publish**, as #434 asks: `pnpm marks check --all --baseline <main build> --candidate assets/generated --yes`, including Thirlwall's `--refresh`.
6. **Ship:** `pnpm ship` from site main. It publishes four reports: three that already exist (new volumes) and us-duelfer-report (new, 37 objects). The reindex is about 13k D1 row writes.
7. **Grades:** record them with `pnpm grade --record`. Covid M2 is C and Duelfer is B, both accepted by Rufus. #431 notes that `pnpm score` cannot read a two-PDF layout (qy4n), so record grades by hand there.
8. **On ship:** close reportsthatmatter-gqsy.16.7, reportsthatmatter-nshl, reportsthatmatter-ivg.4 and reportsthatmatter-gqsy.4.2.

**What moves at release:**

- The four target reports gain their new volumes.
- No published paragraph id moves in any report.
- Covid Volume I has one text repair (6.82). Thirlwall Summary has three rejoined hyphens. Grenfell Volume 1 has one ("co-ordinator").
- Thirlwall: the old alias key `father-o-p-r-i` is now a Volume I paragraph's id (l68o, known).
- No other report moves. Leveson does not move either.

## Beads

Verdict notes are appended to gqsy.16.7, nshl, ivg.4 and gqsy.4.2. No new beads: every finding is resolved in #97 or is a ship step.

## Getting better faster

- **What slowed me down:** `pnpm ingest try` builds each report from the *shared* checkout's HEAD (`manifestIds()` in `scripts/ingest/try.ts`), ignoring `RTM_REPORT_DIRS`. When the report-repo PRs are not merged, it reports false whole-volume losses, and its "before" score crashes on an export the pinned ingest lacks. I had to fall back to `link` + `run` per report.
- **Proposal:** add `pnpm ingest try --report-dirs` (or honour `RTM_REPORT_DIRS`), so that a reviewer of a report-plus-ingest batch can run one command.
- **Second point:** four stage agents built on a base three releases old. The stage skill should rebase onto origin/main (`git merge origin/main`, rebuild `dist`) before opening the ingest PR, so that conflicts like the `Omit<>` lists surface in the stage, not in review.
