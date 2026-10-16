# Review: licences (y2y8.9), 2026-10-16

Reviewer: Opus agent. I did not touch production: no merge, release, pin bump, deploy, publish, reindex or seed; no `pnpm ship` beyond `--plan` with a scratch state file; no wrangler call.

**Verdict: MERGE all 24 PRs, in the order below. The report PRs go first, then #345, then #347. I pushed fixups to #347 and to all 18 report PRs. Hillsborough and Lehman carry honest "not confirmed" wording; two needs-user beads cover what a person would have to ask.**

## Scope

| PR | Branch | Head at start | Head after fixups |
|---|---|---|---|
| site #347 | licence-1016 | 0edb5449 | e641857a |
| site #345 (decisions clerk; only its 0024) | decisions-1016 | eaa0904c | unchanged |
| ingest #81 | licence-1016 | 6d86382 | unchanged |
| visual-changelog #1 | licence-1016 | 311d431 | unchanged |
| factory #1 | licence-1016 | 7106bb6 | unchanged |
| design-experiments #1 | licence-1016 | f4eb30e | unchanged |
| challenger-accident #13 | licence-1016 | 929c20f | c17723b |
| us-columbia-shuttle-accident #10 (default `master`) | licence-1016 | d36bdb6 | 86739ed |
| jack-smith-report #13 | licence-1016 | e4c324d | 6de6705 |
| uk-chilcot-inquiry #12 | licence-1016 | ac3b5bd | ef1c4a4 |
| uk-grenfell-tower-inquiry #3 | licence-1016 | 1f524c6 | 177200d |
| uk-hillsborough-panel #13 | licence-1016 | 5ad943a | 5aee6b4 |
| uk-leveson-inquiry #11 (default `master`) | licence-1016 | d5429a9 | 881e723 |
| uk-litvinenko-inquiry #12 | licence-1016 | 0825c1a | 6403bd4 |
| uk-post-office-horizon-inquiry #3 | licence-1016 | ebdcecf | 900778b |
| uk-saville-inquiry #10 | licence-1016 | 2993818 | 7fe77e9 |
| us-911-commission #13 | licence-1016 | c4bba91 | c3fa9a2 |
| us-deepwater-horizon #11 | licence-1016 | 378264b | b899c62 |
| us-jan6-committee #4 | licence-1016 | 65d5cb3 | e8a1212 |
| us-lehman-examiner #12 | licence-1016 | 121a0fe | d4f3d83 |
| us-mueller-report #2 | licence-1016 | 50fc261 | dab6553 |
| us-psi-financial-crisis #15 | licence-1016 | 151b99e | a1c3f25 |
| us-senate-cia-torture-report #3 | licence-1016 | 24d0dc0 | fdb1e9b |
| us-v-philip-morris #11 | licence-1016 | 1304a1a | 15053b7 |

## Trial

- Site trial `~/src/reportsthatmatter/rtm-reviewlicence-1016` (branch `triallicence-1016` from `origin/main` 1678807c): merged #345, then #347 (at 610010f1, which has the same code as e641857a; the later commit adds only a lesson file). #349 (hbnj, which also edits `src/templates/about.ts`) also merges cleanly on top (`--no-commit`, then aborted).
- `pnpm prerender` 0; `pnpm typecheck` 0; `pnpm test` 0 (81 files, 1287 passed, 1 skipped); `pnpm decisions check` "numbering ok"; `pnpm quality check` 0; `pnpm corpus check` 0 (18 reports unchanged); `pnpm editorial` 0; `pnpm aliases check` 0. Logs: `factory/logs/2026-10-16/rtm-reviewlicence-1016-{bootstrap,prerender,typecheck,test,quality-check,corpus-check,editorial,aliases-check}.log`.
- #347 on its own (worktree `rtm-reviewlicence-1016-fix`): bootstrap, typecheck and test pass: `rtm-reviewlicence-1016-pr347alone.log`. The JSON-LD tests in `tests/seo-head.test.ts` pass. They expect `license` only when the registry has one (lines 148-149), so leaving it out for Hillsborough and Lehman is valid schema.org output.
- The prerendered `processing.html` for Hillsborough and Lehman carry the new licence lines. The old "no copyright restricts it" no longer appears anywhere in `assets/generated/`. I read /about's Licences section from `app.request`.
- `pnpm ship --plan --state build/review-ship-state.json`: every report shows pin ok, clean, and text = site copy. No pin bump and no re-ingest needed (`rtm-reviewlicence-1016-shipplan.log`).
- I did not run `VERIFY_SHARED=1 ./scripts/verify.sh`. No full.md, ingest code or pin changes; the gates it runs were run above.
- Report repos: I checked the evidence by running `pdftotext` on each repo's `archive/` PDF, not on a trial build. These PRs change only LICENSE, LICENSE-MIT and README, plus PROCESSING.md, data files and the manifest for Hillsborough and Lehman. `full.md` is untouched in all 18.

## Evidence check (each LICENSE section 1 against the report's own notice)

| Report | Notice in the source PDF (pdftotext) | LICENSE says | Result |
|---|---|---|---|
| Chilcot (HC 264) | "© Crown copyright 2016 / This publication is licensed under the terms of the Open Government Licence v3.0 except where otherwise stated" + third-party line | the same, quoted | matches |
| Grenfell (HC 19-I) | "© Crown copyright 2024 …OGL v3.0 except where otherwise stated" + third-party line | the same | matches |
| Litvinenko (HC 695) | "© Crown copyright 2016 …OGL v3.0 except where otherwise stated" + third-party line | the same | matches |
| Post Office (HC 1119) | "© Crown copyright 2025 …OGL v3.0 except where otherwise stated" + third-party line | the same | matches |
| Leveson (HC 780, vol. I) | "© Crown copyright 2012 / You may re-use this information (excluding logos) … under the terms of the Open Government Licence" (no version) + third-party line | the same; v3.0 cited as current | quote matches. **Fixed**: the file said "versions 1.0 and 2.0 were current in 2012", but OGL 2.0 dates from 2013. It now says 1.0 was the only version then, with 1.0's URL |
| Saville (HC 29-I) | "Original content of the report © Controller of HMSO 2010. The original content may be reproduced free of charge … provided that the title of the source publication is given and the copyright acknowledged" + s.46 third-party line | quoted; OGL v3.0 via GOV.UK | quote matches. The OGL claim rests on GOV.UK, not on the imprint, and the file says so. The site's PROCESSING.md records that the GOV.UK publication page itself carries the OGL notice (ivg.2). Accepted; it is not an overclaim |
| Hillsborough (HC 581) | "© Hillsborough Independent Panel 2012 / All intellectual property rights for this report belong to Hillsborough Independent Panel … without the prior written permission of the Editor-in-Chief: editorinchief@…" | "not confirmed; evidence conflicts" | quote matches (the trailing "." inside the quote replaced ": email", fixed). **New evidence, fixed**: the Panel's website terms page (Wayback 20140222031352, `hillsborough.independent.gov.uk/terms-conditions/`) offers the OGL only for "material on this website covered by Crown Copyright" and adds "All other intellectual property rights belong to the Hillsborough Independent Panel … In particular this relates to the Panel's Report." So the evidence does not conflict: the rights in the report are reserved. The GOV.UK publication page (API) names the Panel as publisher and carries no OGL notice |
| Columbia (CAIB vol. I) | no copyright notice; "Limited First Printing … by the Columbia Accident Investigation Board; Subsequent Printing … by NASA and the Government Printing Office"; one "© 2003" photo credit | treated as US government work, with a caveat | matches. **Fixed**: NASA's "generally are not subject to copyright" quote is from its media guidelines ("images, audio, video, and media files"), not about text; it now says so |
| 9/11 Commission | no copyright page in the PDF; "© Reuters 2004", "© Tamara Beckwith, New York Post" photo credits | treated as public domain, with a caveat; photos excluded | matches |
| Deepwater ("Deep Water") | "Copyright, Restrictions, and Permissions Notice: Except as noted herein, materials contained in this report are in the public domain. …" | quoted | matches word for word |
| Lehman (Valukas vols 1, 3) | no copyright notice in any of the 4 PDFs; "Jenner & Block LLP" on the cover | "not established; we do not say it is" | matches. 11 U.S.C. § 1104(c) is correct |
| Challenger (H. Rept. 99-1016) | one "Reprinted from the Annual Book of A S M Standards, Copyright ASTM" | 17 U.S.C. § 105; ASTM excluded | matches |
| Jan 6 (H. Rept. 117-663) | no notice; many "Photo by …/Getty Images" and House Creative Services credits | § 105; Getty/AP photos excluded | matches |
| Jack Smith, Mueller, PSI, CIA (S. Rept. 113-288), Philip Morris | no copyright notice (the only "©" in the CIA report is an OCR slip, "6:1© a.m.") | § 105 (Philip Morris: a federal court opinion) | matches |

The template is consistent. Sections 2 and 3 and "Other files" are byte-identical across the 18 LICENSE files. Every README Licence line follows one pattern ("Text of the report: …; markup CC BY 4.0; code MIT"). No README or LICENSE calls a report OGL when its notice does not; Saville is the edge case, and its file names its source. **Fixed in all 18**: the template did not say what `archive/` (the PDFs as published) is, so it now has the line "Files under archive/ are copies of the report as its publisher issued it …; they have the status described in section 1".

MIT: every MIT file (site LICENSE, ingest, visual-changelog, factory, design-experiments, and each report repo's LICENSE-MIT) has the same text as GitHub's `licenses/mit` template, with `2026` / `Rufus Pollock and contributors` filled in (`gh api licenses/mit`). Only a trailing newline differs. GitHub will detect MIT on the five code repos. The report repos' `LICENSE` is a custom statement, so GitHub will show "Other" there; that is intended. The holder makes sense: all git history is Rufus's. Datopian or Life Itself as holder is an open question for Rufus (below), not a blocker.

## Verdicts

| PR | Verdict | Reason |
|---|---|---|
| site #347 (e641857a) | MERGE after the 18 report PRs and #345 | Fixups below. The README links `docs/decisions/0024-licences.md`, which now only #345 provides. The site copies of the Hillsborough and Lehman PROCESSING.md match those PRs, so the report PRs must merge before any later `pnpm ingest aggregate`, or aggregate would copy the old text back |
| site #345 (eaa0904c), its 0024 only | MERGE; fold in #347's content (text below) on its branch or as an immediate follow-up | One 0024 now exists |
| ingest #81, visual-changelog #1, factory #1, design-experiments #1 | MERGE | Standard MIT; nothing else changed |
| 16 report PRs (all but Hillsborough and Lehman) | MERGE | Evidence matches; fixups pushed |
| uk-hillsborough-panel #13 (5aee6b4) | MERGE | Honest wording now: "not under an open licence that we can find", with both of the Panel's notices quoted. Whether to keep serving it is reportsthatmatter-9kn4 |
| us-lehman-examiner #12 (d4f3d83) | MERGE | Honest wording: "not established; we do not say it is public domain". Permission question: reportsthatmatter-hx7j |

## Fixups pushed

- **#347** 16d46c9c: removes the duplicate `docs/decisions/0024-licences.md` (#345's record is the one kept).
- **#347** 610010f1: the /about copy said "UK inquiry reports are Crown copyright", which is false for Hillsborough (© the Panel), and implied all the rest are US government works, which is false for Lehman. It now reads "most are works of the US government, in the public domain, or UK Crown copyright reused under the Open Government Licence. A few are neither, or we could not confirm their status, and we say so." `tests/about-licences.test.ts` still passes. The README now groups Lehman with the unconfirmed reports for the right reason (an examiner's court filing, not UK). It also lists `assets/cards/` and the screenshots and candidates under `docs/design/` as not MIT (the costar-direction screenshots are a third party's site), and notes that quotations in `editorial/` keep the report's status. `reports/uk-hillsborough-panel/PROCESSING.md` and `reports/us-lehman-examiner/PROCESSING.md` are now copied from their licence PRs. Without this, the site's /processing pages would still say "Crown copyright, reused under the OGL v3.0" and "no copyright restricts it" after the deploy; PROCESSING.md is prerendered, and publish does not carry it.
- **#347** e641857a: lesson `docs/design/lessons/2026-10-16-licence-read-the-publishers-terms-page.md`.
- **All 18 report PRs**: the `archive/` line in LICENSE.
- **uk-hillsborough-panel** 5aee6b4: section 1 rewritten around the website terms page (quoted, with the Wayback URL); README, PROCESSING.md and `reference/manifest.json` say the same.
- **uk-leveson-inquiry** 881e723: the OGL version history.
- **us-columbia-shuttle-accident** 86739ed: the NASA quote's scope.

## What #345's 0024 should add (from #347's record, now removed)

Paste into `docs/decisions/0024-licences.md` on `decisions-1016` (or a follow-up PR straight after it merges):

- **Beads:** add `reportsthatmatter-n5p8` (registry licences), `reportsthatmatter-9kn4` (Hillsborough) and `reportsthatmatter-hx7j` (Lehman).
- **Options:** (1) one licence for everything: wrong for reports we do not own; (2) MIT code + each report's text keeps its status at source + our additions CC BY 4.0 (chosen); (3) no licence until a lawyer has read every report: leaves the objection standing for no gain.
- **Decision**, append: "Code (site, ingest, factory, visual-changelog, design-experiments, and the build files in each report repo) is MIT, Copyright (c) 2026 Rufus Pollock and contributors, as a standard `LICENSE` so GitHub shows MIT. Each report repo has a `LICENSE` stating the report text's status with the report's own notice quoted, our markup, processing and editorial additions under CC BY 4.0, and the code under MIT (`LICENSE-MIT`). Where the report's own notice does not support a licence, the file says so instead of naming one: Hillsborough (the PDF imprint and the Panel's website terms reserve the rights in the report to the Panel) and Lehman (a court filing by a court-appointed examiner and his law firm, not federal employees). Columbia and 9/11 are treated as US government works with that caveat noted; Saville's OGL comes from GOV.UK, its imprint giving the HMSO reuse terms. The registry's `license` (JSON-LD) is set only where the repo records one, and is absent for Hillsborough and Lehman."
- **Consequences**, append: "Leveson's README no longer says 'Public domain' (Crown copyright, OGL, version not stated in its 2012 imprint). Hillsborough's OGL claim and Lehman's public-domain claim are withdrawn from READMEs, PROCESSING.md, data files and the registry until someone confirms them (9kn4, hx7j). /about has a Licences section. The private `marketing` repo has no licence."

## Integrator steps

1. Merge the 18 report PRs on each repo's default branch (all `main` except `us-columbia-shuttle-accident` and `uk-leveson-inquiry`, which use `master`). None changes `full.md`, so there is no re-ingest, pin bump or publish:
   ```bash
   for r in challenger-accident:13 us-columbia-shuttle-accident:10 jack-smith-report:13 uk-chilcot-inquiry:12 uk-grenfell-tower-inquiry:3 uk-hillsborough-panel:13 uk-leveson-inquiry:11 uk-litvinenko-inquiry:12 uk-post-office-horizon-inquiry:3 uk-saville-inquiry:10 us-911-commission:13 us-deepwater-horizon:11 us-jan6-committee:4 us-lehman-examiner:12 us-mueller-report:2 us-psi-financial-crisis:15 us-senate-cia-torture-report:3 us-v-philip-morris:11; do gh pr merge ${r#*:} --repo reportsthatmatter/${r%:*} --merge; done
   ```
   Then `git pull --ff-only` in each shared checkout under `~/src/reportsthatmatter/` (the columbia checkout is `columbia-accident/`).
2. Merge the code repos: `gh pr merge 81 --repo reportsthatmatter/ingest --merge`; the same for `visual-changelog` 1, `factory` 1 and `design-experiments` 1. ingest #81 adds only LICENSE: no release needed.
3. Site: add the 0024 text above to #345 (or queue it as the first follow-up), then `gh pr merge 345 --repo reportsthatmatter/reportsthatmatter --merge`, then `gh pr merge 347 --repo reportsthatmatter/reportsthatmatter --merge`. If #345 has been renumbered by then, change the link in #347's README line "The decision and its reasoning" to match.
4. Check `pnpm ingest aggregate uk-hillsborough-panel us-lehman-examiner --shared` from the site after step 1: `git status --short reports/` should be empty, because #347 already holds the copies. If it is not, the report branches moved after this review: commit what aggregate wrote.
5. Deploy (the /about section, the Hillsborough and Lehman /processing pages and the JSON-LD licences are all in the Worker or prerender): the normal `./scripts/deploy-cloudflare.sh` path, or with the next `pnpm ship`. No publish or reindex.
6. Check after the deploy: `/about#licences` shows the section. `/uk-hillsborough-panel` and `/us-lehman-examiner` have no `"license"` in their JSON-LD, and `/jack-smith-vol1` has the public-domain mark URL. `gh repo view reportsthatmatter/reportsthatmatter --json licenseInfo` shows MIT.
7. Beads: close reportsthatmatter-y2y8.9 when all are merged (this unblocks 9uog). Note n5p8 item 1 (licences for 5 reports) as done in #347. 9kn4 and hx7j stay open (needs-user).

## Beads

- Filed **reportsthatmatter-9kn4** (P2, needs-user, decision): Hillsborough, whether to keep serving it, and the permission email to the Home Office and TNA.
- Filed **reportsthatmatter-hx7j** (P3, needs-user, decision): Lehman, the question for Jenner & Block.
- Noted on y2y8.9: verdict and this file.

## Open questions for Rufus

1. **Hillsborough (9kn4).** Honest now: "not under an open licence that we can find; the Panel reserved its rights; served as a public-interest document with its source named, takedown route in the repo". This is a real gap, not a paperwork one. Ask the Home Office (the Panel's site was a Home Office site; the Panel is closed), copying TNA's Information Policy Team (psi@nationalarchives.gov.uk): "Is HC 581 Crown copyright, and may it be reproduced in full under OGL v3.0? If not, may Reports that Matter reproduce its text with attribution and a link?" Meanwhile, decide whether to keep it live.
2. **Lehman (hx7j).** Honest now: "status not established; we do not say it is public domain". Ask Jenner & Block's general counsel (or Valukas) whether they claim copyright and whether they permit reproduction with attribution. A US copyright lawyer could also say whether a §1104 examiner's filed report is treated like a privately written brief. Low risk.
3. **Copyright holder for MIT**: "Rufus Pollock and contributors" (all git history is yours). Say if it should be Datopian or Life Itself instead; it is the same one-line change in every LICENSE, LICENSE-MIT and README Licence line.
4. Columbia and 9/11 are treated as US government works without a statement in terms. Low risk; no one to ask unless you want certainty (NASA HQ legal; the 9/11 Commission's records are with NARA).

## Lessons

- `docs/design/lessons/2026-10-16-licence-read-the-publishers-terms-page.md` (on #347): a footer or a host's blanket OGL line is not a report's licence; the publisher's terms page (via Wayback) settled what had been recorded as a conflict.

## Getting better faster

- **What slowed me:** checking 18 notices by hand. The fix agent's evidence table was on the bead, not next to the PDFs, so every quote had to be re-extracted.
- **What would have caught it:** a `licence` block in each report's `sources.yaml` or manifest recording the quoted notice, the PDF page it is on and the PDF's SHA-256. A test would then check that the LICENSE quote appears verbatim in `pdftotext` of that page, the same way `pnpm editorial` checks quotations.
- **Proposal:** extend `pnpm editorial`'s verbatim check to licence quotes (`scripts/editorial.mjs`, or a small `pnpm licences check`), so a wrong or out-of-date licence claim fails CI like a misquote does.
