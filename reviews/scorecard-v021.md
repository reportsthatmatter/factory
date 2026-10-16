## Release scorecard: ingest v0.21.0, site c586d932, against origin/main

### Defect counts (`pnpm quality report --diff origin/main`)

A `▲` is a regression and needs a bead; an improvement is locked in with `pnpm quality ratchet`.

Counts now against `reports/quality-last.json` at `origin/main` (recorded 2026-10-03, ingest v0.20.0). `▲` is a regression of a gated signal.

| signal | jack-smith-vol1 | us-psi-financial-crisis | challenger-accident | litvinenko-inquiry | uk-leveson-inquiry | columbia-accident | us-911-commission | us-deepwater-horizon | us-v-philip-morris | uk-hillsborough-panel | uk-saville-inquiry | uk-chilcot-inquiry | us-lehman-examiner |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| severed-into-quote | 2 | 1 | 86 | 72 | 324 | 59 | 0 | 9 | 104 | 2 (−18) | 0 | 2 | 1 |
| severed-paragraph | 5 | 41 | 50 | 6 | 24 | 74 | 0 | 6 | 2 | 0 | 1 | 0 | 22 |
| severed-paragraph-capital (advisory) | 21 | 14 | 94 (+11) | 45 | 199 | 24 (+1) | 7 | 61 (+3) | 14 | 5 (+1) | 12 | 4 | 16 |
| bare-footnote-marker | 0 | 0 | 119 | 23 | 101 | 179 | 0 | 1,021 | 39 | 13 (−440) | 0 | 0 | 6 |
| bare-marker-after-quote | 1 (new) | 0 (new) | 2 (new) | 0 (new) | 2 (new) | 6 (new) | 0 (new) | 193 (new) | 5 (new) | 0 (new) | 0 (new) | 0 (new) | 0 (new) |
| note-marker-wrong-note | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| note-marker-unpaired | 9 | 0 | 3 | 0 | 1 | 13 | 0 | 416 | 0 | 0 | 0 | 0 | 0 |
| note-text-in-body | 8 | 6 | 68 | 64 | 13 | 29 | 0 | 269 | 48 | 0 | 0 | 0 | 5 |
| note-citation-vocabulary | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| furniture-paragraph | 0 | 5 | 372 | 16 | 198 | 75 | 0 | 25 | 44 | 0 | 0 | 0 | 5 |
| contents-entry-without-heading | 9 | 8 | 56 | 77 | 1,335 | 73 | 12 | 29 | 11 | 0 (−4) | 0 | 1 | 46 |
| heading-density | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 1 (+1 ▲) | 0 | 1 | 0 |
| heading-repeated | 0 | 1 | 2 | 0 | 3 | 2 | 0 | 0 | 15 | 2 (+2 ▲) | 0 | 1 | 0 |
| heading-function-word | 0 | 1 | 5 | 0 | 1 | 3 | 0 | 0 | 0 | 0 (−1) | 1 | 0 | 1 |
| heading-marker | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 6 | 0 | 0 | 0 | 1 |
| heading-long | 1 | 0 | 17 | 2 | 0 | 0 | 0 | 9 | 60 | 5 (+5 ▲) | 4 | 3 | 34 |
| printed-page-reversal | 2 | 0 | 14 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| rendered-h1 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| chapter-intro-misfiled | 0 | 0 | 2 | 0 | 1 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 |
| rendered-ol-words-share | 2% | 2% | 4% | 1% | 1% | 1% | 0 | 4% | 2% | 0 (−1) | 0 | 0 | 1% |
| numbered-prose-list | 1 | 1 | 2 | 0 | 25 | 2 | 0 | 1 | 3 | 0 (−7) | 0 | 0 | 1 |
| ids-per-1k-words | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| quote-share-parity | 1% | 2% | 2% (+1 ▲) | 2% | 1% (−1) | 3% (+2 ▲) | 1% | 2% | 1% | 1% (−1) | 1% | 2% | 1% |

**Regressions (5), each needs a bead:**
- uk-hillsborough-panel heading-density 0 → 1
- uk-hillsborough-panel heading-repeated 0 → 2
- uk-hillsborough-panel heading-long 0 → 5
- challenger-accident quote-share-parity 1 → 2
- columbia-accident quote-share-parity 1 → 3

Improvements (8): run `pnpm quality ratchet --record` after merging.

### Layout oracle: findings per report (`pnpm ingest verify`; lower is better, `▲` is a rise)

Deltas against reports/verify-last.json at origin/main.

| report | headings-missed | headings-spurious | markers-unlinked | markers-spurious | paragraphs-oversplit | paragraphs-merged | quotes-spurious | quotes-missed | note-off-page |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| jack-smith-vol1 | 22 | 37 | 36 (+3) ▲ | 89 (-6) | 149 | 422 (-3) | 51 | 53 | 71 |
| us-psi-financial-crisis | 37 | 5 | 26 | 7 | 132 | 858 | 226 | 91 | 0 |
| challenger-accident | 170 | 76 | 24 (+1) ▲ | 136 (-2) | 332 | 1973 | 331 | 212 | 140 |
| litvinenko-inquiry | 220 | 0 | 29 | 11 | 251 | 1246 | 184 | 46 | 0 |
| uk-leveson-inquiry | 1350 | 2 | 142 | 2 | 2089 | 2538 | 317 | 1255 | 1 |
| columbia-accident | 89 | 4 | 297 | 46 | 533 | 724 | 290 | 214 | 0 |
| us-911-commission | 27 | 3 | 6 | 9 | 140 (+45) ▲ | 2085 (-124) | 2 | 196 | 0 |
| us-deepwater-horizon | 330 | 0 | 1445 | 822 | 394 | 914 | 31 | 72 | 0 |
| us-v-philip-morris | 0 | 27 | 28 | 0 | 956 | 992 | 261 | 347 | 0 |
| uk-hillsborough-panel | 144 (-246) | 0 | 24 (-908) | 107 (+107) ▲ | 12 (-111) | 403 (-405) | 6 (-16) | 29 (-20) | 1 (+1) ▲ |
| uk-saville-inquiry | 153 | 3 | 0 | 31 (-25) | 16 | 1511 | 293 | 25 | 13 |
| uk-chilcot-inquiry | 13 | 3 | 0 | 0 | 15 | 393 | 5 | 79 | 0 |
| us-lehman-examiner | 0 | 5 | 22 | 5 | 140 | 221 | 143 | 99 | 0 |

### Golden pages (`pnpm ingest verify`; `▼` is a page that stopped matching in full)

| report | golden pages | match in full | known failures |
|---|---:|---:|---:|
| jack-smith-vol1 | 8 | 4 | 4 |
| us-psi-financial-crisis | 7 | 3 | 4 |
| challenger-accident | 7 | 0 | 7 |
| litvinenko-inquiry | 8 (+1) | 1 | 7 (+1) |
| uk-leveson-inquiry | 7 | 1 | 6 |
| columbia-accident | 6 | 0 | 6 |
| us-911-commission | 6 | 5 | 1 |
| us-deepwater-horizon | 6 | 0 | 6 |
| us-v-philip-morris | 5 | 1 | 4 |
| uk-hillsborough-panel | 5 | 5 (+5) | 0 (-5) |
| uk-saville-inquiry | 5 | 5 | 0 |
| uk-chilcot-inquiry | 5 | 4 | 1 |
| us-lehman-examiner | 5 | 4 | 1 |
| **all** | 80 | 33 | 47 |

The oracle against the golden pages, all reports:

| signal | tp | fp | fn | precision | recall |
|---|---:|---:|---:|---:|---:|
| headings-missed | 17 (-5) | 13 (+2) | 19 | 57% | 47% |
| headings-spurious | 1 | 3 | 2 | 25% | 33% |
| markers-unlinked | 22 (-8) | 0 | 11 | 100% | 67% |
| markers-spurious | 8 | 9 | 0 | 47% | 100% |
| paragraphs-oversplit | 41 | 11 | 67 (-4) | 79% | 38% |
| paragraphs-merged | 12 (-2) | 143 (+2) | 1 | 8% | 92% |
| quotes-spurious | 11 | 4 | 7 | 73% | 61% |
| quotes-missed | 2 | 21 | 0 | 9% | 100% |

### Headline scores against the reference editions (`pnpm score`; `docs/scores.json` against origin/main)

**Development set**

| report | boundary P | R | F1 | join, all rows | join, high-conf. | marker P | R | WER | reference error | ours wrong / judged (adjudicated) |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| uk-saville-inquiry (PDF shadow) | 81.3% | 99.2% | 89.4% | 94.2% | 97.0% | 99.3% | 97.7% | 0.0% | n/a | 3 / 30 |
| uk-saville-inquiry (edition adapter vs reference adapter: agreement, not a score) | 100.0% | 76.6% | 86.7% | 93.3% | 96.6% | 98.6% | 99.8% | 0.1% | 0.0% | 0 / 30 |
| us-911-commission (PDF shadow) | 93.5% | 94.1% | 93.8% | 93.1% | 97.0% | 99.3% | 98.3% | 0.4% | n/a | 0 / 29 |
| us-911-commission (edition adapter vs reference adapter: agreement, not a score) | 99.9% | 91.2% | 95.4% | 94.0% | 96.4% | 98.5% | 98.5% | 0.1% | 0.0% | 1 / 29 |
| us-v-philip-morris | 98.4% | 98.8% | 98.6% | 97.2% | 97.5% | 100.0% | 21.3% | 0.9% | 0.0% | 0 / 30 |

**Held-out set (a pass must not lower these)**

| report | boundary P | R | F1 | join, all rows | join, high-conf. | marker P | R | WER | reference error | ours wrong / judged (adjudicated) |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| columbia-accident | 89.0% | 88.3% | 88.6% | 94.4% | 97.8% | n/a | n/a | 0.4% | 3.4% | 6 / 29 |
| uk-chilcot-inquiry | 99.4% | 78.8% | 87.9% | 97.1% | 100.0% | 98.2% | 80.7% | 14.5% | 4.5% | 2 / 30 |
| uk-hillsborough-panel (PDF shadow) | 90.2% | 83.6% | 86.8% | 92.0% | 94.5% | 99.9% | 95.9% | 0.2% | n/a | 0 / 30 |
| uk-hillsborough-panel (edition adapter vs reference adapter: agreement, not a score) | 87.0% | 87.7% | 87.3% | 93.8% | 94.9% | 98.3% | 83.5% | 0.2% | 0.0% | 1 / 30 |
| us-duelfer-report | 81.0% | 67.7% | 73.8% | 91.5% | 95.4% | n/a | n/a | 3.6% | 3.3% | 5 / 30 |

No headline metric regressed.

'ours wrong / judged' is on the adjudicated page breaks (`reference/adjudicated.yaml`); join accuracy is the share of page breaks the reference covers where our split-or-join agrees with it, over all rows and over the high-confidence rows. A reference whose error plus no-answer share is over the ceiling is flagged by `pnpm score` and says little about that report.

### Checklist

- [ ] I read the quality diff, the oracle table and the headline diff, not only the totals; every regression has a bead or a reason here.
- [ ] No pass in this release was tuned on a held-out report (`reports/score-sets.yaml`): its examples came from the development set, and the held-out rows above did not fall. If one fell, say why it is not overfitting.
- [ ] The adjudicated 'ours wrong / judged' did not rise on any report (a rise is a regression even where F1 is flat).
- [ ] `docs/design/lessons.md` has a line for anything this release taught us.
- [ ] After shipping: `pnpm quality ratchet --record`, `pnpm scorecard --record --no-score`, `pnpm score`, and commit `reports/quality-last.json`, `reports/verify-last.json` and `docs/scores.json`.
