# Review: UK Covid-19 Inquiry Module 2, Volume I (ingest #83, report repo #1, site #367), 2026-10-25

Reviewer: Sonnet agent. I did not merge, release, tag, deploy, publish, seed or touch production D1 (no `--remote`, no `publish-report` without `--dry-run --offline`, no `ship` beyond `--plan`). I killed only the dev server I started. The author's worktrees `ingest-covidm2-1024` and `rtm-covidm2-1024` were not touched.

**Verdict: MERGE all three, in the order below, after ingest v0.29.0 is released. No blockers; no fixups were needed. The key claim holds: the other 18 reports are byte-identical under ingest #83.**

## Scope

| PR | Branch | Head reviewed | After fixups |
|---|---|---|---|
| ingest #83 (draft) | covidm2-passes-1024 | 362a2146 | unchanged |
| uk-covid-inquiry-module-2 #1 | vol1-1024 | 7fb8cd04 | unchanged |
| site #367 (draft) | covid-m2-vol1-1024 | 7abc3e6b | unchanged |

Bases: ingest main c4d498c (v0.28.0 plus the LICENSE commit; `src/` identical to the v0.28.0 tag), site main 5e684489.

## The byte-identity proof (ingest #83 changes a shared default)

The PR has three changes. `labelWraps` is opt-in (needs `faces`). The restart scan in `splitFootnoteBlock` needs `sequencedNoteOpenings` and `footnoteRestarts` together; the only reports declaring either are Leveson and Litvinenko (restarts only) and Jack Smith, Mueller, Hillsborough, Lehman, CIA (sequenced only), so none is reached. The third is the shared default: `typographicHeadings` no longer cuts a heading after a bare paragraph label. Measured two ways:

1. `pnpm ingest try ../ingest-review367-1025 --no-score` from `rtm-review367-1025` (base: the pinned v0.28.0), all 18 reports: "rendered text unchanged" for every one; "no quality signal moved"; no oracle, anchor or quality-excerpt finding appeared or vanished; no page changed source or printed-number read. Log: `factory/logs/2026-10-25/review367-ingest-try-vs-pin.log`. (`--no-score` because unchanged text cannot move a score.)
2. Independent: a second site worktree `rtm-indep-review367-1025` and 18 clean report worktrees (`rtm-indep-review367-1025-reports/`, branch from origin/main, or HEAD for Columbia and Leveson, which use master). `pnpm ingest link` to `ingest-base367-1025` (origin/main), `pnpm ingest run <id>` for each, copy `full.md`; then link `ingest-review367-1025` (PR head), rerun, copy. Result: all 18 `cmp`-identical between base and PR, and `git status` in every report worktree was clean after both runs (so both equal the committed `full.md`). Sizes range 379,895 bytes (Chilcot) to 6,988,452 (Leveson).
3. Control that the link bites: on the Covid report the base ingest gives 3,160 notes and a `full.md` differing by 84 insertions and 204 deletions; the PR ingest gives 3,236 notes and reproduces the committed file byte for byte.

## Trial

- Ingest: `~/src/reportsthatmatter/ingest-review367-1025` (branch trial367-1025 at PR head) and `ingest-base367-1025` (origin/main). `pnpm build && git status --porcelain -- dist`: empty. `pnpm test`: 67 files, 939 passed. Merging ingest #82 (edition-corrections-1024) on top: clean merge, `pnpm build` leaves `dist/` clean, 68 files and 943 tests pass.
- Report repo: `rtm-review367-1025-reports/uk-covid-inquiry-module-2` at 7fb8cd04, linked to the PR ingest: `pnpm ingest run` writes a `full.md` identical (`cmp`) to the one in the repo and to the one committed in site #367 (1,254,160 chars, 460 pages, 3,236 notes, 181 headings, 45 auto-fixes); `git status` clean.
- Site: `rtm-review367-1025`, branch trial367-1025 from origin/main, `git merge --no-ff origin/covid-m2-vol1-1024` (no conflicts), `pnpm bootstrap` (19 reports), PR ingest linked into the site and the report worktree. Logs in `factory/logs/2026-10-25/`:
  - `pnpm typecheck`: exit 0. `pnpm test`: 85 files, 1,349 passed, 1 skipped.
  - `pnpm quality check`: "All 19 reports within budget". `pnpm quality ratchet --dry-run`: "Nothing to lower" (no ratchet commit).
  - `pnpm corpus check`: "19 report(s) unchanged". `pnpm aliases check`: pass (Covid: 2,336 ids, 0 aliases, 0 unmatched). `pnpm editorial`: 18 files, `src/generated/editorial.ts` kept.
  - `VERIFY_SHARED=1 ./scripts/verify.sh` with `RTM_REPORT_DIRS=rtm-review367-1025-reports`: "All checks passed", exit 0, `git status --short reports/` empty. Log: `rtm-review367-1025-verify.log`.
- Read the product (local `pnpm dev`, port 8791): landing page lists 59 sections, 188,046 words, the eight chapters at level 2 with the sub-headings nested; section "13 March 2020" reads correctly (4.15 to 4.33 in order, notes beside their paragraphs, Figure 7 caption and source line present, no whiteboard OCR debris); `?p=4-29-evening-number-advisers&h=Downing` returns 200 with the paragraph in the og description; `/processing` 200; og image, default card and plate (the whiteboard crop, "No vaccine in 2020 / Must avoid NHS collapse / lockdown") load and read as the sources.yaml records.
- Ship rehearsal: `pnpm publish-report uk-covid-inquiry-module-2 --dry-run --offline`: 61 objects, 4.8 MB (D1 estimate is 1 because offline; see below). `pnpm ship --plan --state build/review-ship-state.json`: plans cleanly, all repos "pin ok".

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| ingest #83 | MERGE, then release v0.29.0 | The shared default is proven neutral on all 18 (two independent methods); dist clean; tests pass; clean with #82 |
| report repo #1 | MERGE after the pin bump commit | Reproduces its `full.md` byte for byte with the PR ingest; needs the pin bumped to v0.29.0 or `ingest preflight` and a fresh clone fail on `labelWraps` |
| site #367 | MERGE after the two above and the pin bump | All checks pass on the trial; reaches stage 4 only (`evaluate`, state waiting) |

## Findings, by severity

No blockers and no fixups pushed.

- Low, known and documented: chart-label debris renders as paragraphs in a few figures (Figure 6 on p.153: "ry ry ry ry ch ch ..."); front matter yields four "Volume I: ..." sections and an "Appendix 1" before the Introduction; Chapter 3's heading lacks "2020". All in PROCESSING.md and beads (r41s, nqc4); 4/6 golden pages pass, 2 xfail.
- Low: `docs/scores.json` records the Covid entry as ingest "v0.28.0" though it was scored with the unreleased branch. Re-score after the bump (step 6 below).
- Low: no editorial file yet, so the landing page has no introduction (stage 5, gqsy.14.1; hero gqsy.14.2). Do not announce or promote until it exists; publishing the text now is fine.
- Held-out score: boundary F1 91.0%, adjudicated reference wrong 3/30 (10%, above the 5% ceiling), so `pnpm score` warns as for Grenfell. Not a gate.

## What moves

Only the new report is added (59 sections, 2,336 paragraph ids, 3,236 notes). No existing report moves; no ids lost anywhere. D1: not measured (no `--yes` run; the new report is not on production). Reason from the last ship: a first publish writes every paragraph, so budget about 2,400 paragraph rows plus search and section rows on the order of 5,000 row writes, well inside the Paid plan's daily budget; confirm with `pnpm publish-report uk-covid-inquiry-module-2 --dry-run --yes` before publishing.

## Found only by the trial merge

- Other open site PRs conflict textually with #367 (append-to-end files): #365 (ica2017-1024) in `reports/manifest.yaml`, `reports/registry.yaml`, `docs/design/2026-09-12-imagery/sources.yaml`; #182 (duelfer-report) in those plus `src/generated/cards.ts`, `src/generated/marks.ts` and `reports/corpus-baseline.json` (deleted on main, modified there). #364 merges cleanly. Recipe: merge whichever lands second, keep both sides' entries, then `pnpm prerender && pnpm editorial && pnpm cards`, and rerun `pnpm marks`/`pnpm cards` generators for the generated files (take main's and regenerate); for `corpus-baseline.json` take main's deletion and run `pnpm corpus accept <id>`.
- Ingest #82 (edition-corrections) and #83 both change `src/pipeline.ts`/`clean.ts` areas but merge cleanly; if both are released together the version is one tag, so decide who goes first. If #82 merges first, #83 needs only a rebase check: `pnpm build && git status --porcelain -- dist` (checked clean).

## Integrator steps

1. Ingest: `git -C ~/src/reportsthatmatter/ingest checkout main && git pull`; merge #83 (`gh pr ready 83 && gh pr merge 83 --merge`; the repo uses merge commits). If #82 is also ready, merge it too and rerun `pnpm build && git status --porcelain -- dist` (must be empty) and `pnpm test` (943 on both).
2. Release: from ingest `main`, `pnpm release 0.29.0 --dry-run`, then `pnpm release 0.29.0` (latest tag is v0.28.0; no other open ingest PR claims 0.29.0).
3. Site worktree on main: `pnpm bump-pin 0.29.0 --dry-run`, then `pnpm bump-pin 0.29.0` (add `--shared` for shared checkouts). It commits and pushes each report repo's `package.json` and lockfile. The Covid repo's PR branch also pins v0.28.0: after merging report repo PR #1 (`gh pr merge 1 --merge -R reportsthatmatter/uk-covid-inquiry-module-2`), run bump-pin so the shared checkout on main is bumped too, or bump in the PR branch before merge (`pnpm install --no-frozen-lockfile`, commit `package.json` and `pnpm-lock.yaml`).
4. Merge the report repo PR #1 first (so `reports/manifest.yaml`'s `../uk-covid-inquiry-module-2` shared checkout has the content): `git -C ~/src/reportsthatmatter/uk-covid-inquiry-module-2 pull`.
5. Site: check out #367 (or merge it), merge the pin-bump change in, `pnpm bootstrap`, reinstall report repos per `pnpm ingest preflight`'s printed commands. Then `pnpm ingest check` (all 19 must match baselines; Covid must match its committed `full.md`), `pnpm ingest aggregate --shared`, `pnpm aliases generate --all`, `pnpm prerender`, `pnpm aliases check`, `pnpm corpus check` (expect "19 unchanged"), `pnpm editorial`.
6. Re-score and record: `pnpm score uk-covid-inquiry-module-2` so `docs/scores.json` reads the released version; `pnpm quality report --diff origin/main` into the PR body; `pnpm scorecard --out scorecard.md` for the pin-bump PR; commit `docs/scores.json`.
7. `VERIFY_SHARED=1 ./scripts/verify.sh` exit 0; `git status --short reports/` shows only the Covid additions.
8. Merge #367 (gh pr ready 367 first; it is a draft). Merge before #365/#182, or resolve per "Found only by the trial merge".
9. Ship: `pnpm ship --plan`; the Worker deploy comes before `publish-report` for a never-published report; `RTM_PUBLISH_SECRET=$(cat ~/.rtm-publish-secret) pnpm publish-report uk-covid-inquiry-module-2 --base https://reportsthatmatter.org --yes` only via `pnpm ship` steps; production verify.
10. After ship: `pnpm quality ratchet --record`, `pnpm scorecard --record --no-score`, commit `reports/quality-last.json`, `reports/verify-last.json`, `docs/scores.json`; set `reports/pipeline.yaml` Covid row `state` from waiting to `active` at its true stage (run `pnpm test` first: the tests reject unknown state values); changelog entry; close beads gqsy.14 only when editorial (gqsy.14.1) and hero are decided: keep gqsy.14.1, .14.2, nshl (Volume II), pxjq, jz19, nqc4 open.

## Beads

Nothing filed: every finding is already beaded (r41s, jz19, pxjq, nqc4, gqsy.14.1, gqsy.14.2). I did not append notes via `bd` (read-only review, no dolt push); the integrator should append the verdict and this file's path to gqsy.14.

## Lessons

- A shared-default change in an opt-in-heavy pass is proven by two measurements, the `try` rendered-text diff and a byte-for-byte rerun of every report's `full.md` against its committed copy; a control run on the target report shows the link bit.
- `pnpm` with a zsh variable holding several words (`pnpm ingest worktrees $ids`) passes one argument: use an array.

## Getting better faster

- Slowest: the `try` run (about 20 minutes with findings); `--no-findings` would have sufficed for a text-identity claim.
- A `pnpm ingest identical <base> <pr>` helper that reruns every report with two ingests and cmp's `full.md` would make the proof one command.
- Proposal: have `pnpm ingest try` print "all N reports byte-identical" as a single summary line for the PR description.
