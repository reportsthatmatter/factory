# Review: batch 2 (tooling), 2026-10-16

Reviewer: Opus agent (`pr-review` skill). I did not touch production: no merge, release, tag, pin bump, deploy, publish, reindex, seed, `wrangler d1 execute --remote` or `pnpm ship` beyond `--plan` / `--reset` against a scratch state file in the trial worktree.

**Verdict: all six MERGE (three after the review fixups already pushed); #325 needs a one-line conflict resolution after #323 lands; ingest #79 rides the next ingest release with the CIA/jc46 work.**

## Scope

| PR | Bead | Branch | Head at start | Head after fixups |
|---|---|---|---|---|
| site #323 | fjej | `fjej-fix-1016` | 0aca075e | 0aca075e |
| site #324 | moej | `moej-fix-1016` | 7ebb170a | **5da482e9** (fixup 00c3783c + lesson) |
| site #325 | 0eeq | `0eeq-fix-1016` | 21bb6f17 | 21bb6f17 |
| site #328 | v724 | `v724-1016` | cdf4db9b | **a088f42a** (fixup 2d73183e + lesson) |
| site #329 | irfs | `irfs-1016` | 7a80a025 | 7a80a025 |
| ingest #79 | 5p44 | `5p44-control-chars-1016` | 948d215e | **39c16668** |

Base: site `origin/main` 54d314ba, ingest `origin/main` c7ebd54 (v0.27.0).

## Trial

- Site `~/src/reportsthatmatter/rtm-reviewbatch2-1016`, branch `trialbatch2-1016` from 54d314ba, `pnpm bootstrap`, then I merged #323, #324, #325, #328 and #329 in that order. #325 conflicted with #323 in `docs/release-checklist.md` (resolved, recipe below); `scripts/ship.ts` merged without conflict. I merged the fixups in afterwards (57c8f340).
  - `pnpm typecheck` passed; `pnpm test` passed: 71 files, 1137 tests (logs `factory/logs/2026-10-16/rtm-reviewbatch2-1016-{typecheck,test}.log`).
  - `pnpm quality check` passed: all 18 within budget. `pnpm corpus check`: 18 unchanged. `pnpm aliases check` passed. `pnpm editorial --check`: current, nothing written (logs `rtm-reviewbatch2-1016-{quality,corpus,aliases,editorial-check}.log`).
  - `pnpm quality ratchet --dry-run`: nothing to lower, so there is no ratchet commit.
  - `VERIFY_SHARED=1 ./scripts/verify.sh`: All checks passed, including the new editorial step ("… src/generated/editorial.ts is current"). Afterwards `git status` was clean, so verify no longer rewrites the tracked file (`rtm-reviewbatch2-1016-verify.log`).
  - After the fixups: `tests/publish-call`, `skills`, `editorial-check` and `help` passed (92 tests), and typecheck passed.
  - Ship rehearsal: `pnpm ship --reset --state build/review-ship-state.json` printed `state reset: …`, exited 0 and deleted the file. The three report checkouts I sampled showed no git change. `--reset --shared` was refused with the usage line (exit 2). `pnpm ship --plan --state …` exited 0, with every repo already at the pin (`rtm-reviewbatch2-1016-shipplan.log`).
- Cross-batch: `~/src/reportsthatmatter/rtm-reviewbatch2x-1016`, branch `trialbatch2x-1016` = the batch-2 trial plus batch 1 (#321 5bedd35c, #322 fe998af6, #326 a1537aad, #327 56d61f95), then `pnpm bootstrap`.
  - There were **no textual conflicts** between the batches.
  - typecheck passed; `pnpm test` passed: 73 files, 1140 tests.
  - `pnpm editorial --check` passed with #327's editorial edits in.
  - Decision numbers: 0021 is factory-repo (#321) and 0022 is email-capture (#326), already renumbered by batch 1.
  - Logs: `rtm-reviewbatch2x-1016-*.log`.
- Ingest `~/src/reportsthatmatter/ingest-reviewbatch2-1016`, branch `trialbatch2-1016` = v0.27.0 + #79 (with fixup).
  - `pnpm build` leaves `dist/` clean. `pnpm typecheck` passed; `pnpm test` passed: 65 files, 927 tests.
  - The author's gate log `ingest-5p44-1016-gate.log` shows check-dist failing. That run predates the commit; on the PR head check-dist passes.
- Corpus effect of #79: `pnpm ingest try ../ingest-reviewbatch2-1016 --no-findings` from the cross-batch trial. All 18 reports showed "rendered text unchanged", "no quality signal moved", and every score row read 0 correct→wrong / 0 wrong→correct. The links and lock were restored and the tree was clean (`rtm-reviewbatch2x-1016-try.log`). Separately, I counted U+0000-0008, 000B, 000D-001F and 007F with perl in all 18 `reports/*/full.md` and in every `.md` committed on each report repo's default branch: **0 everywhere**. The "byte-identical" claim holds; it can only hold, since the check reads output and never writes it.
- pkill check: every PR's test evidence shows full runs (68-69 files, 1114-1118 tests; the verify logs end "All checks passed"). None looks truncated, and I re-ran everything on the trial anyway.

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| site #323 fjej | MERGE | `--reset` exits before any context is built. `--reset --plan` is allowed and any other combination is refused (exit 2). It is tested by spawning the script against an empty root, and I exercised it on the trial. No doc in the repo or the factory pairs `--reset` with another flag. |
| site #324 moej | MERGE (at 5da482e9) | The idempotency claim is right; see the finding below. The claim that quota 5xx are not retried was not true against the real Worker. The fixup corrects the claim and the hint, and bead 9u3h covers the Worker side. |
| site #325 0eeq | MERGE after resolving the conflict with #323 | The refusal happens before any write or install. Repos already marked done in the state are skipped. `--plan` marks the refused repos. `shared = !overridden` matches `report-dirs`. The integrate-and-ship flow (`bump-pin --shared` first, then `ship --shared`) is unaffected. |
| site #328 v724 | MERGE (at a088f42a) | `--check` writes nothing. verify uses it, and the plain run after it only refreshes `build/editorial-highlights.json`. The fixup aligns the standing rules (finding 2). |
| site #329 irfs | MERGE | A drifted lock relinks, a live lock still refuses, `--restore` leaves pnpm-owned links alone, and preflight fails with the fix. The author reproduced it end to end (`rtm-irfs-1016-e2e.log`). `.rtm-try.lock` is untouched, as the bead requires. |
| ingest #79 5p44 | MERGE, then release with the next ingest batch | The check is correct and reuses extraction's regex plus DEL. The corpus is clean, so no report fails it. The fixup removes a duplicated doc comment. |

## Findings, by severity

1. **#324, medium: quota 5xx would be retried (fixed in wording, 00c3783c; Worker side is bead reportsthatmatter-9u3h).**
   - The Worker has no `app.onError`, and the commit handler has no try/catch around `DB.prepare(...).run()` (`src/index.ts` ~L873). An uncaught D1 error, a spent quota included, therefore reaches the client as Hono's default `500 Internal Server Error` with no detail.
   - `QUOTA_ERROR` can never match that body, so a quota failure at commit *is* retried 3 times (17 s) before it fails. The PR's test for this case uses a JSON body the Worker never sends.
   - Harm is small. A failed commit costs no D1 rows. A real publish runs the quota preflight before uploading (publish-report.mjs L174). The other retried 503s ("no content bucket bound", "no database bound") are config errors that just fail after the retries.
   - The fixup corrects the header comment and release-checklist step 12, and adds a final-failure line pointing at `--preflight`, with a test assertion for it.
   - Idempotency, read in `src/index.ts`:
     - `objects` puts to `reports/<id>/<hash>/<path>`, which nothing points at until a commit. The keys are content-addressed, so re-putting the same body under the same hash is a no-op in effect.
     - `commit` re-derives the hash, reads every object back, then `INSERT … ON CONFLICT(report) DO UPDATE` sets the same hash. Only `published_at` changes, so it is safe to repeat, including when the first attempt wrote the row and then failed to answer.
2. **#328, medium: the standing rules said "never commit `src/generated/editorial.ts`" (fixed, 2d73183e).**
   - R7 in `docs/agent-protocol.md`, fix-agent step 8, report-editorial step 4 and its "Hands on" line, and report-introduction all told authors to `git checkout` the generated file. Once verify runs `--check`, a fix agent who follows R7 after moving ids would fail the done condition.
   - The fixup has PRs commit the regenerated file (as they already do for their lines in `src/generated/cards.ts`). On a conflict they take main's copy and re-run `pnpm editorial`, which is already the pr-review and integrate-and-ship recipe for `src/generated/*`.
   - #321 (batch 1) edits other lines of `agent-protocol.md` and `fix-agent/SKILL.md`; they merge cleanly in the cross trial.
3. **ingest #79, trivial: two doc comments stacked on `CONTROL_CHARACTERS`** (fixed, 39c16668; dist rebuilt, check-dist passes).
4. **#325, note:** the "REFUSED without --shared" plan line has no live repo to show today because every repo is at the pin. It is covered by the unit test.
5. **#323, note:** `--reset --plan --shared` is refused. That is acceptable: `--plan` without `--shared` already reports the guard's view.

## Found only by the trial merge

- #323 and #325 conflict in `docs/release-checklist.md` line 7. Both edit the same paragraph; `scripts/ship.ts` merges cleanly.
- #328 was checked against batch 1's #327 (editorial yaml edits): `editorial --check` stays green, so merging batch 1 does not leave main failing verify.

## What moves

Nothing. No report text, ids, sections, quality counts or score rows change. D1: none (no publish is needed for any of these). Deploy: only #324's and #323's code is script-side, and nothing changes in `src/`. A deploy is not required for batch 2, but ship's own deploy is harmless.

## Integrator steps

Merge after batch 1 or before it: there are no textual conflicts either way. Follow the batch-1 review for its own order.

1. `gh pr view <n> --json headRefOid` must equal: 323 `0aca075e`, 324 `5da482e9`, 325 `21bb6f17`, 328 `a088f42a`, 329 `7a80a025`; ingest 79 `39c16668`. A newer head needs a re-review.
2. `gh pr merge 323 --squash`
3. #325 now conflicts. In a worktree:
   ```bash
   git -C ~/src/reportsthatmatter/reportsthatmatter worktree add ~/src/reportsthatmatter/rtm-0eeqrebase-1016 0eeq-fix-1016
   cd ~/src/reportsthatmatter/rtm-0eeqrebase-1016 && git fetch origin && git merge origin/main
   ```
   In `docs/release-checklist.md` take main's line 7 (it has #323's "`pnpm ship --reset` only deletes that file …" sentence). Then change "then `pnpm ship --shared`;" to "then `pnpm ship --shared` (without `--shared` the pin bump stops before writing any shared report checkout, and `--plan` marks the repos it would refuse);". Nothing else conflicts.
   ```bash
   git add docs/release-checklist.md && git commit --no-edit && git push origin HEAD:0eeq-fix-1016
   ```
   Then `gh pr merge 325 --squash`. The resolved text is the trial's commit cdf5026a if you want to copy it.
4. `gh pr merge 324 --squash`
5. `gh pr merge 328 --squash`. From now on, `verify.sh` fails when `src/generated/editorial.ts` is stale. After any merge that touches `editorial/*.yaml` or moves ids, run `pnpm prerender && pnpm editorial` on main and commit the file if it changed.
6. `gh pr merge 329 --squash`
7. Ingest: `gh -R reportsthatmatter/ingest pr merge 79 --squash`. Do **not** release yet if the CIA/jc46 agent's ingest PR is due today: release both together once it is reviewed. #79 alone changes no output, so it is safe either way.
8. Ingest release, in the shared `~/src/reportsthatmatter/ingest` on `main`:
   ```bash
   git pull --ff-only
   pnpm release 0.28.0 --dry-run
   pnpm release 0.28.0
   ```
9. Pin bump, from the site's main checkout on a new branch:
   ```bash
   pnpm bump-pin 0.28.0 --dry-run
   pnpm bump-pin 0.28.0 --shared
   ```
   Then commit and push each report repo's `package.json` and `pnpm-lock.yaml`, and open and merge the site pin-bump PR. Its body carries `pnpm scorecard` (the CIA work will move text; #79 by itself will not).
10. `pnpm ship --reset`. With #323 merged it only deletes the state and exits 0. Then `pnpm ship --plan`: the guard must list no problem. Its moved-report list should name only what the CIA/jc46 review says, and nothing from #79.
11. `pnpm ship --shared`, acking gates after reading them; then `pnpm ship --shared --yes` per the integrate-and-ship skill. Every report's fidelity checks now print "no control characters — none". A ✗ there is a real defect to bead, not something to accept.
12. Close on ship: `bd close reportsthatmatter-fjej reportsthatmatter-moej reportsthatmatter-0eeq reportsthatmatter-v724 reportsthatmatter-irfs --reason "Merged in site <sha>, batch 2 review 2026-10-16"`. Close `reportsthatmatter-5p44` only after the release ships with "Shipped in ingest v0.28.0". Then `bd dolt push`.
13. Housekeeping: the review worktrees `rtm-reviewbatch2-1016`, `rtm-reviewbatch2x-1016`, `rtm-reviewbatch2fix-1016`, `ingest-reviewbatch2-1016` and `ingest-reviewbatch2fix-1016` can go with `pnpm worktrees prune --apply` between sessions.

## Beads

- Filed: **reportsthatmatter-9u3h** (P3, stream:platform, level:specced, handoff, discovered-from moej): the publish endpoint should answer a D1/R2 failure with its message, scoped to the internal routes, so publish-call's quota rule can work.
- Lessons committed on the PR branches: `docs/design/lessons/2026-10-16-error-body-rules-need-the-server-body.md` (#324) and `2026-10-16-new-gate-vs-old-advice.md` (#328); `tests/lessons.test.ts` passed on the trial.
- Noted on fjej, moej, 0eeq, v724, irfs, 5p44: verdict, head, and this file.

## Lessons

- A client-side rule keyed on an error body is only as good as the server's error body. Read the handler's failure path, not just its success path, before trusting "X errors are not retried". (moej, this review)
- A new gate turns old "don't commit the generated file" advice into a guaranteed failure. When a check starts reading a tracked generated file, grep the protocol and skills for instructions about that file in the same PR. (v724, this review)

## Getting better faster

- **What slowed me:**
  - Re-deriving which checks to run for "tooling" PRs.
  - Waiting for verify and `ingest try` in sequence. I ran try in the cross-batch worktree in parallel instead, which halved the wait.
  - A shell slip: zsh does not word-split `$c`, so my first loop of `pnpm $c` "failed".
- **What would have caught these earlier:** for moej, a test that drives the real Hono app with a throwing DB stub, not a hand-written body. For v724, a `tests/skills.test.ts`-style check that flags any skill or protocol line telling agents to `git checkout` a file that verify gates on.
- **Proposal:** add `pnpm review checks` to run the pr-review step-4 set (typecheck, test, quality, corpus, editorial `--check`, aliases, ratchet `--dry-run`) with one log per check under `factory/logs/<date>/`, so every reviewer runs the same set the same way.
