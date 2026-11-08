# Review: Challenger quality pass (2026-11-08)

TL;DR: both PRs are sound. Merge both; no must-fix items. Four small follow-ups below.

- Report repo, challenger-accident#14: **MERGE**
- Site, reportsthatmatter#420: **MERGE** (it carries the same full.md, which is byte-identical to the report branch)

## Report PR (challenger-accident#14)

**Note repairs (spot check).** I checked 13 entries against the page images (PDF pp. 83, 114, 120, 150, 183, 189, 207, 218, 222, 236, 248, 259): 41a, 51, 69 (OCR "59"), 129 "Hearing", 208, 220 "While this discussion…", 40, 18 "Ibid, Chart SRB-28", 80, 108 "OIS", 109, 145 "Discussion", and 35/36 on p.222. Every restored number and first word matches what the page prints. Two entries blank a line, and in both the blanked line is the whole line, so no tail is lost.

**Joins c-0075..c-0097.** All 23 are one sentence on the page: lowercase or mid-clause continuations, plus hyphen splits that the pipeline rejoins ("External", "indicative"). None joins two real blocks. I checked c-0082 and c-0083 against p.244.

**ingest.ts passes.**
- `repairNoteNumbers`: each repair must match exactly one line or the run fails. Low risk.
- `dropDeskInstructionHeads`: limited to PDF pp. 335–376 and a page's first five lines, with tight regexes. Low risk.
- `dropStrokeNoise`: runs on PDF p.263 onward. I listed every lone-letter line there (450 pages): all are figure or table garble ("I" ×260; scattered c/n/W/Y/N…). The lone "A"/"B" lines on pp. 289, 290, 414 and 422 are figure debris, not list labels. No real prose is lost.
- **full.md diff:** I took 4-word shingles of every removed line and looked for them in the new text. Of the 36 lines not found, all are notes moved into note definitions (renumbered or reformatted), stroke noise or DI running heads. No body prose was removed.

**Follow-ups (not blocking):**
1. c-0082/c-0083 (printed p.244): the joins are right, but the text still reads `Up "Well, to and including` and `I "Yes, think that`, and footnote markers 120 and 121 survive only as "~ ~ ~". The page reads `Mr. DAVIS.120 "Well, I can tell you how it runs now. Up to and including…` and `Mr. MURPHY.121 "Yes, I agree… I think that…`. Widen the two replaces to fix this.
2. A severed paragraph was missed, of the same class as the joins: "…being experienced on the Space Shuttle Solid" / "Rocket Motor clevis joints…" (printed p.204, full.md ~l.4373).
3. PDF p.222 (printed 214): notes 38–40 are still glued onto the end of note 37 ("JmLarry N&…", "30 y r", "‘O b i"). This is not a regression; the page got repairs for 35 and 36 only.
4. Nits: the p.206 repair has no `seen`. The why lines cite "153 more notes" and "141 more notes", but the actual change in note-definition-unreferenced is +144.

## Site PR (reportsthatmatter#420)

- **Budgets:** every raised value has a `# why` with a plausible cause:
  - bare-footnote-marker 76→79, bare-marker-after-quote 18→22, note-marker-unpaired 66→72 and note-definition-unreferenced 165→309: more notes are now defined.
  - rendered-ol-words-share 5→6 and quote-share-parity 1→2: 26 pages moved off the vision reading.
  - oracle note-off-page 78→81: per-chapter note numbering.
  - Everything else falls. One minor point: the why on oracle paragraphs-oversplit (now 307) still describes the old 372.
- **Aliases:** `pnpm aliases check` passes locally (challenger: 3781 ids, 376 aliases, 819 unmatched). Some old footnote-paragraph ids now point to the body paragraph nearby (for example `166cmte-hgs-transcript-july-24` → `addition-number-technical-managers-also`). That is acceptable.
- **e2e:** limiting the count to `/^p-\d+$/` is correct. A word slug can start with "p-" (`p-all-right-gene`), and the fixture is still jack-smith-vol1. The removed comment about Challenger's positional ids is right to go, because p-2 and p-3 are gone.
- **Lesson file:** one line, follows conventions.

Not done: I did not re-run the report ingest locally to confirm full.md reproduces, and neither PR has CI checks.
