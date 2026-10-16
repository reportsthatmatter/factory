# Review: batch 5 (site #337-#344: Tier C flag, verify messaging, bump-pin push, ship beads path, production preflight, atomatic refusal, remote gates, CLS flake), 2026-10-16

Reviewer: Claude Opus 5.5 agent. I did not touch production: no merge, deploy, publish, reindex, seed, `--remote` D1 call, `wrangler` against either Cloudflare account, `pnpm ship` beyond `--plan`, or ratchet (no report text moves). The only production contact was two anonymous `curl` GETs of `https://reportsthatmatter.org/` and `/health` (200, 200) to check #341's preflight targets exist.

**Verdict: all eight MERGE, in the order below, with `gh pr merge --merge` (merge commits, as #331-#335 were). #340 and #343 carry reviewer fixup merges that resolve their conflicts with #339/#341 and #342; squash-merging them would bring the conflicts back. No republish, no reindex, no D1 migration. A deploy is not needed: the only Worker-side change (#337's `src/lib/feed-policy.ts` now importing `src/generated/affected.ts`) produces the same nine-id exclusion list as main; the next deploy carries it.**

## Scope

| PR | Bead(s) | Branch | Head at start | Head after fixups |
| --- | --- | --- | --- | --- |
| #337 | zh1w | `zh1w-tier-c-card` | ebe5fb92 | ebe5fb92 |
| #338 | fzdo | `fzdo-1016` | bde843c8 | bde843c8 |
| #339 | 3cut | `bumppin-1016` | 7a73ea1c | 7a73ea1c |
| #340 | xb83 | `shipbeads-1016` | bec17eb9 | fca2e440 (merges of #341 and #339, conflicts resolved) |
| #341 | 6pky | `preflight-1016` | 90f7c5cf | 90f7c5cf |
| #342 | 4g4d.8 | `cfacct-1016` | 112e8449 | 112e8449 |
| #343 | jp6i, cs92 | `jp6i-remote-gate-tests` | ead3186b | 15faca35 (merge of #342 with fixes, then a docs and hints commit) |
| #344 | a0rx | `a0rx-cls-flaky` | 5b30a086 | 5b30a086 |

origin/main at review: c05cc4d2 (Record the 2026-10-16 ship, #336).

## Trial

- Worktree `~/src/reportsthatmatter/rtm-reviewbatch5-1016`, branch `trialbatch5-1016` from c05cc4d2. Merged with `--no-ff` in the order 337, 338, 339, 341, 342, 344, 340, 343 (I first tried numeric order: #340 and #343 conflicted and were moved to the end), then a test fix and the docs fixup.
- Second worktree `~/src/reportsthatmatter/rtm-reviewbatch5sq-1016` simulates the integrator: from c05cc4d2, `git merge --no-ff` of 337, 338, 339, 341, 340 (fixed), 342, 343 (fixed), 344 in the recommended order. All eight merge with no conflict, and the tree is identical to the trial's (`git diff trialbatch5-1016` empty). A squash simulation of the same order conflicts on #340 (3 files) and #343 (2 files): see "Merge method".
- `pnpm bootstrap` pass (`factory/logs/2026-10-16/rtm-reviewbatch5-1016-bootstrap.log`).
- `pnpm typecheck` pass (`...-typecheck.log`, again after fixups `...-typecheck2.log`).
- `pnpm test` before the test fix: 1 failed, 1284 passed (`...-test.log`): `tests/migrate-hosting.test.ts` "refuses at the command line" (#342 x #343, below). After: 80 files, 1285 passed, 1 skipped (`...-test2.log`; after the docs fixup `...-test3.log`).
- `VERIFY_SHARED=1 ./scripts/verify.sh` on the combined trial: exit 0, "All checks passed", e2e-mobile's "[Pixel 7] the panel adds no layout shift" passing (`...-verify.log`). Re-run on the final trial (with both fixups, the same tree the integrator will get): exit 0, "All checks passed" (`...-verify2.log`). The re-run was needed because I briefly edited the registry and generated file (the staleness probe) while the first run was going.
- `pnpm editorial --check`: pass (`...-editorial-check.log`). Staleness proved both ways: flipping every `affected_community` flag in the registry fails with "src/generated/affected.ts differs from what pnpm editorial would write" (exit 1); hand-editing `src/generated/affected.ts` fails the same way. Both reverted.
- `pnpm ship --plan --state build/review-ship-state.json`: exit 0 (`...-shipplan.log`). The status step lists the GET of `/` and `/health` first; every production command shows `--yes` (marks check, d1-estimate dry runs, publish, reindex, seed).
- #344 sensitivity test (below): `...-cls-baseline.log`, `...-cls-injected.log`, `...-cls-injected-cold.log`, `...-cls-clean-cold.log`; the local worker's log `...-dev.log`.
- Not run, not applicable (no report text, ids or ingest change): quality, corpus, aliases, ratchet, `pnpm ingest try`, publish dry runs.

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| #337 | MERGE | The registry flag reproduces main's hard-coded list exactly (same nine ids, same order, CIA study included as batch 1 intended), so the feed's exclusion is unchanged. `editorial --check` catches a stale `affected.ts` in both directions. Tier C is a warning only (`TIER_C_IS_FAILURE = false`); 0 warnings on today's files. |
| #338 | MERGE | Message and docs only; the new aggregate text is accurate (aggregate reads the shared checkouts and writes the worktree's `reports/`). |
| #339 | MERGE | Commits only `package.json` and `pnpm-lock.yaml`, with pathspec, on the default branch from `origin/HEAD`; skips a worktree branch or other dirt; never forces; `--no-push`. Tested with bare remotes on main and master. |
| #340 | MERGE at fca2e440 (after #339 and #341) | Ignores exactly `.beads/issues.jsonl`; porcelain lines are not trimmed, so `slice(3)` is the path. Conflicts with #339 (skill step 3/4) and #341 (checklist paragraph, ship test) resolved on the branch by union. |
| #341 | MERGE | Preflight GETs `/` and `/health` (both exist and answer 200); stops ship's status step before prerender or any read; `publish-report --all --status` exits 1. `deploy-cloudflare.sh` already tolerates a non-zero status (`|| echo`). |
| #342 | MERGE | Per-publish `--rollback-account` flag, refusal before the secret and any call. Leaves seed-highlights, feed, deploy and ship's publish uncovered (PR says so): keep 4g4d.8 open. |
| #343 | MERGE at 15faca35 (after #342) | The gate is in the one D1 runner (`wranglerRunner({ confirmed })`), so no path can skip it; every caller in ship and verify.sh passes `--yes`. Semantic conflict with #342 fixed on the branch (below), plus the hand-typed commands and printed retry hints that still lacked `--yes`. |
| #344 | MERGE | Does not mask a panel regression: an injected post-paint shift of the landed paragraph fails the check warm (0.5470 vs 0.0000) and with `E2E_COLD_FONT_MS=1500` (0.5466 vs 0.0000); the clean trial passes cold. Threshold unchanged, no retry. |

## Findings, by severity

### Fixed on the branches

1. **High, found only by the trial merge: #343 would have broken #342's rollback flag.** #343 adds an unknown-flag allowlist to `scripts/publish-report.mjs`; #342 adds `--rollback-account`, which that list did not know. With both merged and only the textual conflict resolved, `pnpm publish-report <id> --base … --rollback-account --yes` exits 2 "unknown flag --rollback-account": the deliberate rollback publish becomes impossible. #342's own command-line test also failed, because #343's `--yes` gate now fires before the account check. Fix in 949fb339 on `jp6i-remote-gate-tests` (a merge of `cfacct-1016`): `ROLLBACK_ACCOUNT_FLAG` added to the known flags, both usage lines merged, and the test's remote runs pass `--yes` (the `--rollback-account` case now also proves the flag is accepted).
2. **Medium: textual conflicts.** #340 against #339 in `.claude/skills/integrate-and-ship/SKILL.md` (take #339's bump-pin bullets, #340's step 4 with the `.beads/issues.jsonl` pull note) and against #341 in `docs/release-checklist.md` (#340's paragraph with #341's preflight sentence inserted before "State is") and `tests/ship.test.ts` (keep both new tests). Resolved in 9b0a603a and fca2e440 on `shipbeads-1016`.
3. **Low: commands and hints still without `--yes`** after #343 (each would refuse with a clear message, not hang or skip, but the integrator types or pastes them). Fixed in 15faca35 on `jp6i-remote-gate-tests`: AGENTS.md "Publishing a report" (the publish example, `--preflight`, `--dry-run`, `reindex-search.sh --dry-run`, with a line saying a dry run against production needs `--yes` too), `docs/report-preparation.md` step 4, `docs/ARCHITECTURE.md` (staleness fix), `pr-review` and `report-introduction` skills, and the printed hints in `scripts/check-search-staleness.mjs` ("Fix:"), `scripts/publish-report.mjs` (reindex retry), `scripts/lib/status-table.ts` (the publish line under the drift table) and `scripts/lib/ship.ts` (d1-estimate describe and the publish and reindex retry hints).

### Not fixed (no bead needed, or already tracked)

- #342 covers publish-report only; seed-highlights `--remote`, `pnpm feed --remote`, `deploy-cloudflare.sh` and ship's publish step still follow `RTM_CF_ACCOUNT=atomatic` silently. 4g4d.8 lists them: leave it open.
- #344: the check compares warm loads, so a panel shift that happens only while fonts are cold, or before first paint, is not measured (the second was true on main too: a shift before first paint is not reported by the Layout Instability API; my first injections at 150 ms registered 0). The real cold-font 0.28 shift for readers is 3gx4 (evidence already appended by the author).
- #337: "lives" in the Tier C word list will also match the verb ("he lives in"); a warning only, and the author's real-file run gives 0. Revisit when 63cd flips `TIER_C_IS_FAILURE`.
- #338: the bead's code option (aggregate skipping dirty checkouts) is not done; the docs fallback is the bead's allowed alternative.

## Integrator path checks (#343)

- No prompts: no script reads stdin (`readline`/`prompt` appear nowhere in `scripts/`), so a missing `--yes` exits 1 with "Nothing sent to production. … needs --yes"; nothing can hang.
- Nothing skips silently: `pnpm ship` passes `--yes` to `marks check --all`, `publish-report --dry-run` (d1-estimate), `publish-report … --no-reindex` (publish), `reindex-search.sh` (reindex) and `seed-highlights --remote` (seed); `reindex-search.sh` forwards `--yes` to `reindex-search.mjs`; `publish-report`'s own reindex calls `reindex-search.sh <id> --yes`; `verify.sh` passes `--yes` to `check-search-staleness` (production mode only) and runs `seed-highlights` locally (no gate). `pnpm ship`'s status step and `deploy-cloudflare.sh` run `publish-report --all --status`, which is not gated. `pnpm d1-usage` has no gate by design (the cron's monitor; it only rejects unknown flags).
- Hand-typed: the release checklist, report-publish skill and report-introductions already said `--yes` in #343; the rest is fixup 3.

## Merge method

`gh pr merge --merge` for all eight. The fixups on #340 and #343 are merge commits of the earlier PR branches; with merge commits those earlier commits become ancestors and #340 and #343 merge cleanly (simulated). With `--squash` they do not (the squash commit is not an ancestor, so the resolved hunks conflict again). If the integrator must squash, merge #340 and #343 last and, on each PR branch, `git merge origin/main` and keep the PR branch's side of each conflict (`git checkout --ours <file>` for the five files named in finding 2 and 1), then run `pnpm test`.

## Integrator steps

1. `cd ~/src/reportsthatmatter/reportsthatmatter && git fetch && git pull --ff-only` (if the pull trips on `.beads/issues.jsonl`: `git restore .beads/issues.jsonl` first).
2. Check heads: `for n in 337 338 339 340 341 342 343 344; do gh pr view $n --json number,headRefOid --jq '"\(.number) \(.headRefOid[0:8])"'; done` must print ebe5fb92, bde843c8, 7a73ea1c, fca2e440, 90f7c5cf, 112e8449, 15faca35, 5b30a086. A different head needs a reviewer's word.
3. Merge, in this order, each with a merge commit: `gh pr merge 337 --merge`, `gh pr merge 338 --merge`, `gh pr merge 339 --merge`, `gh pr merge 341 --merge`, `gh pr merge 340 --merge`, `gh pr merge 342 --merge`, `gh pr merge 343 --merge`, `gh pr merge 344 --merge`. Hard constraints: #340 after #339 and #341; #343 after #342. No conflicts expected.
4. `git pull --ff-only`, then on main: `pnpm install && pnpm typecheck && pnpm test` (expect 1285 passed, 1 skipped) and `pnpm editorial --check` (must pass: `src/generated/affected.ts` is committed by #337).
5. No pin bump, ingest release, re-ingest, republish, reindex, seed or D1 migration. No deploy needed (Worker behaviour unchanged; the next routine deploy carries #337's refactor). If you deploy anyway, it is a plain `./scripts/deploy-cloudflare.sh` and its drift table must list nothing.
6. From now on hand-typed production commands need `--yes` (`pnpm publish-report <id> --dry-run --yes --base https://reportsthatmatter.org`, `./scripts/reindex-search.sh <id> --yes`, `pnpm seed-highlights --remote --yes`, `pnpm marks check --all --yes`, `pnpm check-search-staleness --yes`, `pnpm migrate-hosting <cmd> --yes`); `pnpm ship` and `verify.sh` pass it themselves. `pnpm bump-pin X.Y.Z --shared` now commits and pushes each report repo (`--no-push` to hold).
7. Close on merge: `bd close reportsthatmatter-zh1w reportsthatmatter-fzdo reportsthatmatter-3cut reportsthatmatter-xb83 reportsthatmatter-6pky reportsthatmatter-jp6i reportsthatmatter-cs92 reportsthatmatter-a0rx --reason "Merged in site batch 5 (review-batch5-2026-10-16.md)"`. Leave **reportsthatmatter-4g4d.8 open** with a note: publish-report done (#342); seed-highlights, feed, deploy-cloudflare.sh and ship's publish remain. Then `bd dolt push`.
8. Clean-up: `git worktree remove ../rtm-reviewbatch5-1016 ../rtm-reviewbatch5sq-1016 ../rtm-review340fix-1016 ../rtm-review343fix-1016` and delete the local branches `trialbatch5-1016 trialbatch5sq-1016 shipbeads-1016-fix jp6i-fix` (or leave it to `pnpm worktrees prune`).

## Found only by the trial merge

- The #342 x #343 flag allowlist break and the failing #342 test (finding 1). Each PR passed its own tests on its own branch.
- The squash-vs-merge difference for the fixup merges (simulated both).

## Beads

None filed. Verdict lines appended to each PR's bead (zh1w, fzdo, 3cut, xb83, 6pky, 4g4d.8, jp6i, cs92, a0rx).

## Lessons

- Two PRs that each add a flag and a flag allowlist to the same CLI conflict semantically even when git merges them: run the trial's tests, not each branch's.
- A layout-shift check is only as good as its injected failure: shifts before first paint are invisible to the API, so a sensitivity test must inject after paint.

## Getting better faster (R13)

- What slowed me: proving that pushing resolution merges to PR branches helps took a squash simulation, which showed it helps only with merge commits. The integrate-and-ship skill says `--squash` while recent PRs used `--merge`.
- What would have caught finding 1 earlier: a test that every flag a script's usage line prints is in its `exitOnUnknownFlags` list (parse the usage string, run with each flag, expect no "unknown flag"). One test over all gated scripts.
- Proposal: add that usage-versus-allowlist test to `tests/remote-gate.test.ts`, and settle the merge method for batches with fixup merges in the integrate-and-ship skill (`--merge` when the review says the branch carries a resolution merge).
