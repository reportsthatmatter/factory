# Agent review of site PRs #279 and #278 (2026-10-04)

Reviewer: Opus agent. I did not touch production D1 and did not deploy. Scope:

- **#279:** sharing and highlights, epic `bght`. It adds the Editor's highlight label, 72 quote cards, `og:url`/size/alt tags, `pnpm highlight add` and diff-only `seed-highlights`.
- **#278:** lessons from the marketing research, `y2y8`.

Neither repo has CI. I trial-merged both onto `origin/main` at `d72327fb` (the v0.22.0 re-ingest) in the worktree `~/src/reportsthatmatter/rtm-review279-1004` (branch `review-279-1004`). I then ran `pnpm bootstrap` and the checks below.

**v0.22.0 is still not live.** At 22:34 UTC, `reportsthatmatter/build/ship/state.json` stops at `publish: awaiting-yes`. The v0.22.0 integrator is waiting for the 00:00 UTC D1 reset. #279 was opened on the pre-re-ingest main, so it had to be checked against the v0.22.0 text.

## Verdicts

| PR | Verdict | Reason or exact fix |
|---|---|---|
| site #279 sharing and highlights | **MERGE** (the reviewer has pushed the fixes) | Main had moved under it. The fixes are pushed to `sharing-highlights` (head `6c2147c4`) and described in a PR comment:<br>(1) `388d67d8` merges `origin/main`. The `marketing/queue.yaml` conflict is resolved by regenerating the queue with `pnpm posts`.<br>(2) `9ae03309`: v0.22.0 moved one card highlight's id, Deepwater `frequency-unannounced-inspections-plummeted-108` → `-although`. Its card is keyed on (paragraph id, words), so the key moved from `q-56d39b37` to `q-09509400`; the image is byte-identical. The card, `src/generated/cards.ts` and the queue link now follow the new id. Before this fix, `tests/share-links.test.ts` fails on that queue item.<br>(3) `bd494d9a`: 17 code comments and test names cited "decision 0013", which is the mini-reference split. They now cite 0014.<br>(4) `6c2147c4`: two lessons.<br>The PR now merges cleanly onto `origin/main`. |
| site #278 lessons: marketing research | **MERGE** | Docs only (`lessons.md`, +7). It merges cleanly onto main. After #279 it conflicts in `docs/design/lessons.md`: keep both sides, with a blank line before `## Marketing research (y2y8, 2026-10-03)`. |

## What I checked (trial = main + updated #279 + #278)

- **Every highlight and card quote is still verbatim against the v0.22.0 text, and every id resolves.**
  - `pnpm editorial`: all 13 files approved, 163 highlights, all resolved and verbatim.
  - `pnpm posts --check`: "every not-yet-posted item still resolves".
  - `pnpm cards`: exit 0. It now fails any `quote:` not in its paragraph, and all 91 cards passed.
  - Every `card: true` highlight's computed `quoteCardId` exists on disk and in the manifest; my script for this is `scratchpad/cardcheck.ts`.
  - **Only the Deepwater card had moved.** The other 71 quote cards and every curated or default card re-render byte-identical, so the PNGs are deterministic. The generated queue equals the PR's except for the Deepwater item.
- **bght.9 is fixed.**
  - Both Jack Smith `share-quotes.yaml` quotes are verbatim in `reports/jack-smith-vol1/full.md`: "When an advisor at the White House learned this, he rushed to the dining room and informed Mr. Trump, who replied "So what?"" and "Crucially, not only was Mr. Trump's voter-fraud narrative objectively false-he knew that it was false."
  - "false-he" is how the PDF's text layer reads (`pdftotext` gives a hyphen). It is not the PR's doing.
  - The stale `running-mate-example-trump-s` card is deleted.
  - Locally, `?p=just-before-2-24-p&h=|So what?|` and `&h=|dining room|` both preview with `default.png`. Only `?p=` with no `h` takes the paragraph card, which is verbatim.
- **Checks, all on the trial:**
  - `pnpm typecheck` clean.
  - `pnpm test`: 754/754.
  - `pnpm corpus check` and `pnpm quality check` pass.
  - `VERIFY_SHARED=1 ./scripts/verify.sh` passes. It is local-only and includes the e2e social-proof round trip against a local D1. Its one warning is the existing `pipeline.yaml` drift.
  - `pnpm marks check` is skipped because it reads production. #279 moves no paragraph ids: `assets/generated/` is byte-identical to main's prerender (`diff -rq` is empty).
- **The rendered pages, served by local `wrangler dev` with a local D1 seeded by the new script:**
  - Queued `?h=` links for Jack Smith `just-before-2-24-p`, Deepwater `…-although` and Jack Smith `core-trump-s-obstructive-scheme` each give the right head:
    - `og:image` and `twitter:image` show their own `q-….png`.
    - `og:image:width` is 2400 and `og:image:height` is 1260.
    - `og:image:alt` and `twitter:image:alt` carry the quote.
    - `og:site_name` is present.
    - `og:url` keeps `?p=&h=`.
    - `rel=canonical` is the bare section URL.
  - A section page shows `mark.social-proof.editor` with the hover title **"Editor’s highlight"** and a key under the header: **"Shaded: the editor’s highlights and passages readers marked"**. The shading is the one-reader wash. `/reports/jack-smith-vol1/marks` rows carry `"readers":0,"editor":true`.
  - The Most marked block ("Editor’s highlight · N readers") is not shown anywhere today, because every report has an editorial landing page. It is covered by tests.
- **seed-highlights on the local D1:**
  - Empty table: 163 inserts. A re-run: 0.
  - Simulating production after v0.22.0's ship: running main's old delete-and-reinsert seed and then the new `--dry-run` plans **0 writes**.
- **Not blocking:**
  - The key under the header always says "and passages readers marked", even when every shaded passage is the editor's. That is fine under decision 0014's option A.
  - The Jack Smith `just-before-2-24-p` card says p. 29 and the queue text says p. 30. This was already so on main.
  - The new `just-before` quote is a different sentence from the old note. The note was reworded accordingly.

## D1 cost of shipping #279

#279 changes no prerender input, and `assets/generated/` is byte-identical to main's. So there is **no publish, no reindex and no search write**. The deploy itself costs no D1 row reads or writes. Only `seed-highlights` touches D1.

| step | row writes | row reads |
|---|---:|---:|
| deploy (`./scripts/deploy-cloudflare.sh`) | 0 | its closing drift table (`publish-report --all --status`): about 13 small reads |
| `pnpm seed-highlights --dry-run --remote` | 0 | one full scan of `marks` (see below) |
| `pnpm seed-highlights --remote`, if v0.22.0's ship has already seeded | **0** (plans 0, writes nothing) | one more scan of `marks` |
| the same, if production still holds the v0.21.0-era rows | 12 to 44 planned (6 to 22 highlights replaced: Deepwater 10, Challenger 2 to 10, Hillsborough 0 to 24), about 36 to 132 billed | as above |
| worst case (every row replaced) | 326 planned, about 980 billed | as above |
| post-ship checks (curl `/marks` and a few pages) | 0 | about 15 per `/marks` call (the `report` index) |

- **Why "billed" is about 3× the plan:** D1 counts one extra row written per index a write touches, and `marks` has two indexes (`marks_report_para`, `marks_report_actor`). The planner's "one per delete or insert" undercounts (bead `q2ph`).
- **Reads:** the seed's `SELECT … WHERE actor = 'editorial:rufus-pollock'` cannot use `marks_report_actor`, because `report` is that index's leading column. So each run reads every row of `marks`: 163 editor rows plus every reader's mark, likely under 1,000.
- **Page views are unchanged:** `markCounts` still reads by `report`, so it costs the same per page view.

**Budget: under 1,000 row writes and about 2,000 row reads in the worst case; 0 writes expected.** That is negligible against 100k writes and 5M reads a day, even on the day v0.22.0 ships.

## Merge order and integrator steps

1. **Let v0.22.0 finish first,** through `pnpm ship --yes` and `verify-prod`.
   - Do not merge #279 while that ship run is paused at `awaiting-yes`. Its later steps (deploy, seed) run from the shared checkout, so they would pick up #279 halfway through a release.
   - Folding #279 in would save at most about 300 writes, which is not worth the risk.
2. **Merge site #279** (`sharing-highlights`, head `6c2147c4` or later), using a merge commit. It merges cleanly onto main at `d72327fb`. If main has moved again with a re-ingest, first run `pnpm prerender && pnpm editorial && pnpm cards --highlights && pnpm posts`, and commit any card or queue changes.
3. **Merge site #278** (`marketing-lessons-2026-10-03`). Resolve `docs/design/lessons.md` by keeping both sections.
4. **Pull and re-check** in the shared checkout:
   - `git pull --ff-only`, then `CI=true pnpm install`, because #279 adds the devDependency `upng-js`.
   - Then `pnpm prerender && pnpm editorial`.
   - Confirm `git status` is clean, so `src/generated/editorial.ts` and `cards.ts` did not change.
   - Optionally run `pnpm typecheck && pnpm test` (754).
5. **Deploy:** `./scripts/deploy-cloudflare.sh`. Its drift table should say all 13 reports are current, the v0.22.0 hashes. **If it lists any report as drifted, stop:** v0.22.0 did not finish, and #279 does not publish anything.
6. **Seed:**
   - `pnpm seed-highlights --dry-run --remote`. Expect "163 stored, 163 wanted, 0 writes".
   - Run `pnpm seed-highlights --remote` only if it plans writes. Read the `-`/`+` lines first: only re-ingest moves (Deepwater, Challenger, Hillsborough) should appear.
7. **Mark decision 0014 accepted.** Rufus accepted option A ("Editor's highlight") on 2026-10-03, per `next-session-prompt-2026-10-04.md`. In a small docs PR or commit:
   - In `docs/decisions/0014-editors-highlights.md`, set `**Status:** accepted`, `**Date decided:** 2026-10-03`, `**Decided by:** Rufus Pollock`.
   - Replace the "Not yet made…" paragraph under `## Decision` with "A: Editor's highlight, unnamed. Shipped in #279 (2026-10-04). Idea for later: a dedicated editor account, separate from Rufus's personal one."
   - In `docs/decisions/README.md`, change the 0014 row's `proposed` to `accepted`.
   - `bght.6` is already closed, so no bead change is needed for this step.
8. **Close the beads on a green post-ship check:** `bght.9`, `bght.7`, `bght.1`, `f2e`, `wb0`, and any other `bght.*` that #279 names as done.
9. **Run `bd dolt push`.**

## Post-ship checks (production, read-only, a handful of D1 reads)

1. **The label is live:**
   - `curl -s https://reportsthatmatter.org/reports/us-911-commission/marks | head -c 300` shows `"readers":0,"editor":true`.
   - In a browser, open `https://reportsthatmatter.org/reports/jack-smith-vol1?p=just-before-2-24-p`. Hovering the shaded "When an advisor…" should say **"Editor’s highlight"**, never "Highlighted by 1 reader".
   - The header should carry the key "Shaded: the editor’s highlights and passages readers marked".
2. **A social preview of a `?h=` link,** using the first queue item: `https://reportsthatmatter.org/reports/jack-smith-vol1?p=just-before-2-24-p&h=location%20at%20the%20Capitol.%20|When%20an%20advisor%20at%20the%20White%20House%20learned%20this%2C%20he%20rushed%20to%20the%20dining%20room%20and%20informed%20Mr.%20Trump%2C%20who%20replied%20%22So%20what%3F%22|&src=bsky`.
   - **Head tags:** `curl -sL -A 'facebookexternalhit/1.1' '<link>' | grep -E 'og:|twitter:|canonical'` should show:
     - `og:image` ending in `/assets/cards/jack-smith-vol1/q-75447469.png`
     - width 2400 and height 1260
     - `og:image:alt` holding the quote
     - `og:url` with `?p=…&h=…`
     - canonical with no query
   - **The image itself:** `curl -sI https://reportsthatmatter.org/assets/cards/jack-smith-vol1/q-75447469.png` returns 200 `image/png`, about 150 KB.
   - **Bluesky's preview:** `https://cardyb.bsky.app/v1/extract?url=<urlencoded link>` returns that image.
   - **Optional:** paste the link into https://www.opengraph.xyz or the LinkedIn Post Inspector.
   - **The bght.9 regression check:** `…/jack-smith-vol1?p=just-before-2-24-p&h=|So%20what%3F|` must preview with `jack-smith-vol1/default.png`, not a paragraph card.
3. **The moved card:** `…/us-deepwater-horizon?p=frequency-unannounced-inspections-plummeted-although&h=…` (the queue's link) names `q-09509400.png`.

## Found only by the trial merge

1. **The queue conflicts with v0.22.0, and one card went stale.** The Deepwater highlight id moved, so its card key and queue link moved with it. This is fixed on the branch.
2. **Decision-number clash.** The PR's code cited "decision 0013" for the editor's highlights, but 0013 is the mini-reference split. This is fixed on the branch.
3. **Orphan cards are never pruned** (bead `u09x`). `cards.mjs` builds its manifest from every PNG on disk, so after any re-ingest the old `q-*` cards stay in `src/generated/cards.ts`. I deleted the one orphan by hand. `pnpm ship` does not regenerate cards or the queue after a re-ingest. Only `share-links.test.ts` notices, and only for queued items.
4. **D1 estimates ignore index rows** (bead `q2ph`). See the D1 section above. The same question applies to `pnpm ship`'s `d1-estimate`.

## Getting better faster (reviewer retro)

- **What slowed me down:**
  - Working out whether production holds the v0.21.0 or the v0.22.0 highlight rows. I could not ask D1.
  - Working out whether #279 changes published content. It does not, but that took a `diff -rq` of the prerender.
- **What would have caught it earlier:**
  - A test that every `card: true` highlight's `quoteCardId` is in `CARDS` and that no `q-*` in `CARDS` is unwanted (`u09x`).
  - `pnpm ship` running `cards --highlights` and `posts` after re-ingest.
- **One proposal:** an offline `pnpm seed-highlights --plan-against <editorial-highlights.json>` that diffs two builds' highlight sets, so the D1 cost of a seed can be budgeted without reading production.
