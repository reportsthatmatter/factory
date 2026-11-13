# Agent review of Stern batch 2b (Chapters 11, 12, Part IV introduction, Chapters 14-17): uk-stern-review#4, site #451 (2026-11-13)

Reviewer: Opus agent. I did not touch production: no merge, release, tag, pin bump, `pnpm ship` beyond `--plan`, publish, deploy, reindex, seed, `wrangler d1 --remote` or production D1 read (`marks check --no-marks`; `publish-report --dry-run --offline`).

**Verdict: MERGE AND SHIP both (uk-stern-review#4 → #451 → `pnpm ship --shared`). No must-fix. No ingest release or pin bump: both already on v0.37.0.**

## Scope

- **reportsthatmatter/uk-stern-review#4** (`stern-b2b-1113`): **beae830** at start and end. Five commits on report main `5424b72` (v0.37.0 pin): two WIP saves (the author was cut off), the Annex 11A.1 p.268 drop, the Chapter 11 note 5 run-over (c-0050), line-end hyphen corrections and PROCESSING/README.
- **reportsthatmatter/reportsthatmatter#451** (`stern-b2b-1113`): **9b7ce78a** at start and end. Six commits on main `36e61057` (0 behind): full.md, id ledgers, budgets, corpus baseline, pipeline row, reading guide (+ `src/generated/editorial.ts`), lesson (qba4).
- Fixups pushed: none needed.

## What the PRs do

Add seven volumes in table-of-contents order: Chapter 11 (with Annex 11A), Chapter 12, the Part IV introduction, Chapters 14-17. Stern goes from 299 to 425 pages, 147,745 to 212,578 words (baseline count), 115 to 172 headings, 2,099 to 3,097 paragraphs, 857 notes. Volume indices after Chapter 10 shift by two (Chapter 13 is now volume 20); the author retargeted the Table 13.2 golden page and page-scoped corrections and filed qba4 so a future insertion retargets itself. Two new golden pages (Ch 14 p.320, Ch 16 p.372). 20 corrections (c-0048..c-0067): 18 line-end hyphen joins, c-0049 (a raised 0 printed for a degree sign, "20C" to "2°C", Ch 12 p.276) and c-0050 (Ch 11 note 5's five run-over lines at the foot of p.256 set back on the note; text checked word for word against the PDF, appears once in full.md). Annex table 11A.1's second page (p.268) rows are dropped by a page window, its note kept, as for every other Stern table (6uqx).

## Trial

- Site worktree `~/src/reportsthatmatter/rtm-review451-1113` (branch `trial451-1113` = origin/main `36e61057` + `origin/stern-b2b-1113`, merge `ce1aae5c`, no conflict), `pnpm bootstrap`.
- Report worktree `~/src/reportsthatmatter/rtm-review451-1113-reports/uk-stern-review` via `pnpm ingest worktrees uk-stern-review --branch trial4-1113` (= report main `5424b72` + `origin/stern-b2b-1113`, merge `c41c142`, no conflict), `RTM_REPORT_DIRS=/Users/rgrp/src/reportsthatmatter/rtm-review451-1113-reports`. No `VERIFY_SHARED` (the shared Stern checkout lacks batch 2b).
- **Re-ingest reproduces**: `pnpm ingest run uk-stern-review` leaves the report worktree clean (full.md and fidelity.md byte-identical; 209,978 words, 93.4% of source; every fidelity check passes). `pnpm ingest aggregate uk-stern-review` leaves the site clean. Log `factory/logs/2026-11-13/rtm-review451-1113-run.log`.
- **`pnpm ingest verify uk-stern-review`**: golden 18/20 + 2 known (lb9l, Executive Summary pp.3-4, unchanged); oracle markers-unlinked 514 = budget. Log `…-ingestverify.log`.
- **Ids**: `pnpm aliases generate uk-stern-review` changes only the first-recorded timestamps of the seven new sections in `reserved-ids.txt` (`## 10:00:13Z` to `## <now>`; no id or fingerprint line differs); reverted. `pnpm aliases check`: uk-stern-review 3,262 recorded ids, 15 aliases, 0 unmatched. The PR's `published-ids.txt`, `published-sections.txt` and `reserved-ids.txt` only add lines (+1063, +7, +1070, 0 removed). The corpus baseline only adds sections and counts.
- **Chapter 13** (the efso case): its rendered body (heading to its References' end) is byte-identical between main and the PR (436 lines; the only difference after it is Part IV's `%%page 308#2%%`). Paragraph-to-section map, main prerender against the trial's: 0 paragraph ids lost or moved section. What moves is apparatus only: 26 sidenote anchors (`sn-N-M`, renumbered as notes are inserted; 9 gone, 17 moved), 4 positional note anchors (`note-4-11`, `note-9-3`, `note-10-3`, `note-25-7`: Ch 13 to Ch 17) and `page-284` (Ch 10 to Ch 12, now the right chapter). These are not citable paragraph ids and were moved the same way by batch 2a.
- **Checks**, all exit 0 (logs `factory/logs/2026-11-13/rtm-review451-1113-<name>.log`): `pnpm ingest check` (23 ✓), `pnpm corpus check` (23 unchanged), `pnpm quality check` (all within budget), `pnpm editorial` (no change: `src/generated/editorial.ts` is current), `pnpm editorial --check`, `pnpm posts --check` ("every not-yet-posted item still resolves"; the 5 Stern posts included), `pnpm cards --check` (138 cards match), `pnpm marks check uk-stern-review --no-marks` (links 5/5, editorial 52/52 anchor, 0 fail).
- **Grade** (`pnpm grade uk-stern-review`): **A, 0.28 per 10 pp, publishable** (425 pp; was A 0.37 at 299 pp). grades.json/md reverted (recorded at release).
- **verify.sh**: VERIFY_PENDING
- **Ship rehearsal** (`pnpm ship --plan --state build/review-ship-state.json`): every repo's text equals the site copy; 0 of 23 repos need a pin bump; the guard stops only because the trial is not main and the Stern worktree is on `trial4-1113`.
- **Publish dry run** (`pnpm publish-report uk-stern-review --dry-run --offline`): 28 objects, 4.4 MB, content hash **bf914739b6cfc25f** (live is 71a81fd5b81c61a6).

## Budget raises, each read against the page

| Budget | Was → now | Checked |
|---|---|---|
| anchor markers-wrong | 1 → 2 | Part IV introduction prints folio 308 (pdftotext), as does Chapter 13's last page; both correct, as the table of contents sets them. |
| oracle markers-unlinked | 472 → 514 (+42) | 40 formula subscripts; Ch 15 p.342 "0.7 Gt CO255" in the PDF text is CO2 + note 55 (served `CO2[^55]`); Ch 12 p.276 raised 0 for a degree sign (c-0049). |
| quality bare-footnote-marker | 0 → 1 | Ch 17 reference "Grubb, M.(1990): 'Energy policies and the Greenhouse Effect, vol.1 Policy Appraisal'" in the PDF. |
| quality furniture-paragraph | 11 → 17 | "Key Messages" box title, one per new chapter (6). |
| quality heading-long | 3 → 4 | "14.9 The performance of taxation and trading against principles of efficiency, equity and public finance considerations", p.320 (golden page). |
| quality printed-page-reversal | 1 → 2 | Chapter 11's reissue prints folios 1-16 (pdftotext: first page 1, last 16), after Ch 10's p.252 (6u5h). |

## Reading guide

Seven new entries (Ch 11, 12, Part IV, 14-17). Every quote is verbatim in full.md and in the chapter PDF's text (normalised whitespace; script `factory/logs/2026-11-13/rtm-review451-q.mjs`); each `paragraph:` id anchors (marks check editorial 52/52). The "why" lines match the chapters' Key Messages. Minor, not fixed: Chapter 14's quote opens "It concludes that…", whose "It" is Section 14.4 in the sentence before; it reads acceptably under the "why" line.

## Spot-read (`pnpm paragraphs uk-stern-review <words>`)

Ch 11 "The costs of mitigation will not be felt uniformly…" (cited p.1: the reissue's own folio, 6u5h, as Chapter 5); Ch 12 p.276 "…limiting global mean temperature increase to 2°C…"; Ch 14 p.310 Section 14.4 paragraph; Ch 14 p.320 "Under a tradable-quota system…", "In terms of the criteria discussed above…" and its list; Ch 15 p.324 "Both tax and trading…", p.342 "Aviation CO2 emissions currently account for 0.7 Gt CO2 (1.6%…". All clean, right section and page.
