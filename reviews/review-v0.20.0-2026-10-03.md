# Agent review of the v0.20.0 PRs (2026-10-03)

Reviewer: Opus agent, read-only. Every PR in scope SHIPs; none HOLD. Site #182 and ingest #22 (Duelfer) are held, out of scope. Neither ingest nor site has CI; the integrator's local verify is the only automated gate. Scratch clones with a simulated ingest integration (branch `sim48`): `/private/tmp/claude-501/-Users-rgrp-src-reportsthatmatter/c2584b47-3b47-4b84-8911-b50684365c43/scratchpad/{ingest,site-me}`. There, all ingest PRs squash cleanly: typecheck clean, 693/693 tests.

## Verdicts

| PR | Verdict | Reason or exact fix |
|---|---|---|
| ingest #44 page-break residuals | SHIP-WITH-FIX | No `dist/` committed, so `scripts/release.mjs` refuses `pnpm release`. Run `pnpm build` and commit `dist/pagebreaks.{js,d.ts}`, `dist/paragraphs.js`, `dist/passes.{js,d.ts}` (or a "chore: rebuild dist" commit on main before releasing). |
| ingest #45 Saville cleanEdition | SHIP | The page-gap fill applies to every HTML-edition report: 9/11 page markers move (pp.144, 324, 413, 448), as expected. Entity decoding is now case-first, which can change some text. |
| ingest #46 layoutMarkers | SHIP | Opt-in, but three changes reach every report: layout parsing (a raised marker goes back inside its line), note pairing above 40M cells, and oracle counting. Read the re-ingest diffs of reports without layoutMarkers too. |
| ingest #47 printed paragraph numbers | SHIP | On by default, so ids move widely and aliases are needed. |
| ingest #48 note-off-page | SHIP-WITH-FIX | After #46 squashes: `git rebase --onto origin/main origin/marker-linker-b94 note-off-page-y0w9`, force-push, retarget to main. A plain retarget conflicts in `src/oracle.ts`, `dist/oracle.d.ts`, `README.md`. `sections.ts` changes slugs for any report whose headings carry a linked marker. Minor: `hasPageNotes` checks a pass named `"edition"`, but it is `"cleanEdition"`; harmless today. |
| site #252 quote folding | SHIP | Matching only. Must land before the Saville text. |
| site #244 page-break re-ingest | SHIP-WITH-FIX | Keep only `editorial/jack-smith-vol1.yaml` and `lessons.md`; discard the `full.md` files and `corpus-baseline.json` (the re-ingest replaces them). lessons.md conflict: keep both sides. |
| site #251 oracle/budgets | SHIP | Must land with the v0.20.0 pin; otherwise oracle marker counts are inflated for layoutMarkers reports. |
| site #255 note-off-page budgets | SHIP-WITH-FIX | `scripts/ingest/cli.ts` imports `hasPageNotes`, which only #48 exports, so every `pnpm ingest` breaks until the pin is a v0.20.0 that includes #48. Merge after the pin bump; after #251 squashes, `git rebase --onto origin/main marker-linker-b94 note-off-page-y0w9`. Discard its Litvinenko/PM `full.md`. |
| site #249 Saville hybrid | SHIP-WITH-FIX | Drop the derived commit (Saville `full.md`, `PROCESSING.md`, `aliases.yaml`, `published-ids.txt`, `corpus-baseline.json`, the Saville budget block) and regenerate at v0.20.0. Its `published-ids.txt` adds 44 ids never published. Keep commit 1. Land `editorial/uk-saville-inquiry.yaml` with the regenerated text (new slugs). n9em does not recur in the PR's text; re-check after the re-ingest. |
| site #250 shared-checkout guard | SHIP (last) | Conflicts with #255 in the `cli.ts` imports; keep both. Any non-empty `VERIFY_SHARED` enables it. Doc follow-up: skills `report-first-ingest` and `report-evaluate` still say plain `pnpm ingest run`. |
| site #254 draft post | SHIP | Draft. lessons.md conflict only. |
| site #256 numbered-prose-list | SHIP | Conflicts with #249 in the Saville block of `quality-budget.yaml`; take the ratcheted value at the new pin. |
| site #257 vision pass | SHIP | Measure-only; tests pass. |
| us-911-commission #11 | SHIP | |
| us-v-philip-morris #8, #9 | SHIP | #9 builds only at the v0.20.0 pin. |
| jack-smith-report #7, #8 | SHIP | #8 is reference data only (optional). |
| challenger-accident #6 | SHIP (required) | c-0034/35 move 195 to 194; without it `ingest check` fails at v0.20.0. |
| challenger-accident #7, #8 | SHIP | #8 is reference data only. |
| us-lehman-examiner #6, #7, #8 | SHIP | #7 and #8 conflict on the golden p.748 xfail (#7 keeps `xqu`, #8 keeps `0bf`). Resolve by running golden after the re-ingest; probably only the `must_contain` xfail survives. |
| uk-chilcot-inquiry #9, #10 | SHIP | |
| uk-hillsborough-panel #7, #8 | SHIP | |
| uk-leveson-inquiry #5, #6, #7 | SHIP | #6: footnotes 8,596 to 8,511 (contents pages back into the body). |
| us-deepwater-horizon #5, #6 | SHIP | #6 removes ids, so aliases are needed. |
| us-psi-financial-crisis #9, #10, #11 | SHIP | |
| uk-saville-inquiry #7 | SHIP | Needs #45. |
| uk-saville-inquiry #8 | CLOSE as superseded | The re-ingest after #7 decides; don't merge its `full.md`. |
| uk-litvinenko-inquiry #7, #8 | SHIP | #7 builds only at the v0.20.0 pin. |
| us-columbia-shuttle-accident #7 | SHIP | |

## Merge order and hazards

1. **Ingest:**
   - Squash #44 (with the `dist/` fix), #45, #46 and #47.
   - Rebase #48 with `--onto` and squash it.
   - Confirm `pnpm build` leaves `dist/` unchanged, then do the dry run and `pnpm release 0.20.0`.
2. **Report repos:**
   - Their pins are at **v0.18.0**, not v0.19.0. Bump to v0.20.0 with each merge.
   - Merge source files only: `ingest.ts`, `golden.yaml`, `corrections.yaml`, and for Saville also `inquiry-html.ts`, `reference/raw/`, README, PROCESSING, datapackage. Then re-ingest each report once.
   - Same-report overlaps: Lehman #6/#7/#8, Leveson #5/#6/#7, PSI #9/#10/#11, Chilcot #9/#10, Challenger #6/#7, Hillsborough #7/#8, Deepwater #5/#6, Litvinenko #7/#8, Saville #7/#8, Philip Morris #8/#9.
3. **Site:**
   - Pin bump to v0.20.0 (with #48), then #252, #244 (source only), #251, #255 (rebased), #249 (derived commit dropped), #256, #257, #254.
   - Then re-ingest, aggregate, aliases, prerender, accept and ratchet. Then ship. Then #250 last.
   - Conflict hazards: `lessons.md` (8 PRs, keep both sides), `quality-budget.yaml` (#249 against #256), `cli.ts` (#255 against #250), `oracle-budget.yaml` (#251, #255), `corpus-baseline.json` (#244, #249).

## Differences from the a94n bead

- **Rebuild ingest `dist/` before releasing.**
- **Rebase #48 with `--onto`.**
- **Bump the pin before #255.**
- **Aliases:**
  - `pnpm aliases generate` compares against `origin/main`. Keep derived files off main until generation, or run `pnpm aliases generate --all --old-ref 78e963b` (the last deployed commit).
  - Generate for **all** reports, not about 9.
- **Include the mv1t set.** Close Saville #8.
- **Read re-ingest diffs for every report.**
- **n9em is still live.** A fix agent is running; its PR(s) go into v0.20.0.
- **#248 is already merged.**
