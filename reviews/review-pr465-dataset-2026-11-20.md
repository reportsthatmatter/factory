# Review: PR #465, per-paragraph dataset (9uog), 2026-11-20

Reviewer: Claude Opus agent. I did not touch production: no deploy, no `publish-report`, no reindex, no seed, no Hugging Face upload, no posts.

**Verdict: MERGE (merged by the reviewer on Rufus's standing "always merge"); /data goes live only on the next deploy.**

## Scope

- Site PR #465, branch `dataset-9uog`, bead reportsthatmatter-9uog. Head at start `436adfbd`; after fixups `0cd3f6d6`.

## Trial

- Worktree `~/src/reportsthatmatter/rtm-review465-1120`, branch `trial465-1120` from `origin/main` (`1e3aee5a`), `git merge --no-ff origin/dataset-9uog` (clean), `pnpm bootstrap`, then the fixups merged and `pnpm prerender` re-run (the registry is a stamp input).
- `pnpm typecheck`: pass. `pnpm test`: 1554 passed, 1 skipped. `pnpm quality check`: all 24 within budget. `pnpm corpus check`: 24 unchanged. `pnpm editorial`: pass, generated files kept. `pnpm aliases check`: pass. `pnpm quality ratchet --dry-run`: nothing to lower. Logs: `factory/logs/2026-11-20/rtm-review465-1120-*.log`.
- `VERIFY_SHARED=1 ./scripts/verify.sh`: one failure, `tests/poster.test.ts` (7 tests, exit status 1 from the fake-PDS subprocess) under verify's load. Unrelated to this PR (it touches no poster code); passes 3/3 run alone and in the plain `pnpm test`. Log: `factory/logs/2026-11-20/rtm-review465-1120-verify.log`.
- `pnpm dataset` on the trial: 64,618 rows from 24 reports; csv.gz 12.2 MB, jsonl.gz 12.6 MB. `/data` rendered via `app.request`: 200, every statement present, caution paragraph names Hillsborough and Lehman; `/data/licences.json` 200 JSON, `/data/x.txt` 404.
- `pnpm ship --plan`: the deploy step runs `./scripts/deploy-cloudflare.sh`, so ship deploys pick up `pnpm dataset`.

## Findings, by severity

Fixed on the branch:

1. **Licence statement not honest (medium), `c395387a`.** Hillsborough's `text_status.statement` (and so /data, `licences.json`, the HF card) said "a question to the Home Office is pending". Bead 9kn4 is closed: Rufus decided (2026-10-17) not to ask and to keep serving. Now "(reportsthatmatter-9kn4: we decided to keep serving it without asking)".
2. **Deploy fails late if a file outgrows the Workers 25 MiB asset limit (low), `0cd3f6d6`.** `pnpm dataset` now exits with a clear message above 25 MiB and warns above 80%. Today's largest file is 12.6 MB, about half the limit; at the current rate that is roughly double the corpus before it bites.
3. **/data showed "licences.json (0.0 MB)" (cosmetic), `0cd3f6d6`.** Files under 100 kB now show in KB.

Checked and fine: Lehman `not-established`, Hillsborough `not-open`, neither has a registry `license`, neither label says public domain or open (test enforces); Columbia and 9/11 hedged as "treated as ... not confirmed", matching 0024; Saville and Stern match their repo LICENSE files; ICA and Duelfer say their repos have no LICENSE yet, which is true. `set -euo pipefail` in the deploy script means a `pnpm dataset` failure stops the deploy before `wrangler deploy`; its only failure modes are a stale prerender (impossible straight after `pnpm prerender`), a report without `text_status` (caught first by `tests/dataset.test.ts` in ship's checks) and the new size guard.

## Integrator steps

1. Merged: `gh pr merge 465 --squash`.
2. /data is not live until the next deploy (`./scripts/deploy-cloudflare.sh` or `pnpm ship`); no publish or reindex is needed for it. After that deploy: `curl -sI https://reportsthatmatter.org/data/reportsthatmatter-paragraphs.csv.gz` should be 200.
3. Hugging Face upload stays with Rufus (`docs/dataset.md`).

## Beads

- 9uog: left open with a note (deploy needed; HF upload by Rufus); close once /data answers 200 on production.

## Lessons

- A licence statement copied from a repo LICENSE can carry a bead's state that has since changed; check each bead a statement cites.
