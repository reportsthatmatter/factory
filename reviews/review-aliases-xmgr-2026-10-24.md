# Review: site PR #400, aliases map quotation text to the paragraph before it (xmgr)

Reviewer: Claude agent. I did not touch production: no merge, release, deploy, publish, seed or remote D1 command.

**Verdict:** SHIP. No fixup needed; the head is unchanged.

## Scope

- PR #400, branch `xmgr-aliases`, head `6f9bbf39` at start and at end. Bead reportsthatmatter-xmgr. Site only; no ingest change, no pin bump.

## Trial

- Worktree `~/src/reportsthatmatter/rtm-review400-1024`, branch `trial400-1024` from `origin/main` (`97a85c58`), PR merged `--no-ff` with no conflict, `pnpm bootstrap`.
- `pnpm typecheck` pass. `pnpm test` 1452 passed, 1 skipped. `pnpm aliases check` pass (Leveson: 16,985 recorded, 4,287 aliases, 2,603 unmatched).
- The new test fails on main's `alias-gen.ts` (1 failed, 23 passed) and passes with the fix.
- `pnpm quality check`, `pnpm corpus check`, `pnpm editorial` pass; `pnpm quality ratchet --dry-run`: nothing to lower. `pnpm ship --plan` runs.
- `VERIFY_SHARED=1 ./scripts/verify.sh` exit 0 on the trial (the litvinenko-inquiry failures the author saw did not reproduce on current main). Log: `factory/logs/2026-10-25/rtm-review400-1024-verify.log`. `git status --short` clean after every check.
- Only `reports/uk-leveson-inquiry/aliases.yaml` changed under `reports/`, so no other report's aliases moved.

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| #400 | SHIP | Fix is a two-line locator change; test is a true regression test; Leveson aliases are better than before; nothing else moves. |

## Findings

- Regenerating independently (`pnpm aliases generate uk-leveson-inquiry --old-ref 17945bb8^`) gives 4,290 aliases / 2,600 unmatched against the PR's 4,287 / 2,603; the three differences are the section-slug-like ids `chapter-5`, `chapter-5-2`, `press-police` (the PR leaves them unmatched, the regenerate maps them). Harmless, conservative. Not fixed.
- Diff of aliases against main: 810 new aliases, 85 existing aliases re-pointed, 22 aliases dropped to unmatched, `moved_out` 59 entries before and after (7 values differ).
- Spot check of 10 random new aliases (old text against target): all 10 correct. Each old id's text is the quotation now held as the block under the lead-in paragraph the alias points to (e.g. `we-very-concerned-avoid-situation` to 5.21 "In her evidence ... Ms Jowell", `point-contact-official-point-contact` to 5.190 "Mr Hunt described Mr Smith's role as:", `company-told-select-committee-march` to I 2.36).
- Spot check of the re-pointed and dropped: the old targets were coincidental probe matches and the new ones are right (`again-i-do-not-believe` was 4.16, an unrelated "Again, I do not consider ..."; now 2.16, which introduces the quotation; `all-reasons-set-out-above` now 5.60, which introduces that quotation; `carter-ruck` was a spurious 5.14 and is now unmatched). One debatable case: `9-29-ministerial-code-scotland` moved from 6.8 (which discusses paragraph 9.29) to 6.7 (a quotation containing the same words). Low; not fixed.
- `moved_out` changes are harmless: that map only records ids that kept their paragraph and lost an introduced block; nothing resolves through it, and its size is unchanged.

## What moves

- Leveson only: unmatched 3,391 to 2,603, `?p=` links to quoted passages now land on the introducing paragraph. No ids lost, no text change, no editorial change, no D1 cost beyond the usual Leveson publish (aliases ride in `meta.json`).

## Integrator steps

1. `gh pr merge 400` (no ingest release, no pin bump, no other report repo).
2. From a fresh site worktree on main: `pnpm bootstrap && pnpm aliases check`.
3. Leveson needs the usual publish to ship `meta.json` with the aliases (`report-publish` skill; `pnpm ship` handles it).
4. Close reportsthatmatter-xmgr on ship.

## Beads

None filed. Optional follow-up: the remaining 2,603 Leveson unmatched ids (text no longer present, mostly) are the next triage, under the existing aliases bead family.

## Lessons

The PR's own lesson file is accurate. Additional: regenerating a report's aliases from a different old ref than the committed file was built from re-points existing aliases, and that was correct here; compare old and new targets by text before treating re-pointing as a regression.

## Getting better faster

- Slow: `pnpm quality check` / `corpus check` need the script name as an argument (`pnpm quality check`), not `pnpm "quality check"`; wasted one call.
- A `pnpm aliases diff <id>` summarising new/re-pointed/dropped counts with sampled old/new text would replace my hand-written spot-check script.
- Proposal: add that sampled-text diff to `pnpm aliases generate` output.
