# Review: cia-torture-quality, 2026-11-08

Reviewer: fresh general-purpose subagent (read-only), 17:30 CET 8 Nov. PRs: ingest#89, reportsthatmatter#419, us-senate-cia-torture-report#4.

Verdicts: ingest#89 MERGE WITH FIXES; #419 HOLD until rebase and pin bump; report#4 MERGE WITH FIXES (needs the new pin).

Findings and what was done:
1. noiseRaised's stroke-row rule hid Challenger p.249's OCR-spaced note 111 ("fly.” 1 1 1"). Fixed: a row needs three lone 1s or a run of three, and no word, quotation or stop before it; portion markings need three short tokens. Negative tests added. Second try: all 21 other reports' findings unchanged.
2. Anchor change not covered by try: Philip Morris's "Page N of 1682" court-stamp counter had been read as its folio; now pdf-volume scheme, counts unchanged. Challenger improves (unmarked 6->5, blocks 35->31). Stated in #419.
3. Site cli.ts needs the v0.34.0 pin to typecheck: pin bump lands in #419.
4. #419 conflicted with #417/#418: rebased; note-definition-unreferenced ratcheted 344->305.
5. oracle-budget why updated. 6. CIA end-state numbers from the worktree pasted in #419.
Integrator order: merge ingest#89, release v0.34.0, merge report#4, bump-pin --shared, pin into #419, merge, ship.
