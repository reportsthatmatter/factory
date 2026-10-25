# Review: PR #381 (Covid Module 2 introduction, hero, cards, queue) and PR #379 (ICA 2017 hero) (2026-10-25)

Reviewer: Opus agent (pr-review skill). I did not touch production: no merge, deploy, publish, reindex, `seed-highlights --remote` or remote D1; nothing posted. No fixup commits pushed; I did not commit in the author worktrees `rtm-covidintro-1025` or `rtm-icahero-1025`.

**Verdict: MERGE both (#381 at `5958808e`, #379 at `f09c9e49`), in either order; they trial-merge cleanly onto main and with #374. One non-blocking follow-up (reportsthatmatter-1gc7).**

## Scope

- #381 `covid-m2-intro`, beads reportsthatmatter-gqsy.14.1, gqsy.14.2. Head `5958808e` (one commit), unchanged by review. Introduction (`editorial/uk-covid-inquiry-module-2.yaml`), hero, 3 Tier A quote cards, registry `context_line`, pinned test update in `tests/posts.test.ts`, 3 items appended to `marketing/queue.yaml`, `reports/pipeline.yaml` notes, one lesson.
- #379 `ica-hero`, bead reportsthatmatter-gqsy.13.1. Head `f09c9e49`, unchanged. Hero for `us-ica-2017-russia-elections`.
- Base: `origin/main` `3b950a32`. Interaction checked with open #374 `ntjx-og-desc-1024` at `a5dc5290`.

## Trial

- Worktree `~/src/reportsthatmatter/rtm-review381-1025`, branch `trial381-379-1025` = `origin/main` + `--no-ff` merges of `covid-m2-intro`, then `ica-hero`. Both auto-merged (`sources.yaml` and `src/generated/editorial.ts` touched by both; no conflict: #379 inserts its hero entry mid-file, #381 appends). `pnpm bootstrap` ok, 20 reports pre-rendered.
- `pnpm editorial --check`: pass, 20 files, `src/generated/editorial.ts` current after the merge (no regeneration needed). Every quote verbatim.
- `pnpm posts --check`: pass ("every not-yet-posted item still resolves"). `pnpm posts --verify`: rc 0, "marketing/queue.yaml is up to date"; the 3 Covid Tier A highlights "would be queued"; the 9 over-300 skips are existing reports, not this PR.
- `VERIFY_SHARED=1 ./scripts/verify.sh`: **All checks passed**, 1375 tests, rc 0. Log `factory/logs/2026-10-25/rtm-review381-1025-verify.log`. `git status --short` empty after.
- #374 merged on top (`--no-ff`, clean): `tests/description-length.test.ts` and `tests/editorial.test.ts` 46/46 pass, `pnpm typecheck` pass, `pnpm editorial --check` current.

## Verdicts

| PR | verdict | reason |
| --- | --- | --- |
| #381 `5958808e` | MERGE | Quotes verbatim; queue only appends 3 Tier A items, 26-28 Nov; posts check/verify pass; hero licence matches Commons; dignity rule respected; verify green on the trial. |
| #379 `f09c9e49` | MERGE | Public domain confirmed on Commons; no people, seal or campaign imagery; verify green on the trial. |

## Findings, by severity

No blocking findings. No fixups pushed.

- **Queue diff** (`marketing/queue.yaml`): append-only (0 removed lines vs main); 3 new items after the last existing one (ICA, 2026-11-25): `2-206-four-governments-knew` 2026-11-26, `4-231-through-their-own` 2026-11-27, `cobr-designed-deal-acute-emergencies` 2026-11-28. All three are `card: true`, `tier: A` highlights in the editorial; all dates later than 2026-11-03; no date collides with an existing item.
- **Hook facts spot-checked** against the report: "Scotland could carry out 350 tests a day" (`scotland-only-350-tests-could`, p. 91); "Northern Ireland, on 10 February, 40" (sidenote 459, "In Northern Ireland, as at 10 February, it stood at 40 tests a day"); COBR did not meet during the February half-term (`2-114-uk-cabinet-not`, `2-183-highest-levels-uk`); "COBR meetings were largely discontinued after May 2020" (`lack-trust-between-prime-minister`, p. 13). All supported.
- **Findings spot-checked** (two, per brief): finding 3's "about 23,000 fewer deaths in England in the first wave" matches `inquiry-recognises-lockdown-decision-difficult` (48%, approximately 23,000, to 1 July 2020); finding 5's "Scotland's stringent local measures avoided a second national lockdown that autumn" matches `number-cases-scotland-autumn-2020`. Both accurate.
- **Hero licences** (Commons API, extmetadata): Liverpool Street, `LicenseShortName` CC BY-SA 4.0, Artist Kwh1050, own work, no restrictions, 5472x3080, taken 2020-05-21 13:25:45 (camera clock; sources.yaml says "about 2pm", fine if the camera was on GMT; not on the page). CIA OHB, Public domain, Artist The Central Intelligence Agency, no restrictions, 4992x3328, 2011-03-08. Both match `sources.yaml` and the page credit.
- **Dignity rule** (`docs/hero-images.md`), checked on `assets/heroes/*-1200.webp`: Covid shows an almost empty concourse, a dozen small figures none identifiable, "Thank you ... NHS and key workers" boards; no hospital, patient, funeral or bereaved imagery. Passes, and the passed-over memorial wall is the right call. ICA shows the OHB canopy and facade, no people, small flag only. Passes.
- **Interaction with #374** (not blocking): if #374 lands, the Covid landing page's meta/og:description falls back to the first sentence of `why_it_matters`, which is 272 characters, so it is cut to "...made their decisions from the first…" (195 characters). Nothing breaks (tests pass, length within 200), but it reads as a cut sentence in a link card. Fix: add a `description:` once #374 is on main; bead reportsthatmatter-1gc7. ICA's first sentence is 154 characters, unaffected. The field is harmless before #374 (no unknown-key check), but leave it to the follow-up so the copy gets its own read.

## Found only by the trial merge

Nothing: both PRs and #374 merge cleanly in sequence and the generated editorial file stays current.

## Integrator steps

1. `gh pr merge 379 --merge` (head `f09c9e49`), then `gh pr merge 381 --merge` (head `5958808e`). Either order is clean; if a later merge conflicts in `src/generated/editorial.ts`, take main's and run `pnpm prerender && pnpm editorial`, commit.
2. In the shared checkout: `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only` (if `.beads/issues.jsonl` trips it, `git restore .beads/issues.jsonl` first), then `pnpm prerender && pnpm editorial --check && pnpm posts --check`.
3. No report text moves and no publish is needed (the Covid report is already live; heroes, cards and editorial are app assets). Deploy: `pnpm ship --plan` then `pnpm ship --shared --yes`, or by hand `./scripts/deploy-cloudflare.sh`.
4. Seed the new highlights: `pnpm seed-highlights --remote --yes`.
5. Check production: `VERIFY_SHARED=1 VERIFY_BASE=https://reportsthatmatter.org ./scripts/verify.sh`; open `/reports/uk-covid-inquiry-module-2` (hero, credit linked, findings) and `/reports/us-ica-2017-russia-elections` (hero).
6. Close beads reportsthatmatter-gqsy.14.1, gqsy.14.2, gqsy.13.1 on ship.
7. When #374 merges (any order), do reportsthatmatter-1gc7 (Covid `description:`).

## Beads

- Filed reportsthatmatter-1gc7 (Covid `description:` after #374), discovered from gqsy.14.1.
- Notes appended to gqsy.14.1, gqsy.14.2, gqsy.13.1: verdict and this file's path.

## Getting better faster

- Slowed by: nothing much; the two PRs had already been written to avoid each other (#379 inserted its `sources.yaml` entry mid-file).
- Would have caught the #374 interaction earlier: a `pnpm editorial` warning when an approved file's first `why_it_matters` sentence is over 200 characters and there is no `description:` (only meaningful once #374 lands).
- Proposal: add that warning to #374's `resolveEditorial` as a non-fatal note, so every new introduction gets the nudge at authoring time.
