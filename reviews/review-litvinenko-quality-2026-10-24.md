# Agent review: the Litvinenko Inquiry's headings, ingest #87, uk-litvinenko-inquiry #13, site #397 (2026-10-25)

Reviewer: Opus agent. I did not touch production: no `wrangler d1`, no publish, no seed, no reindex, no deploy, no merge, no tag, no release, no `marks check` against production D1. `pnpm ship --plan` ran with a scratch state file only.

**Verdict: MERGE all three, with my two fixup commits; release @rtm/ingest as the next free minor (v0.32.0 unless ingest #88 is released first), bump every pin, publish litvinenko-inquiry only.**

## Scope

- **ingest #87** (`litvinenko-quality-1024`): head **36d86d0** at start and end (no fixup). `runningFurniture({ keep })`, `typographicHeadings({ commaWraps, skipMarked })`. Based on v0.31.0 (`33f139d`).
- **uk-litvinenko-inquiry #13** (`litvinenko-quality-1024`): **adc7941** at start, **87e557a** after my fixup. Still pins `#v0.31.0`.
- **site #397** (`litvinenko-quality-1024`): **49d35ab3** at start, **7501cac5** after my fixup. Based before main `9853540f` (#392); the trial merge with main is clean.

## Trial

- **ingest** `ingest-litvq-review-1024` (branch `triallitvq-review-1024`) = origin/main + #87 (`--no-ff`). `pnpm build` leaves `dist/` unchanged; `pnpm test` 955/955.
- **site** `rtm-litvq-review-1024` (branch `triallitvq-review-1024`) = origin/main `9853540f` + #397, then + its fixups. **Report repo** `rtm-litvq-review-1024-reports/uk-litvinenko-inquiry` = origin/main + #13 + fixup. Trial ingest linked with `pnpm ingest link`, restored afterwards.
- **Opt-in / corpus effect** (`pnpm ingest try ../ingest-litvq-review-1024`, all 20 reports; log `factory/logs/2026-10-24/rtm-litvq-review-1024-try.log`): 19 of 19 other reports "rendered text unchanged"; score decisions 0 flips for every report with a reference; no page changed source or folio read. `try` re-ingested litvinenko from the shared checkout's `main` `ingest.ts`, and its output is main's `full.md` (the diff it prints is exactly the reverse of #397's), so the options are inert unless declared. Code agrees: `commaWraps` and `skipMarked` are gated on `faces`, `keep` on being passed.
- **Reproducibility:** with the trial ingest linked, `pnpm ingest run litvinenko-inquiry` on #13's head reproduces #13's `full.md` byte for byte (before my fixup); after the fixup the report repo's `full.md` equals the site copy (`cmp`).
- **Checks on the final trial** (logs `factory/logs/2026-10-24/rtm-litvq-review-1024-*.log`): `pnpm typecheck` pass; `pnpm test` pass (1421 + 1 skipped on the fixup branch); `pnpm quality check` all 20 within budget; `pnpm corpus check` pass (after `corpus accept litvinenko-inquiry` on the branch); `pnpm editorial --check` current; `pnpm aliases check` pass; `pnpm ingest check` pass; `pnpm ingest verify litvinenko-inquiry` pass (golden 5/8, 3 known: 2nx x2, nhs6); `pnpm quality ratchet --dry-run` nothing to lower; `pnpm marks check litvinenko-inquiry --no-marks` 39/39 anchor; `RTM_REPORT_DIRS=… VERIFY_SHARED=1 ./scripts/verify.sh` "All checks passed" (`rtm-litvq-review-1024-verify.log`). `git status` clean after.
- **Grade:** oracle headings-missed 220 -> 52 (#397) -> **51** (with fixup), headings-spurious 0 -> 1. With a litvinenko-only verify log (precisions fall to priors) `pnpm grade litvinenko-inquiry` gives **C 7.89** (H 0.83); the PR's C 4.95 uses the pooled corpus precisions. C either way; the integrator's `pnpm grade --record` after the release gives the number of record.
- **`pnpm ship --plan`**: as expected; publish step lists litvinenko-inquiry.

## Verdicts

| PR | verdict | reason |
|---|---|---|
| ingest #87 @ 36d86d0 | MERGE, release minor | Opt-in proven on 20 reports; small, gated, tested (each option fails without it). |
| uk-litvinenko-inquiry #13 @ 87e557a | MERGE after the release, then pin bump | All 168 added headings are in a declared heading face in the PDF; no caption became a heading; fixup declares Notice No. 4's face. |
| site #397 @ 7501cac5 | MERGE after the pin bump re-ingest reproduces it | Text, sections, aliases and budgets read and acceptable; alias-gen change correct and only reached by reports re-ingested; fixup recovers 8 lost ids. |

## Headings against the PDF

- **Every** heading the PR adds (168 lines `+#` in the `full.md` diff) was checked by script against the layout cache (`.cache/layout-*.xml`, every line ≥20pt with its face): each one's text is a line, or consecutive lines, in one of the six declared faces. None is in an undeclared face, none is a running head (`The Litvinenko Inquiry` 17pt) and none is in italic quotation faces.
- **Captions:** all 13 figure captions in ArialMT 22 blue (pp.128-130, 145 x2, 169 x2, 172, 177, 178 "The teapot", 202) are absent from the headings (`skipMarked` works). The 22 genuine 22pt topics (e.g. "The pathologists" p.48, "A set up?" p.208, "Which reactor(s)…" p.225) are h5.
- **Read on the page** (`pnpm ingest page litvinenko-inquiry 1 N`): p.4 contents ("Contents" h2, the five group labels "Introductory", "Open narrative evidence"… h4 in ArialMT 26 blue: faithful, but contents-page headings; acceptable), p.7 "Appendices", p.81 "Andrey Lugovoy" (26pt topic, quotation below stays a quote), p.248 Appendix 1 "The police investigation" (chapter face, then 17 more Appendix 1 subheads), p.268-269 Appendix 3, p.298-299, 307 Appendix 7, p.316 Appendix 8 group labels "(Analytical documents)" etc. (22pt blue group rows of a two-column list; h5 under h2, a level skip; acceptable), p.324 Appendix 10 (Counsel / Solicitor / Secretariat), p.326-327 Appendix 11 (the nine core participants). All match print. Also spot-read: Whistleblower (p.22), Treatment in Barnet Hospital (p.35), 18 October: return to Moscow, Childhood, Accident, Arrival of Andrey Lugovoy and his party, Chapter 5: Mario Scaramella, Chechen groups, Alexander Talik (p.214), Part 5 (p.86), the five "Chapter 1: Introduction".
- **Word diff** main -> PR: +5 "Chapter 1: Introduction", "Part: 5" -> "Part 5:" (c-0001, allowed by 8rv's acceptance), nothing lost.

## Findings, by severity

Fixed on the branches:

1. **Restriction Notice No. 4's title stayed a paragraph, so its three sections sat under Notice No. 3** (p.307: the title alone is Arial 26 bold black, undeclared). Fixed: uk-litvinenko-inquiry **87e557a** adds `Arial|26|#000000|b` to the chapter level (the face sets only that one line in the PDF); site **7501cac5** re-ingest: new section `restriction-notice-no-4` split from `restriction-notice-no-3`, `restriction-notice-no-4` paragraph id aliased, headings-missed 52 -> 51, corpus baseline accepted, budgets unchanged.
2. **alias-gen left every sub-heading printed more than once unmatched**, losing 4 aliases that worked on main (`conclusion-8-58-reasons-set`, `conclusion-9-111-my-conclusion` and their targets) plus the four "Amendments to this Restriction Notice". Fixed in **7501cac5**: a paragraph that became a heading printed more than once pairs with those headings in reading order, only when as many such paragraphs are gone as there are headings now (otherwise unchanged: left to the containment probe). Test added. Litvinenko: 523 -> 532 aliases, 779 -> 771 unmatched; targets checked (`conclusion` -> 8.58, `conclusion-2` -> 9.111, amendments 1-4 -> each notice's "15 A letter…").

Not fixed, with beads:

3. Appendix 3's "B." and "C." (p.268) stay glued mid-paragraph while A. and D. are now headings: **reportsthatmatter-17ui**. Pre-existing glue; the inconsistency is new and minor.
4. Appendix 7: each notice's "Introduction" sub-heading is missing (on main too), the five "RESTRICTION ORDER" titles go as furniture (recorded in `ingest.ts`), and 20 notice ids (identical body text in all four notices) plus `alexander-talik`, `andrey-lugovoy` stay unmatched: **reportsthatmatter-ox1f**. Unmatched is shown, not graded (decision 0035).

Judged acceptable:

- **Alias-gen safety for other reports:** `headingTexts` is new data on `Rendered`; `becameHeading` runs only for an old id that is no longer live and whose text (≤20 words) equals a heading, or is a heading's opening (≥2 words). It takes precedence over `locate`, which could misroute only a short paragraph whose id changed while its text survives as a paragraph and also as a unique heading; I found no such case and it only runs when a report is re-ingested and aliases regenerated. No other report's aliases change in this release (no other text moves).
- **Section restructuring** (`corpus check`): front-matter -> contents (aliased), new Part 5, Part 9 Chapter 5, nine Appendix 1 sections, Notice 2/3/4 sections; Part 5 Chapter 2 and Part 9 Chapter 4 fold into their Part (aliased via `sections`). 0 unmatched sections.
- **Hand-raised budgets** (`# why`): note-text-in-body 56 -> 67 (the notices' "1 This Restriction Notice…" paragraphs, a signal false positive once their titles became headings), heading-density 0 -> 1 (229 headings / 329 pp, every one verified in a heading face), heading-repeated 0 -> 4 ("Chapter 1: Introduction" x5, the notices' repeated titles, as printed), heading-long 2 -> 6 (the 113-character "Restrictions on the Disclosure…" title x4, as printed). Justified: men's acceptance asked for no rise as a proxy for "no caption became a heading", which the face check above answers directly.
- **Ingest code:** clean and gated. Nits, no action: `keep` with a `/g` regex would be stateful under `.test` (document or strip flags if reused); the `joinBlocks` path can turn a `quote` block into a heading if its text is exactly a heading's prefix run (needs `commaWraps` and a face match); the `Required<Omit<…>>` type in two places grows by two more keys each time an option is added (a named type would help).

## What moves

- litvinenko-inquiry only: +169 headings, ~161 new ids, ids lost all aliased or listed unmatched (0 reused), 13 new sections. 19 other reports byte-identical.
- **D1:** one report republished; reindex is incremental (changed paragraphs only, a few hundred rows); no seed change (editorial anchors 39/39).

## Integrator steps

1. Merge ingest #87 (squash). From `~/src/reportsthatmatter/ingest` `main` (pulled): `pnpm release 0.32.0`. If ingest #88 (Leveson, `facingPageMargins`) was released first, use the next minor (v0.33.0) and, from a site worktree, re-run `pnpm ingest try <ingest main>` to prove the 18 reports other than litvinenko and leveson unchanged.
2. Merge uk-litvinenko-inquiry #13 (head 87e557a).
3. From a site worktree on main: `pnpm bump-pin 0.32.0` (add `--shared` for shared report checkouts), `pnpm bootstrap`, `pnpm ingest preflight`.
4. Merge site #397 (head 7501cac5) into that worktree (or merge first and bump after). Then `pnpm ingest run litvinenko-inquiry` with the released pin and check `git status` shows no change to `reports/litvinenko-inquiry/full.md` (it must reproduce 7501cac5's text), `pnpm ingest check`, `pnpm corpus check`, `pnpm quality check`, `pnpm aliases check`, `VERIFY_SHARED=1 ./scripts/verify.sh`.
5. Before publishing: `pnpm marks check litvinenko-inquiry` against production (stored marks replay; needs `--yes`), expecting no mark to stop anchoring.
6. `pnpm ship --shared` (publish litvinenko-inquiry; reindex it; record: `pnpm quality ratchet --record`, `pnpm scorecard --record`, `pnpm grade --record`, expect litvinenko C).
7. Close reportsthatmatter-men and reportsthatmatter-8rv on ship. Leave reportsthatmatter-17ui, -ox1f, -nhs6 open.

## Beads

- Filed: **reportsthatmatter-17ui** (Appendix 3 B./C.), **reportsthatmatter-ox1f** (Appendix 7 residue and its unmatched ids), both `discovered-from:reportsthatmatter-men`.
- Noted: verdict and this file on men and 8rv.

## Lessons

- A face declared at one level can miss a one-off face for the same role (Notice No. 4 in 26pt, not 30pt). Listing every line ≥ body size by face across the PDF (a 20-line script over the layout cache) found it in a minute; this is the lesson file in #397 ("list every line the face sets"), applied to undeclared faces too.

## Getting better faster

- Slowed by: `pnpm ingest try` re-ingests a report from the shared checkout, not `RTM_REPORT_DIRS`, so it cannot show a declaring report's own change; I had to re-ingest separately.
- Would have caught it: a `pnpm ingest faces <id>` command (each face over body size, its line count, pages, and whether declared) beside `ingest page`.
- Proposal: add `pnpm ingest faces` and make `try` honour `RTM_REPORT_DIRS` for the trial side.
