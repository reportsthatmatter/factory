# Review: PR #373, `pnpm grade` (38s.16, 38s.19, znfm)

Reviewer: Opus 5.5 agent. I did not touch production, merge, push or edit the branch; no trial merge (PR is MERGEABLE on c084a388), no verify.sh, no ship rehearsal.

**Verdict: MERGE after one doc fix (decision 0035 option 5); code is correct.**

Head reviewed: dc5d2176.

## Checks

- `pnpm -s exec vitest run`: 86 files, 1371 passed, 1 skipped.
- `pnpm grade --help`: prints usage, exit 0, tree clean. `pnpm grade --no-write` and `--no-write --diff`: run, write nothing (diff shows all 20 "new", as main has no grades.json).
- `reports/verify-last.json`: identical to `origin/main` after deleting `goldenOracle` (scripted JSON compare); 20/20 reports gained it.
- Formula checked by hand on Mueller and Leveson terms: precision fallback, max(oversplit, page breaks) + quotes, sum of graded dimensions, D/F-below-C rule (grade.ts:201) all match docs/scoring.md and decision 0030.
- Default `reportDir` in inputs.ts gives cleanEdition=false everywhere; re-graded: no letter changes today.

## Should fix before merge

1. docs/decisions/0035-grade-method.md:16-21: a fifth unsettled choice that moves letters is missing. grade.ts:40,100 takes a report's raw own precision from just 5 findings, no shrinkage: Leveson oversplit 5/5 gives 100% x 2091 (P 11.0 D, the only reason it is "paragraphs below C"); Mueller headings-missed 0/5 gives 0% x 455. Add it as option 5 (for example, Laplace on own counts, or a higher MIN_FINDINGS), and say so in scoring.md:32.

## Nits

- grade.ts:114: `pages === 0` gives per10 0, so A and publishable. Should be unmeasured/not publishable.
- grade.ts:109: `exact()` matches "adjudicated, all strata (biased up)", so it would count as "measured". Hidden today only because the oracle terms are pooled.
- inputs.ts:53: the default `reportDir` (`reports/<id>`) never has ingest.ts. Without the report repos, cleanEdition is silently false. Warn instead.
- grade.mjs:43-55: `--record-referee --no-write` still writes referee-last.json.
- grades.md: Challenger shows "10.0 D" (10.047). Show 2 decimals near a boundary.
- scoring.md:43: "recall 35% for oversplits". The pooled figure is 13/41 = 32%.
- 0035:18 says "10 reports" but lists 8 (Jack Smith 5.2 and PSI 3.7 are missing). The count is right.

## Acceptance

- 38s.16: met (six dimensions, precisions, unmeasured named with b78.6/38s.3/38s.15, scoring.md section and decision record).
- 38s.19: met. The "13-report table" is now 20, as the bead notes say. Scorecard section 4b and ship record step are fine; ship tests pass.
- znfm: thresholds copied verbatim (scoring.md:30).

## Decision 0035 defaults

They are reasonable and honestly stated. Not grading unmatched ids or WER is well argued, with the cost shown. Taking the biased reference rate "can only make a grade worse" is true under max(). The one gap is the per-report-precision rule above.
