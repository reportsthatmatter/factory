# Review: PR 425, roman page labels (reportsthatmatter-6j6q)

Reviewer: Claude agent (light form, time-boxed). I did not touch production: no merge, deploy, publish, seed, `verify.sh`, `ship`, or edit of the branch. Checks ran in the worktree `~/src/reportsthatmatter/rtm-stern-roman-1108` on the branch alone, not a trial merge onto main.

**Verdict: MERGE at b0ecad7c** (no must-fix; one note for the integrator).

## Scope

PR 425, branch `roman-pages-1108`, head b0ecad7c, based on 01f05b6f. origin/main has since moved to ba65ed69 (#419: `src/lib/anchors.ts`, `scripts/ingest/cli.ts`, cia-torture `full.md`, a test). No overlap with this PR's files, so the merge should be clean; I did not rehearse it.

## Checks

| Check | Result |
|---|---|
| Consumers of `pageOf` / `Citation.page` / `ResolvedPost.page` / `QueueItem.page` | All handle a string (below) |
| `pnpm prerender` then `pnpm posts --verify` | "marketing/queue.yaml is up to date"; `git diff origin/main...HEAD -- marketing` empty; no Stern item appears in the would-be-queued list |
| `pnpm typecheck` | pass |
| `pnpm exec vitest run` | 1514 pass; 2 failures in `tests/poster.test.ts` (timing-sensitive "Too early" gate test) under load; `tests/poster.test.ts` rerun alone: 51/51 pass |
| `pnpm corpus check` | 23 reports unchanged |
| `pnpm quality check` | all 23 within budget |

## Consumer audit

- `src/templates/editorial.ts` `pageLabel`: `cite.page ? "p. "+page : "¶"`. A non-empty string is truthy and the page is escaped on output. Good. (A numeric page 0 was already falsy before this PR.)
- `src/lib/posts.ts`: `formatPost` / `formatHooked` only interpolate into a template string; the null check on `pageOf` is unchanged; `ResolvedPost.page` is used only by `formatHooked`. No arithmetic or comparison.
- `QueueItem` has no page field (`src/lib/posts.ts` carries only text); the queue holds the formatted text.
- D1: `resolveEditorial` highlights pass through `numericPage`, so `marks.page` stays INTEGER or null for roman folios. `src/lib/marks.ts` (`typeof === "number"`, `row.page > have.page`) and `seed-highlights.ts` therefore never see a string.
- JSON-LD / `structured-data.ts`, `src/index.ts`, `workers/`: no use of editorial cite pages.
- `src/lib/feed.ts` has its own local `pageOf` (numeric, unchanged), so feed passages and `feed-admin` stay numeric. Not touched, not affected.
- `src/lib/quality/*` use their own numeric `page`, unrelated.
- The regex change is anchored on `data-page="(\d+|[ivxlc]+)"`, so `12#2` still gives null; covered by `tests/page-labels.test.ts`.

## Findings

- Must-fix: none.
- Note (low): `[ivxlc]+` accepts any such letters, not only valid numerals. Harmless: it only reads what the renderer emitted.
- Note (low): Stern roman highlights are unmarked in D1 (page null) by design (comment cites 6j6q); a later change to the marks schema would be needed to carry them.
- Removing `card: true` from 6 Stern highlights (and their q-*.png) is what keeps the queue unchanged: with a page now resolving, cards would have made them queue candidates. Confirmed by `posts --verify`. If a Stern post is wanted later, it is a deliberate queue addition.

## What moves

Landing-page Stern excerpts and cites now read "p. iv" instead of "¶"; `src/generated/editorial.ts` regenerated accordingly; `cards.ts` and `picks.ts` lose the 6 Stern cards. No other report's text or ids move (`corpus check` and `quality check` clean).

## Integrator steps

1. Merge PR 425 (no ship-gated files beyond generated ones). If it conflicts with main after #419, take main's and run `pnpm editorial`.
2. After merge: `pnpm prerender && pnpm posts --verify` should still say up to date.
3. Close 6j6q on ship.

## Lessons

The poster end-to-end tests are time-gated and flake under a parallel full run; rerun alone before calling a failure real.
