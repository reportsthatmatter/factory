## Release scorecard: ingest v0.22.0, site a88513f0, against d72327fb

### Defect counts (`pnpm quality report --diff d72327fb`)

A `▲` is a regression and needs a bead; an improvement is locked in with `pnpm quality ratchet`.

Counts now against `reports/quality-last.json` at `d72327fb` (recorded 2026-10-03, ingest v0.21.0). `▲` is a regression of a gated signal.

| signal | jack-smith-vol1 | us-psi-financial-crisis | challenger-accident | litvinenko-inquiry | uk-leveson-inquiry | columbia-accident | us-911-commission | us-deepwater-horizon | us-v-philip-morris | uk-hillsborough-panel | uk-saville-inquiry | uk-chilcot-inquiry | us-lehman-examiner | uk-post-office-horizon-inquiry | us-jan6-committee |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| severed-into-quote | 2 | 1 | 85 (−1) | 72 | 323 (−1) | 54 (−5) | 0 | 6 (−3) | 104 | 2 | 0 | 2 | 1 | 5 (new) | 0 (new) |
| severed-paragraph | 6 (+1 ▲) | 41 | 43 (−7) | 6 | 25 (+1 ▲) | 70 (−4) | 0 | 6 | 2 | 0 | 1 | 0 | 22 | 0 (new) | 0 (new) |
| severed-paragraph-capital (advisory) | 23 (+2) | 13 (−1) | 90 (−4) | 41 (−4) | 168 (−31) | 20 (−4) | 7 | 48 (−13) | 10 (−4) | 4 (−1) | 12 | 3 (−1) | 12 (−4) | 3 (new) | 7 (new) |
| bare-footnote-marker | 0 | 0 | 76 (−43) | 0 (−23) | 39 (−62) | 2 (−177) | 0 | 1 (−1,020) | 39 | 0 (−13) | 0 | 0 | 4 (−2) | 0 (new) | 0 (new) |
| bare-marker-after-quote | 1 | 0 | 18 (+16 ▲) | 0 | 3 (+1 ▲) | 0 (−6) | 0 | 0 (−193) | 5 | 0 | 0 | 0 | 0 | 0 (new) | 0 (new) |
| note-marker-wrong-note | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 (new) | 0 (new) |
| note-marker-unpaired | 0 (−9) | 0 | 66 (+63 ▲) | 0 | 1 | 0 (−13) | 0 | 0 (−416) | 0 | 0 | 0 | 0 | 0 | 0 (new) | 0 (new) |
| note-reference-sequence | 14 (new) | 0 (new) | 71 (new) | 0 (new) | 38 (new) | 7 (new) | 0 (new) | 3 (new) | 0 (new) | 0 (new) | 0 (new) | 0 (new) | 0 (new) | 0 (new) | 0 (new) |
| note-text-in-body | 8 | 6 | 48 (−20) | 56 (−8) | 1 (−12) | 7 (−22) | 0 | 8 (−261) | 48 | 0 | 0 | 0 | 5 | 0 (new) | 0 (new) |
| note-citation-vocabulary | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 (new) | 0 (new) |
| furniture-paragraph | 0 | 5 | 150 (−222) | 16 | 199 (+1 ▲) | 61 (−14) | 0 | 25 | 44 | 0 | 0 | 0 | 5 | 0 (new) | 5 (new) |
| contents-entry-without-heading | 9 | 8 | 56 | 77 | 1,335 | 71 (−2) | 12 | 29 | 11 | 0 | 0 | 1 | 46 | 0 (new) | 0 (new) |
| heading-density | 0 | 0 | 1 (+1 ▲) | 0 | 0 | 1 | 0 | 0 | 0 | 1 | 0 | 1 | 0 | 0 (new) | 0 (new) |
| heading-repeated | 0 | 1 | 12 (+10 ▲) | 0 | 3 | 2 | 0 | 0 | 15 | 3 (+1 ▲) | 0 | 1 | 0 | 1 (new) | 2 (new) |
| heading-function-word | 0 | 1 | 5 | 0 | 1 | 2 (−1) | 0 | 0 | 0 | 0 | 1 | 0 | 1 | 0 (new) | 1 (new) |
| heading-marker | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 6 | 0 | 0 | 0 | 1 | 0 (new) | 0 (new) |
| heading-long | 1 | 0 | 17 | 2 | 0 | 0 | 0 | 9 | 60 | 7 (+2 ▲) | 4 | 3 | 34 | 0 (new) | 42 (new) |
| printed-page-reversal | 2 | 0 | 0 (−14) | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 (new) | 0 (new) |
| rendered-h1 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 (new) | 0 (new) |
| chapter-intro-misfiled | 0 | 0 | 1 (−1) | 0 | 1 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 (new) | 0 (new) |
| rendered-ol-words-share | 2% | 2% | 5% (+1 ▲) | 1% | 1% | 1% | 0 | 1% (−3) | 2% | 0 | 0 | 0 | 1% | 1% (new) | 0 (new) |
| numbered-prose-list | 1 | 0 (−1) | 2 | 0 | 26 (+1 ▲) | 2 | 0 | 0 (−1) | 3 | 0 | 0 | 0 | 1 | 0 (new) | 0 (new) |
| ids-per-1k-words | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 (new) | 0 (new) |
| quote-share-parity | 2% (+1 ▲) | 2% | 1% (−1) | 2% | 1% | 4% (+1 ▲) | 1% | 1% (−1) | 1% | 1% | 1% | 2% | 1% | 3% (new) | 1% (new) |

**Regressions (14), each needs a bead:**
- jack-smith-vol1 severed-paragraph 5 → 6
- uk-leveson-inquiry severed-paragraph 24 → 25
- challenger-accident bare-marker-after-quote 2 → 18
- uk-leveson-inquiry bare-marker-after-quote 2 → 3
- challenger-accident note-marker-unpaired 3 → 66
- uk-leveson-inquiry furniture-paragraph 198 → 199
- challenger-accident heading-density 0 → 1
- challenger-accident heading-repeated 2 → 12
- uk-hillsborough-panel heading-repeated 2 → 3
- uk-hillsborough-panel heading-long 5 → 7
- challenger-accident rendered-ol-words-share 4 → 5
- uk-leveson-inquiry numbered-prose-list 25 → 26
- jack-smith-vol1 quote-share-parity 1 → 2
- columbia-accident quote-share-parity 3 → 4

Improvements (44): run `pnpm quality ratchet --record` after merging.

### Layout oracle: findings per report (`pnpm ingest verify`; lower is better, `▲` is a rise)

Deltas against reports/verify-last.json at d72327fb.

| report | headings-missed | headings-spurious | markers-unlinked | markers-spurious | paragraphs-oversplit | paragraphs-merged | quotes-spurious | quotes-missed | note-off-page |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| jack-smith-vol1 | 22 | 37 | 30 (-6) | 38 (-51) | 147 (-2) | 421 (-1) | 51 | 53 | 11 (-60) |
| us-psi-financial-crisis | 37 | 5 | 2 (-24) | 7 | 131 (-1) | 848 (-10) | 226 | 91 | 0 |
| challenger-accident | 169 (-1) | 101 (+25) ▲ | 32 (+8) ▲ | 147 (+11) ▲ | 372 (+40) ▲ | 1924 (-49) | 385 (+54) ▲ | 208 (-4) | 78 (-62) |
| litvinenko-inquiry | 220 | 0 | 0 (-29) | 11 | 250 (-1) | 1246 | 184 | 46 | 0 |
| uk-leveson-inquiry | 1350 | 2 | 47 (-95) | 3 (+1) ▲ | 2082 (-7) | 2537 (-1) | 318 (+1) ▲ | 1256 (+1) ▲ | 1 |
| columbia-accident | 89 | 2 (-2) | 5 (-292) | 1 (-45) | 277 (-256) | 815 (+91) ▲ | 235 (-55) | 218 (+4) ▲ | 0 |
| us-911-commission | 27 | 3 | 6 | 6 (-3) | 140 | 2085 | 2 | 196 | 0 |
| us-deepwater-horizon | 330 | 0 | 2 (-1443) | 29 (-793) | 80 (-314) | 903 (-11) | 30 (-1) | 63 (-9) | 0 |
| us-v-philip-morris | 0 | 27 | 28 | 0 | 952 (-4) | 992 | 261 | 347 | 0 |
| uk-hillsborough-panel | 70 (-74) | 0 | 1 (-23) | 7 (-100) | 12 | 337 (-66) | 6 | 29 | 1 |
| uk-saville-inquiry | 153 | 3 | 0 | 24 (-7) | 16 | 1511 | 293 | 25 | 13 |
| uk-chilcot-inquiry | 13 | 3 | 0 | 0 | 15 | 395 (+2) ▲ | 5 | 79 | 0 |
| us-lehman-examiner | 0 | 5 | 19 (-3) | 2 (-3) | 137 (-3) | 221 | 143 | 99 | 0 |
| uk-post-office-horizon-inquiry | 19 | 0 | 0 | 1 | 13 | 236 | 17 | 4 | 0 |
| us-jan6-committee | 62 | 15 | 16 | 17 | 133 | 2599 | 17 | 779 | 0 |

### Golden pages (`pnpm ingest verify`; `▼` is a page that stopped matching in full)

| report | golden pages | match in full | known failures |
|---|---:|---:|---:|
| jack-smith-vol1 | 8 | 5 (+1) | 3 (-1) |
| us-psi-financial-crisis | 7 | 3 | 4 |
| challenger-accident | 7 | 2 (+2) | 5 (-2) |
| litvinenko-inquiry | 8 | 2 (+1) | 6 (-1) |
| uk-leveson-inquiry | 7 | 1 | 6 |
| columbia-accident | 6 | 2 (+2) | 4 (-2) |
| us-911-commission | 6 | 5 | 1 |
| us-deepwater-horizon | 6 | 3 (+3) | 3 (-3) |
| us-v-philip-morris | 5 | 1 | 4 |
| uk-hillsborough-panel | 7 (+2) | 7 (+2) | 0 |
| uk-saville-inquiry | 5 | 5 | 0 |
| uk-chilcot-inquiry | 5 | 4 | 1 |
| us-lehman-examiner | 5 | 4 | 1 |
| uk-post-office-horizon-inquiry | 5 | 4 | 1 |
| us-jan6-committee | 5 | 5 | 0 |
| **all** | 92 | 53 | 39 |

The oracle against the golden pages, all reports:

| signal | tp | fp | fn | precision | recall |
|---|---:|---:|---:|---:|---:|
| headings-missed | 16 (-1) | 16 (+3) | 10 (-9) | 50% | 62% |
| headings-spurious | 0 (-1) | 3 | 1 (-1) | 0% | 0% |
| markers-unlinked | 4 (-18) | 1 (+1) | 3 (-8) | 80% | 57% |
| markers-spurious | 0 (-8) | 14 (+5) | 1 (+1) | 0% | 0% |
| paragraphs-oversplit | 15 (-26) | 14 (+3) | 30 (-37) | 52% | 33% |
| paragraphs-merged | 8 (-4) | 175 (+32) | 1 | 4% | 89% |
| quotes-spurious | 5 (-6) | 12 (+8) | 12 (+5) | 29% | 29% |
| quotes-missed | 3 (+1) | 23 (+2) | 0 | 12% | 100% |

### Headline scores against the reference editions (`pnpm score`; `docs/scores.json` against d72327fb)

**Development set**

| report | boundary P | R | F1 | join, all rows | join, high-conf. | marker P | R | WER | reference error | ours wrong / judged (adjudicated) |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| uk-saville-inquiry (PDF shadow) | 81.4% (+0.1 pts) | 99.2% | 89.4% (+0.0 pts) | 94.7% (+0.4 pts) | 97.0% | 99.3% | 97.7% | 0.0% | n/a | 2 / 30 (was 3 / 30) |
| uk-saville-inquiry (edition adapter vs reference adapter: agreement, not a score) | 100.0% | 76.6% | 86.7% | 93.3% | 96.6% | 98.6% | 99.8% | 0.1% | 0.0% | 0 / 30 |
| us-911-commission (PDF shadow) | 93.5% (-0.0 pts) | 94.1% (-0.0 pts) | 93.8% (-0.0 pts) | 93.2% (+0.1 pts) | 97.1% (+0.1 pts) | 99.2% (-0.1 pts) ▼ | 98.7% (+0.5 pts) | 0.4% (-0.0 pts) | n/a | 0 / 29 |
| us-911-commission (edition adapter vs reference adapter: agreement, not a score) | 99.9% (-0.0 pts) | 91.1% (-0.1 pts) | 95.3% (-0.1 pts) | 94.0% | 96.4% | 98.6% (+0.1 pts) | 98.5% (+0.0 pts) | 0.1% (+0.0 pts) | 0.0% | 1 / 29 |
| us-v-philip-morris | 98.4% (+0.0 pts) | 98.8% | 98.6% (+0.0 pts) | 97.4% (+0.3 pts) | 97.6% (+0.1 pts) | 100.0% | 21.3% | 0.9% | 0.0% | 0 / 30 |

**Held-out set (a pass must not lower these)**

| report | boundary P | R | F1 | join, all rows | join, high-conf. | marker P | R | WER | reference error | ours wrong / judged (adjudicated) |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| columbia-accident | 89.4% (+0.4 pts) | 88.3% (-0.0 pts) | 88.8% (+0.2 pts) | 95.4% (+1.0 pts) | 97.8% (+0.0 pts) | n/a | n/a | 0.7% (+0.2 pts) ▼ | 3.4% | 5 / 29 (was 6 / 29) |
| uk-chilcot-inquiry | 99.4% | 78.8% | 87.9% | 97.1% | 100.0% | 98.2% | 80.7% | 14.5% | 4.5% | 2 / 30 |
| uk-hillsborough-panel (PDF shadow) | 91.1% (+0.9 pts) | 91.1% (+7.5 pts) | 91.1% (+4.4 pts) | 92.0% (+0.0 pts) | 94.6% (+0.0 pts) | 99.9% (+0.0 pts) | 93.6% (-2.3 pts) ▼ | 0.2% (+0.0 pts) | n/a | 0 / 30 |
| uk-hillsborough-panel (edition adapter vs reference adapter: agreement, not a score) | 87.3% (+0.3 pts) | 89.8% (+2.1 pts) | 88.5% (+1.2 pts) | 93.8% | 94.9% | 98.0% (-0.3 pts) | 84.7% (+1.2 pts) | 0.2% (-0.0 pts) | 0.0% | 1 / 30 |
| uk-post-office-horizon-inquiry | 99.1% | 91.1% | 94.9% | 96.8% | 100.0% | 99.6% | 66.3% | 17.2% | 3.3% | 2 / 30 |
| us-duelfer-report | 81.0% | 67.7% | 73.8% | 91.5% | 95.4% | n/a | n/a | 3.6% | 3.3% | 5 / 30 |

Regressions (each needs a bead or an explanation): columbia-accident wer (held-out); uk-hillsborough-panel marker_r (held-out); us-911-commission marker_p.

'ours wrong / judged' is on the adjudicated page breaks (`reference/adjudicated.yaml`); join accuracy is the share of page breaks the reference covers where our split-or-join agrees with it, over all rows and over the high-confidence rows. A reference whose error plus no-answer share is over the ceiling is flagged by `pnpm score` and says little about that report.

### Checklist

- [ ] I read the quality diff, the oracle table and the headline diff, not only the totals; every regression has a bead or a reason here.
- [ ] No pass in this release was tuned on a held-out report (`reports/score-sets.yaml`): its examples came from the development set, and the held-out rows above did not fall. If one fell, say why it is not overfitting.
- [ ] The adjudicated 'ours wrong / judged' did not rise on any report (a rise is a regression even where F1 is flat).
- [ ] `docs/design/lessons.md` has a line for anything this release taught us.
- [ ] After shipping: `pnpm quality ratchet --record`, `pnpm scorecard --record --no-score`, `pnpm score`, and commit `reports/quality-last.json`, `reports/verify-last.json` and `docs/scores.json`.
