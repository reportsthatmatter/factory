# Agent review of reserved paragraph ids (efso, decision 0012): ingest#96 and site #448 (2026-11-13)

Reviewer: Opus agent. I did not touch production: no merge, release, tag, pin bump, `pnpm ship` beyond `--plan`, publish, deploy, reindex, seed, `wrangler d1 --remote`, or read of production D1. `pnpm marks check uk-stern-review` ran against the shared checkout's cached export (`--marks-file`, exported 2026-11-12T21:35Z), not D1.

**Verdict: MERGE AND SHIP both, after my two fixups (now on the branches), in the order below (ingest#96 → release v0.37.0 → pin bump folded into #448 → #448 → `pnpm ship --shared`). Must-fix: none left open; two were found and fixed here.**

## Scope

- **reportsthatmatter/ingest#96** (`efso-reserved-ids`): **2ed5c4a** at start, **d8e26d4** after my fixup. Based on main `4abb390` (v0.36.0).
- **reportsthatmatter/reportsthatmatter#448** (`efso-reserved-ids`): **16e6008c** at start, **f2eef317** after my fixup. Base `e712c3c7`; merges onto current main `5efbdbbc` (#447, the batch 2a release record) with no conflict.
- Also stacked: draft **#427** (`stern-posts-1112`, **2fdb488e**) on top, for its 5 card quotes.

## Trial

- Ingest worktree `~/src/reportsthatmatter/ingest-review96-1113` (`trial96-1113` = main + #96 + fixup): `pnpm build` leaves `dist/` clean; `pnpm test` 989/989 (988 + my test).
- Site worktree `~/src/reportsthatmatter/rtm-review448-1113` (`trial448-1113` = main `5efbdbbc` + #448 + fixup), ingest linked to the trial ingest (the state after the pin bump). Control worktree `~/src/reportsthatmatter/rtm-review448main-1113` = plain main on v0.36.0.
- **Per-report id diff, main vs trial, all 23 reports** (prerendered `assets/generated/reports/*`, every file hashed, plus id → text and id → section; script `factory/scratch/review448-1113/iddiff.py`): after the fixups, **22 reports byte-identical to main; uk-stern-review the only change** (46 ids, below). Before the site fixup, 3 more reports swapped ids (finding 1).
- **Checks on the trial** (final state): `VERIFY_SHARED=1 ./scripts/verify.sh` **"All checks passed."**, 1,521 tests, `corpus check` 23/23 unchanged, `aliases check` pass, `git status --short reports/` clean. Log: `~/src/reportsthatmatter/factory/logs/2026-11-13/rtm-review448-1113-verify.log`. `pnpm aliases generate --all` on the trial (old ref origin/main) writes **nothing**: the committed `aliases.yaml`, `published-ids.txt` and `reserved-ids.txt` reproduce exactly, and the stray us-mueller-report `moved_out` line no longer appears (it came from finding 1's swap). `pnpm quality ratchet --dry-run`: nothing to lower. `pnpm ship --plan --state build/review-ship-state.json`: guard stops only because the trial is not main (log `…/rtm-review448-1113-shipplan.log`).
- **Main alone** (`rtm-review448main-1113`): `pnpm corpus check` **23/23 unchanged**; `aliases generate --all` writes nothing.
- **Marks** (`pnpm marks check uk-stern-review --marks-file ../reportsthatmatter/build/marks-check/marks.json`): marks 12/12, editorial 44/44, 0 fail. The export predates batch 2a going live (#447, 02:13Z), so marks made on batch 2a's ids since then are not covered: integrator step 4.
- **#427 stacked** (`trial448s-1113`): `editorial --check`, `posts --check`, `cards --check` all pass (138 cards match; run `pnpm editorial` first, else cards reports the 5 Stern quote cards as orphans from a stale `build/editorial-highlights.json`). Its 5 card paragraphs are not among the 46 and keep their text.

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| ingest#96 @ d8e26d4 | MERGE, release v0.37.0 | No `reserved`: byte-identical output. Fixup closes a hole where a retired id was reborn on a different paragraph. |
| site #448 @ f2eef317 (+ pin bump) | MERGE AND SHIP | After the fixup, 22 reports unchanged and Stern exactly as intended; every check green on the linked trial. |
| #427 (draft) | unaffected | Stacks cleanly; cards, posts, editorial green. |

## Findings, by severity

### Fixed on the branches

1. **Must-fix, site: three live reports swapped ids** (fixed in #448 `f2eef317`). `toReserved` ordered a family by the number after the last hyphen, so an id whose own text ends in a number sorted after its `-2`. Where two paragraphs share text and next passage, tier 1 then swapped them: litvinenko-inquiry `order-made-under-section-19` … `-19-5` rotated (5 ids, "Restriction Notice No 4"), us-mueller-report `papadopoulos-statement-offense-i-5` ↔ `-5-2`, uk-covid-inquiry-module-2 `january-2020-may-2022` ↔ `-2`. **These are the three `corpus check` failures the PR attributed to drift on main: main passes 23/23; the PR caused them.** They are not in-flight work of another session. Fix: order within a family by id length, then id (the order the de-duplicator hands them out). Test added. Stern's output is unchanged by the fix.
2. **Must-fix, ingest: a retired id could be reborn on a different paragraph** (fixed in #96 `d8e26d4`). "Lost to an older one" was decided only after all three tiers, so tier 3 (first family paragraph left in the section) could hand a retired, aliased id to another paragraph. Reproduced: with `tol-r-s-j-2002-2` retired onto "part II", adding a "part I" Tol 2002 reference to that list gave it `tol-r-s-j-2002-2`, the exact c99s defect. Now retired ids are decided after tiers 1-2 and tier 3 skips them ("part I" gets `-3`). Test added. Corpus output unchanged.

### Not fixed (bead reportsthatmatter-8vfn, P3)

3. `familiesOf` lets an id ending in `-N` also match the family without it in tier 3 (`…-section-19` could take a "… section" paragraph if its own paragraph was corrected). No case in today's corpus.
4. Fingerprints are first-served and never refreshed, so each correction pushes that id to tier 3, the weakest match. Fine today; worth a refresh column later.
5. `docs/release-checklist.md` step 5 does not name `reserved-ids.txt` among the files `generate` writes.

### Matching robustness (asked)

- Short or duplicate texts ("Key Messages", identical reference entries, headings) are matched by text and next passage, then text and section; a tie goes to the same section, then reading order, given the list's order (now correct). The 16 Stern restorations include 5 "Key Messages" and 11 references, all correct.
- Deterministic: same input, same output. The order is by first-served stamp, then id; the tiers run in a fixed order.
- A paragraph whose text changes so its base id changes leaves its family: its reservation matches nothing and constrains nothing. That is the old behaviour, which corpus check and `aliases generate` catch as before.

## What moves (uk-stern-review only)

Verified against batch 1 (#433, `c552deb7`): rendering batch 1's `full.md`, **all 1,092 batch-1 ids carry the same text in the trial**. Checked by hand: `key-messages` → Ch 2; `-2`…`-5` → Ch 6, 9, 10, 13; `tol-r-s-j-2002` → Ch 6, "part II: dynamic estimates"; `intergovernmental-panel-climate-change-2001` → Ch 6 (WG I Scientific Basis); `-2001-2` → Ch 9 (WG III Mitigation); `-2000` → Ch 9 (Land-use). The 15 retired batch-2a ids each alias to the id now on the paragraph they showed, with the same section and text (e.g. `key-messages-6` → `key-messages-2` in Ch 6, `intergovernmental-panel-climate-change-2001-4` → `-2001-2` in Ch 9). 15 new suffixed ids sit on the new chapters (`key-messages-12`…`-15` on Ch 1, 3, 4, 5; references `-3`/`-4`/`-5`/`-6`). The alias file has 15 aliases and 0 unmatched.

**Next batch (Ch 11-12 inserted before Ch 13), simulated** (`factory/scratch/review448-1113/sim.mts`). Two versions: new chapters with their own Key Messages and repeated IPCC/Tol/IEA/EPA/Schlenker references; and, harsher, whole copies of Ch 9 and 10. **With reserved ids, all 2,184 live ids keep their text and section, Ch 13 included.** Without them, 23-24 ids change meaning and 7-8 go.

**Republish:** uk-stern-review only. Every other report is byte-identical under v0.37.0. **D1:** small, about 46 search rows rewritten plus Stern's meta. Ship's `d1-estimate` reads the real figure.

## Failure modes and upkeep (asked)

- **Missing file:** the report renders as before (no reservation). `aliases check` fails ("no reserved-ids.txt (run pnpm aliases seed-reserved)").
- **Stale file** (new ids not added): `aliases check` fails ("current id(s) not in reserved-ids.txt"). `pnpm ship` runs `aliases generate --all`, which appends new ids with their first home and a timestamp. Ship's alias step refuses removed lines in the file. Nobody regenerates it by hand.
- **Pin older than v0.37.0:** the file is silently ignored. #448 must not merge without the bump, or Stern's baseline and aliases fail (as the PR says).
- **`rtm-publish` in a report repo** renders without reservations. Publishing goes through the site, as before.

## Fixups pushed

1. ingest#96 `d8e26d4`: `src/render.ts` (tier 3 skips retired ids), `dist/render.js`, a test in `tests/reserved-ids.test.ts`.
2. site #448 `f2eef317`: `src/lib/reserved-ids.ts` (`familyOrder` replaces `suffixOf`), a test in `tests/reserved-ids.test.ts`. No data file changed.

## Integrator steps

1. Confirm heads: `gh pr view 96 --repo reportsthatmatter/ingest --json headRefOid` = d8e26d4…; `gh pr view 448 --repo reportsthatmatter/reportsthatmatter --json headRefOid` = f2eef317…. Re-read any new commits.
2. `gh pr merge 96 --repo reportsthatmatter/ingest --squash`; `git -C ~/src/reportsthatmatter/ingest pull --ff-only`; from `~/src/reportsthatmatter/ingest`: `pnpm release 0.37.0`.
3. Pin bump folded into #448. Either half alone fails `corpus check` and `aliases check` on main.
   - `git -C ~/src/reportsthatmatter/reportsthatmatter worktree add ~/src/reportsthatmatter/rtm-ship448-1113 -b ship448-1113 origin/efso-reserved-ids`, `cd` there, `pnpm bootstrap`, `unset RTM_REPORT_DIRS RTM_REPO_ROOT`.
   - `pnpm bump-pin 0.37.0 --dry-run --shared`, then `pnpm bump-pin 0.37.0 --shared`. It commits and pushes the bump in all 23 report repos on their default branch (`master` for columbia-accident and uk-leveson-inquiry). Read any repo it skips.
   - `pnpm prerender && pnpm corpus check && pnpm aliases check` (expect 23 unchanged, pass). Commit the site's `package.json` and `pnpm-lock.yaml` ("Bump @rtm/ingest to v0.37.0"), `git push origin HEAD:efso-reserved-ids`, wait for CI, then `gh pr merge 448 --repo reportsthatmatter/reportsthatmatter --squash`.
4. **Marks on batch 2a ids** (production D1, read-only, needs `--yes`): from the site's main, `pnpm marks check uk-stern-review --refresh --yes`. It must show 0 fail. A failing mark means a reader marked one of the 16 ids in the hour batch 2a was live: read it before shipping.
5. `git -C ~/src/reportsthatmatter/reportsthatmatter pull --ff-only`; `pnpm ship --plan`, then `pnpm ship --shared`. Expect: re-ingest leaves all 23 texts unchanged (no baseline gate); `aliases generate --all` writes nothing (0 new, 0 reused; it rewrites `reserved-ids.txt` identically); no corpus gate (Stern's baseline is in #448); no cards or queue change.
6. `d1-estimate` (small), then `pnpm ship --yes`. It should publish **only uk-stern-review**. If it lists another, stop and read why. Then deploy, reindex Stern, seed (nothing new), verify-prod. Spot-check `?p=key-messages` → Ch 2, `?p=tol-r-s-j-2002` → Ch 6, `?p=key-messages-6` → Ch 6 via its alias.
7. `record`: the usual PR; a changelog entry ("published paragraph links no longer move when an earlier chapter is added").
8. Beads: close **reportsthatmatter-efso** and **reportsthatmatter-c99s**. Leave 15zu (restoring older shifts, needs-user) and 8vfn open. #427 stays a draft.

## Beads filed or noted

- Filed: reportsthatmatter-8vfn (findings 3-5).
- Noted: efso (verdict, this file).

## Lessons

The id-diff caught what `corpus check` was saying, but the PR read it as drift on main. Before calling a failure "baseline drift on main", run the check on a clean main worktree: here main passed 23/23.

## Getting better faster

- What slowed me: nothing much. The per-report byte and id diff against a main control worktree took one script and found finding 1 in its first run.
- What would have caught finding 1 earlier: a `corpus check` run on main alongside the branch run. The author's claim rested on "ids identical before and after", compared in a way that missed a same-text swap. Text-only comparison cannot see two identical paragraphs trading ids, but a byte comparison can.
- Proposal: `pnpm ingest try` and prerender print, per report, "bytes identical to the pin's render: yes/no", so a swap of identical paragraphs shows as a change rather than "0 meaning changed".
