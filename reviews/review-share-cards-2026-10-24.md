# Review: PR #360, share cards (plate beside the quote, 1200x630) (2026-10-24)

Reviewer: Opus agent (pr-review skill). I did not touch production: no merge, deploy, publish, reindex, seed or remote D1; nothing posted. I did not touch `marketing/queue.yaml`, the poster or the author's worktree `rtm-share-cards-1024`.

**Verdict: MERGE (approve). No blocking issues in the PR; one cross-PR collision (decision 0033 with #359) fixed on the branch by renumbering to 0034.**

## Scope

- #360 `share-cards-1024`, bead reportsthatmatter-j10f. Head at start `fdcfc11b`; after fixups `075d17c0`. Base: main `5d4740ea`.

## Trial

- Worktree `~/src/reportsthatmatter/rtm-review360-1024`, branch `trial360-1024` = `origin/main` + `--no-ff` merge of `share-cards-1024`. Clean merge. `pnpm bootstrap` ok.
- `pnpm cards --check` (after `pnpm editorial`, which writes `build/editorial-highlights.json`): pass, "95 card(s) and the manifest match a fresh render". Log `factory/logs/2026-10-24/rtm-review360-1024-cards.log`.
- `pnpm typecheck`: pass.
- `pnpm test`: 1307 pass, 1 fail: `tests/posts.test.ts` "Challenger first". Not this PR: it reads only `marketing/queue.yaml` and `reports/registry.yaml`, which the PR does not touch; main is red since #355 front-loaded Mueller (first open item 2026-10-25 us-mueller-report). PR #362 (`posts-test-fix-1024`) fixes it; trial-merged on top, `tests/posts.test.ts` 45/45 pass. Log `rtm-review360-1024-test.log`.
- `VERIFY_SHARED=1 ./scripts/verify.sh` (on `fdcfc11b`): exit 1, the single failing check is vitest, the same `posts.test.ts` failure from main. Everything else green. Log `rtm-review360-1024-verify.log`. The fixups after it are a file rename and two text edits, re-checked with `pnpm test` and `pnpm cards --check`.
- Queue cards: all 27 `card:` paths in `marketing/queue.yaml` exist; largest 177,971 bytes (`uk-leveson-inquiry/q-766adfa6.png`), far under Bluesky's 1,000,000. All 96 PNGs in `assets/cards` are 1200x630, 21,895 to 181,838 bytes.
- Rendered cards read: Philip Morris longest queue quote (225 chars) and shortest (60), Leveson q-766adfa6 (215), Hillsborough q-a41f1724 (plateless: pilcrow and "P. 9"), CIA torture default, Post Office default (plateless), `site.png`. No clipping or overflow; `assertFits` in `scripts/cards.mjs` guards this on every render.
- PR touches neither `marketing/queue.yaml` nor the poster (`scripts/post-next.mjs`, `src/lib/poster.ts`, `workers/poster`). Card file names unchanged, so the poster picks the new images up from main.
- Trial with #359 (`newsletter-blog-1024`, `c8423790`) merged on top: see findings.

## Verdicts

| PR | verdict | reason |
| --- | --- | --- |
| #360 | MERGE | Checks pass on the trial merge (bar main's own red test, fixed by #362); cards reproducible; every queue card present and small; renders clean. |

## Findings, by severity

Fixed on the branch:

- **Decision number clash with #359.** #359 also adds `docs/decisions/0033-newsletter-signup-and-provider.md`; `tests/decisions.test.ts` fails whichever merges second. #360 had 3 references against #359's 16, so #360 moved to 0034 (`865b4625`, `075d17c0`; the second commit carries the title and lesson edits the rename missed). PR body updated; bead j10f noted. The two PRs now merge in either order on this point.

Not fixed (for the integrator, not blocking):

- **#359's blog cards go stale once #360 is on main.** #359 renders `assets/cards/blog/*.png` with the old `renderDefaultCard` at 2x; after #360 `pnpm cards --check` fails on all four and `tests/card-design.test.ts` "are 1200x630" fails. Recipe (rehearsed, passes): after merging the second of the two, `pnpm cards --blog` and commit the four PNGs and `src/generated/cards.ts`. Also a `docs/CHANGELOG.md` conflict (both add a 2026-10-24 entry at the top): keep both.
- Nits (no action needed to ship): the Post Office default card's footer repeats its title (no `common_name`); the CIA default card's byline wraps "9 / December 2014"; size figures differ between the `cards.mjs` comment (120-200 KB), the decision (65-200 KB), the PR body (41-200 KB) and the actual range (22-182 KB). `docs/design/2026-10-03-sharing-and-highlights.md` still says 2400x1260 (a dated design note; fine as history).

## Integrator steps

1. Merge #362 (main is red without it), then #360: `gh pr merge 360 --merge` (head `075d17c0`). No publish, reindex or seed needed; the deploy ships the cards and the og:image 1200x630 tags.
2. If #359 merges after #360: in its branch or on main right after, resolve the `docs/CHANGELOG.md` conflict keeping both entries, then `pnpm editorial && pnpm cards --blog && pnpm cards --check && pnpm vitest run tests/card-design.test.ts tests/cards.test.ts tests/decisions.test.ts`, commit `assets/cards/blog/*.png` and `src/generated/cards.ts`. If #359 merges first, do the same on #360's branch before merging it.
3. Deploy main (platform). After the deploy, the next poster run uploads the new card as the link-card thumb; check the first post's thumb on Bluesky.
4. Bead reportsthatmatter-j10f is a decision (proposed, revisit after two weeks of posts): leave it open; close only the implementation if a separate bead exists.

## Beads

- reportsthatmatter-j10f: notes appended (renumber, review path).
- No new beads: the posts test is already fixed by #362; the nits are cosmetic.

## Getting better faster

- Slowed by: two open PRs taking the same decision number on the same day; neither author's check could see the other's branch.
- Would have caught it: a check that lists decision files across open PR branches (`gh pr diff --name-only` for each open PR) in `pnpm decisions next`.
- Proposal: make `pnpm decisions next` also skip numbers claimed by open PRs.
