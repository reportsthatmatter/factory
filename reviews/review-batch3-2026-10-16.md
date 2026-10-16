# Review: batch 3 (jc46, Senate study severed paragraphs), 2026-10-16

Reviewer: Opus agent (`pr-review` skill). I did not touch production. I ran no merge, release, tag, pin bump, deploy, publish, reindex or seed, and no `wrangler d1 execute --remote`. `pnpm ship` ran only with `--plan` against a scratch state file. `marks check` read the cached marks export from 2026-10-10, not D1.

**Verdict: all three MERGE, as one 0.28.0 release with ingest #79 (already on ingest main). Site #330 is at 2ab07a68 after two review fixups. Merge order: ingest #80 → release → bump-pin → report #2 → site pin bump → site #330 → ship.**

## Scope

| PR | Repo | Branch | Head at start | Head after fixups |
|---|---|---|---|---|
| ingest #80 | reportsthatmatter/ingest | `jc46-severed-1016` | 5d681fc4 | 5d681fc4 |
| report #2 | reportsthatmatter/us-senate-cia-torture-report | `jc46-severed-1016` | a6b708a9 | a6b708a9 |
| site #330 | reportsthatmatter/reportsthatmatter | `jc46-severed-1016` | d800c0f7 | **2ab07a68** (fixup) |
| ingest #79 (5p44, batch 2) | ingest | merged during this review as 8ace7c2 | 39c16668 | n/a |

Bases:
- Ingest: `origin/main` c7ebd54 (v0.27.0). #79 then landed as 8ace7c2.
- Site: `origin/main` 54d314ba at the start. Batches 1 and 2 landed during the review, moving it to c6b1e196. I re-ran the trial on c6b1e196 (below).
- Report: `origin/main` 7d0bd3e.

## Trial

- **Ingest** `~/src/reportsthatmatter/ingest-reviewbatch3-1016`, branch `trialbatch3-1016`: v0.27.0, then #79 (39c16668), then #80 (5d681fc4).
  - The merge was clean; the two PRs touch disjoint files.
  - `pnpm build` leaves `dist/` clean. Typecheck passed. `pnpm test` passed: 66 files, 933 tests.
  - Logs: `factory/logs/2026-10-16/ingest-reviewbatch3-1016-{build,typecheck,test}.log`.
  - Its tree, cc46bebc, is the same tree `git merge-tree origin/main origin/jc46-severed-1016` gives now that #79 is squashed onto main. So the post-merge `main` is exactly what I tested, `dist/` included.
- **Corpus effect of #79 + #80 together**:
  - Command: `pnpm ingest try ../ingest-reviewbatch3-1016 --no-findings`, all 18 reports, report repos at their `main` HEAD (`rtm-reviewbatch3-1016-try.log`).
  - **The 17 other reports: "rendered text unchanged". No quality signal moved, and every score row read 0 correct→wrong / 0 wrong→correct.**
  - The study is reported changed only because the trial site copy was #330's while its repo HEAD declares none of the new passes. The trial re-ingest of that HEAD gave back exactly v0.27.0's counts (101 / 57 / 82 / 2, advisory 107) and its 2,408 paragraphs. That is the opt-in claim, checked from the other side.
- **The study at the PR heads**:
  - Report worktree `rtm-reviewbatch3-1016-reports/us-senate-cia-torture-report` = report `main` + #2, linked to the combined ingest.
  - `pnpm ingest run` left `git status` clean, so the combined ingest reproduces #2's `full.md`, `baseline.json` and the other outputs byte for byte. #2's `full.md` is byte-identical to #330's `reports/us-senate-cia-torture-report/full.md`.
  - The new #79 check passes: "no control characters — none".
  - `pnpm ingest verify`: golden pages 6/10 match, 4 known failures (p.40 jc46, p.51 and p.20 fgle, p.560 u7zu). The three new goldens (p.47, p.83, p.618) pass. I confirmed from v0.27.0's `full.md` that each fails there.
  - `ingest recheck --passes hangingLabels,insetOpenings,quoteListRunOns`: every correction matched once. `ingest crosscheck` was clean.
  - Logs: `rtm-reviewbatch3-1016-{run,ingestverify}.log`, `rtm-reviewbatch3b-1016-{recheck,crosscheck}.log`.
- **Site trial 1**: `~/src/reportsthatmatter/rtm-reviewbatch3-1016`, branch `trialbatch3-1016` = 54d314ba + #330 (d800c0f7).
  - These passed: typecheck, `pnpm test` (1110 passed), `quality check`, `corpus check` (18 unchanged), `aliases check`, `editorial`, `ratchet --dry-run` ("Nothing to lower").
  - **`VERIFY_SHARED=1 ./scripts/verify.sh` failed** (`rtm-reviewbatch3-1016-verify.log`), on two findings fixed below. Re-run with the fixup merged: **All checks passed**, tree clean (`-verify2.log`).
- **Site trial 2, on the moved main**: `~/src/reportsthatmatter/rtm-reviewbatch3b-1016`, branch `trialbatch3b-1016` = c6b1e196 (batches 1 and 2 merged) + #330 at 2ab07a68. The merge had no conflicts.
  - These passed: typecheck, `pnpm test` (1138 passed, 1 skipped), `editorial --check` ("current, nothing written"), `quality check`, `corpus check`, `aliases check`, `ratchet --dry-run` (nothing).
  - `VERIFY_SHARED=1 ./scripts/verify.sh`, with the combined ingest linked and `RTM_REPORT_DIRS` naming the report-#2 worktree: **All checks passed**, tree clean afterwards (`rtm-reviewbatch3b-1016-*.log`).
  - My first attempt ran without the report worktree, so verify read the study from the shared checkout, which does not have #2. It then failed corpus, aliases, quality and anchors, because aggregate reverted the site copy. That is the merge-order constraint in "Integrator steps", seen live.
- **Ship rehearsal**: `pnpm ship --plan --state build/review-ship-state.json` exited 0 (`rtm-reviewbatch3-1016-shipplan.log`).
  - It lists the study as "text differs from the site's copy", as expected from a trial branch.
  - The guard stopped only on "not main" and "behind origin", both expected on a trial branch.
- **Marks**: `pnpm marks check us-senate-cia-torture-report --baseline <v0.27.0 prerender> --candidate <#330 prerender> --marks-file build/marks-check/marks.json` (`-marks.log`).
  - Marks 14/14, links 3/3, editorial 34/34: 0 fail, 0 regress.
  - The export is from 2026-10-10, so the integrator re-runs it with `--refresh` (checklist step 7).

## Claims checked

| Claim | Result |
|---|---|
| severed-into-quote 101 → 23 | **True.** I recounted with the signal's own code on both `full.md` files (`rtm-reviewbatch3-1016-advisory.log`). |
| severed-paragraph 57 → 24 | **True.** Same recount. |
| No word of the study's text changes | **True.** I stripped `%%page N%%`, leading `> ` and `- `, then diffed word by word: empty (394,452 words each side). The 709 page markers are in the same order. Some sit later in the text, after a quotation now joined over the page: the existing marker-after-joined-block convention (e.g. p.19 after Hart's testimony). `corpus-baseline` words 402,168 → 402,167 is the rendered count of one fewer block, not a lost word. |
| All 17 other reports byte-identical | **True** for #79 and #80 combined (`ingest try`, above). |
| Aliases: 106, 36 new | **True.** 70 on main + 36 = 106. `aliases check` passes; 15 `moved_out` lines change. |
| `redacted-email-from-redacted-redacted` reused, cited by nothing | **True, and harmless.** See finding 3. |
| Advisory severed-paragraph-capital 107 → 117, explained | **Mostly right; incomplete for 4 of the 10.** See finding 4. |
| Goldens p.47/83/618 fail on v0.27.0 | **True.** None of the three `must_contain` strings is in v0.27.0's `full.md`. |
| `quote-share-parity` 2 → 1, `bare-footnote-marker` 82 → 81 | True (`quality check` against the ratcheted budgets). |

## Rendered pages (local `wrangler dev`, the #330 prerender)

- **Printed p.18-19, Hart's testimony** ("It has never fallen to my lot … To me it is an abomination, and I am happy to say …"): now one `<blockquote>`, with `#page-19` after it. Before, it was two quotations.
- **Printed p.54**: one `<p id="redacted-combatant-command-alert-command" data-page="54">` running "… According to the CIA inspector general, the detention and interrogation of Ridha al-Najjar …". Before, this was a quotation plus a lower-case paragraph.
- **Study Claim / Fact items**:
  - All 64 labelled items in `full.md` are paragraphs. In the rendered page, 0 of the 70 `<p id="study-claim-*|fact-*">` sit inside a `<blockquote>`.
  - Samples: p.581 `study-claim-f-redacted-least` (the no-gap case `labels` exists for), p.589 `study-claim-redacted-review-cia` and `fact-f-f-redacted-1`, p.597 `study-claim-redacted-t-cia`.
  - The longest item is 861 characters, so no item swallowed the body text after it.
  - Residue the author recorded: a Fact whose sentence the OCR cut into note garble (p.593, "helped lead to the" / "167 '"7 See CIA …").
- `?p=18` and `/full?p=54` answer 200.

## Verdicts

| PR | Head | Verdict | Reason |
|---|---|---|---|
| ingest #80 | 5d681fc4 | **MERGE**, release as 0.28.0 with #79 | Both passes are opt-in: a pass name in `KNOWN_PAGE_PASS_NAMES`, `resolvePasses`, and nothing in a shared default. `hangingLabels` runs only when declared. With `labels` the generic capitalised-label rule still applies as well (documented). `insetOpenings` is same-page, needs no marker in between, and stops at 400 characters. It skips a quotation that ends its sentence, opens on a quotation mark or is title-cased, and needs a lower-case next block. Tests cover each refusal. Corpus effect: none outside the study. |
| report #2 | a6b708a9 | **MERGE after the 0.28.0 pin bump is on its main** | Byte-for-byte reproducible with the combined ingest. Goldens are verified and fail on v0.27.0. The `PROCESSING.md` Known-limitations line is honest about the residue (Appendix 3 columns, pp.465-497; draft cables pp.673-677). It does not touch `package.json`, so the pin bump merges under it cleanly. |
| site #330 | **2ab07a68** | **MERGE immediately after the site pin-bump PR** (same sitting, before `pnpm ship`) | Both fixups are pushed. With them, verify passes on today's main. Ratchet is committed; nothing is left to lower. |

## Findings, by severity

1. **#330, medium: site copy of `PROCESSING.md` missing (fixed, 2ab07a68).** Report #2 edits `PROCESSING.md`, but #330 carried only `full.md`. verify's aggregate copied the new file over `reports/us-senate-cia-torture-report/PROCESSING.md`, so the tree was dirty and ship's `committed` gate would stop. The fixup adds the file, identical to #2's.
2. **#330, medium: page-anchor budget over by one (fixed, 2ab07a68; check bead reportsthatmatter-bpcc).**
   - verify failed "page markers over budget": `blocks-wrong 147 > budget 146`. Only `verify.sh` runs this check; the author ran `pnpm ingest verify`, which does not.
   - Cause: the quotation "As Zubaydah himself explained…" (printed p.47) is now one block over pp.47-48. Its words are also quoted in note 2647 (p.485), and the check locates it there. The text is right and this is a miss of the check (`anchors --md` listing diff: that one line).
   - Fixup:
     - `blocks-wrong: 147` with a `# why:` naming it.
     - `markers-wrong` 6 → 5, a real gain, ratcheted by hand to the measured count. stacked 6 and unmarked 22 are unchanged.
     - The lesson file below.
3. **#330, note: the reused id is a slug collision, and harmless.**
   - On v0.27.0, `redacted-email-from-redacted-redacted` named the paragraph "[Redacted] email from: [Redacted] to: [Redacted] D A C T E D ] … Ramzi bin al-Shibh, Khallad bin Attash and Adnan al-Libi were threatened with rectal re[hydration]".
   - insetOpenings joined the quotation two blocks above it ("> [Redacted] email from: … In") to "addition to the rectal rehydration or feeding of al-Nashiri…". The joined block now opens with the same five slug words, takes the base id, and the old text moves to `-2`.
   - `addition-rectal-rehydration-feeding-al-nashiri → redacted-email-from-redacted-redacted` is therefore the right alias for that text.
   - An old link to the reused id lands two paragraphs above its text, on the same page (p.100, section H).
   - Nothing cites it: I grepped editorial, queue, build and generated files. Marks check: 0 regress. Accepting it was right.
4. **#80 / #2 / jc46 notes, low: the advisory explanation covers 6 of the 10 new severed-paragraph-capital findings** (`rtm-reviewbatch3-1016-advisory.log`).
   - 6 match the PR's explanation (pp.54, 138, 309, and three more). The block after the page break was a quotation opening "> [Redacted]…", which the signal skips; insetOpenings made it a paragraph, so the pair now counts. The break was already severed.
   - The other 4 (three "DIRECTOR HAYDEN:" blocks in Appendix 3, pp.478/482/484, and "CIA representations related to the 'identification' of the Parachas", p.354) are a different mechanism:
     - quoteListRunOns joined a quotation over a page break, and the `%%page%%` marker moved after the joined block.
     - The existing unfinished-quotation → capitalised-paragraph pair, formerly on one page, now has a page marker between, so it counts as "crossed".
   - Neither kind is a new break in the reading. No action beyond this note; I comment it on the PRs.
5. **#330, note:** the ratchet dropped the three `# why:` comments on the lowered study budgets. That is the ratchet's behaviour (a why is needed only on a raise), so nothing to do.
6. **jc46, note: acceptance not met, so do not close on ship.** The bead's bar is severed-paragraph ≤ 13; it is 24. The author recorded the residue in the bead (Appendix 3 columns about 13, draft cables about 6, others) and in `PROCESSING.md`. That is "converge and ship" as AGENTS.md means it.
7. **Oracle hint, trivial:** `ingest verify` says the oracle budget can be ratcheted (markers-unlinked 523 < 525). This is optional and not in any PR.

## Found only by the trial merge

- Findings 1 and 2. Only the full `verify.sh` (the aggregate step and the anchors gate) catches them; the author's gate ran `pnpm ingest verify` plus the site checks.
- The order constraint: on a site whose study repo does not yet have #2, `verify.sh` and `ship` aggregate the v0.27.0-shaped text back over #330's. Report #2 must be on the report repo's main before ship runs.
- #80 and #79 combine with no conflict. The combined tree is identical to post-merge `main` + #80.

## What moves

- **Only the study.**
  - 2,408 → 2,407 paragraphs (counted by the rendered corpus baseline); `full.md` diff +132/−392 lines.
  - Ids move in 11 sections: 38 lost, 39 new, 36 aliased, 1 reused and accepted, 2 newly unmatched (`fact`, `provided-information-associate-ksm-name-2`).
  - No words change.
- **D1:** one publish (57 objects, 7.6 MB, offline dry-run). The reindex rewrites only the changed passages: about 110 blocks changed, split, new or lost, so well under 1,000 row writes. The reads are negligible.
- **Deploy:** ship's normal deploy (prerendered text changes).

## Integrator steps

Use the shared checkouts on `main`, `unset RTM_REPORT_DIRS RTM_REPO_ROOT`, and run `git -C <each> pull --ff-only` first (integrate-and-ship).

1. Check the heads:
   ```bash
   gh -R reportsthatmatter/ingest pr view 80 --json headRefOid -q .headRefOid   # 5d681fc4…
   gh -R reportsthatmatter/us-senate-cia-torture-report pr view 2 --json headRefOid -q .headRefOid   # a6b708a9…
   gh pr view 330 --json headRefOid -q .headRefOid   # 2ab07a68…
   ```
   A newer head needs a re-review. Ingest #79 is already merged (8ace7c2).
2. Merge the ingest PR: `gh -R reportsthatmatter/ingest pr merge 80 --squash`.
3. Release, in `~/src/reportsthatmatter/ingest`:
   ```bash
   git pull --ff-only
   pnpm release 0.28.0 --dry-run
   pnpm release 0.28.0
   ```
   This is a minor release: two new passes, plus #79's check.
4. Bump the pin, from the site's main checkout on a new branch (e.g. `git switch -c pin-0.28.0`):
   ```bash
   pnpm bump-pin 0.28.0 --dry-run
   pnpm bump-pin 0.28.0 --shared
   ```
   - Check that each `pnpm-lock.yaml` names v0.28.0's commit.
   - Commit and push each report repo's `package.json` and `pnpm-lock.yaml` on its `main` ("Bump @rtm/ingest to v0.28.0"), the study's included.
5. Merge the report PR: `gh -R reportsthatmatter/us-senate-cia-torture-report pr merge 2 --squash`. It does not touch `package.json`, so it merges cleanly over the step-4 pin commit. Then `git -C ~/src/reportsthatmatter/us-senate-cia-torture-report pull --ff-only` and `pnpm -C ~/src/reportsthatmatter/us-senate-cia-torture-report install`.
6. Open the site pin-bump PR with `package.json` and `pnpm-lock.yaml`. Its body carries `pnpm scorecard` and this note: "17 reports unchanged (review batch 3 `ingest try`); the study moves via #330". Merge it.
7. **Straight after it**, `gh pr merge 330 --squash`.
   - Main is now consistent: the site pin, the study repo and the site copy of the study all say 0.28.0.
   - Do not leave main between steps 6 and 7. verify on main fails there: the study's site copy is v0.27.0's while its repo is #2's.
   - If #330 conflicts because main moved again: for `reports/corpus-baseline/us-senate-cia-torture-report.json`, take main's and run `pnpm corpus accept us-senate-cia-torture-report`. For `src/generated/*`, take main's and run `pnpm editorial` / `pnpm cards`.
8. `git pull --ff-only && pnpm bootstrap && pnpm ship --reset && pnpm ship --plan`. Check:
   - every repo "pin ok";
   - the guard lists no problem;
   - the study is "text = site copy";
   - nothing else differs.
9. `pnpm ship --shared`, then read each gate before acking it.
   - The baseline/corpus gate should name only us-senate-cia-torture-report.
   - The fidelity checks print "no control characters — none" for all 18. A ✗ there is a real defect to bead, never to accept.
   - The anchors gate should pass at study blocks-wrong 147 / markers-wrong 5.
   - Run `pnpm marks check --all --refresh` when step 7 of the checklist comes up (my replay used the 2026-10-10 export).
10. `pnpm ship --shared --yes`: publish, deploy, reindex, seed, verify-prod, record. Then:
    - `pnpm publish-report --all --status --base https://reportsthatmatter.org` lists nothing.
    - `curl -sD- -o /dev/null https://reportsthatmatter.org/reports/us-senate-cia-torture-report/full | grep -i x-rtm-content-version` is the new hash.
11. Beads:
    - `bd close reportsthatmatter-5p44 --reason "Shipped in ingest v0.28.0"`.
    - **Do not close reportsthatmatter-jc46.** severed-paragraph 24 is still over its 13 bar. Append instead: `bd update reportsthatmatter-jc46 --append-notes "Shipped in ingest v0.28.0 / site <sha>: severed-into-quote 23, severed-paragraph 24; residue as recorded (Appendix 3 columns, draft cables)"`.
    - `bd dolt push`.
12. Housekeeping between sessions: `pnpm worktrees prune --apply`. This removes the review worktrees `rtm-reviewbatch3-1016` (+`-reports`), `rtm-reviewbatch3b-1016`, `rtm-reviewbatch3fix-1016` and `ingest-reviewbatch3-1016`.

## Beads

- Filed: **reportsthatmatter-bpcc** (P3, bug, stream:quality, level:specced, handoff, discovered-from jc46). The page-anchor check takes a block whose text recurs (a quotation repeated in a note) to the wrong page. The fix is to prefer the occurrence nearest the block's marker.
- Noted on jc46 and 5p44: the verdict, the heads and this file.

## Lessons

- On the PR branch: `docs/design/lessons/2026-10-16-site-copy-of-report-docs-and-full-verify.md`. A report-repo PR that edits `PROCESSING.md` needs its site copy too, and a re-ingest needs the full `verify.sh`: only that runs the aggregate and the page-anchor budget. `tests/lessons.test.ts` passed.
- Two kinds of moved pages drive an advisory signal after a join. Before writing the explanation, list the new findings against the old (`rtm-reviewbatch3-1016-advisory.log` took 20 lines of tsx).

## Getting better faster

- **What slowed me:**
  - Main moved twice under the review: batches 1-2 and ingest #79 merged mid-way, so I re-ran the site trial.
  - `pnpm ingest worktrees` refused a second worktree for the same report on the same day (the branch name `wt/<repo>-<date>` collides), which cost one failed verify run.
  - There is no ready-made "which findings appeared" diff for one quality signal between two texts.
- **What would have caught the defects earlier:**
  - The author's gate ran `pnpm ingest verify` and the site checks, not `VERIFY_SHARED=1 ./scripts/verify.sh` with the linked ingest. Both findings show up only in the full run.
  - `pnpm ingest try` could run the anchors gate per report (bead 11g7 already asks for this).
  - A test that fails when a report repo's PR touches `PROCESSING.md` but the site copy differs.
- **Proposal:** add `pnpm quality findings <id> --md-a <old> --md-b <new> [--signal s]`, printing each signal's appeared and vanished excerpts between two `full.md` files. `ingest try` has this inside its findings diff, but only for its own two sides. With it, an author can explain an advisory rise from the list, not from a guess.
