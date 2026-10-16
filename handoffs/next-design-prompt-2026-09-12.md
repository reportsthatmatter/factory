Continue reportsthatmatter-xs3 (imagery: a mark per report + logo fix,
issue #99). Run `bd show reportsthatmatter-xs3` first — its description and
comments are the full spec and current state; this prompt just points you
at it.

State: sourcing is done. All ten reports have a plate mark, built and
committed on branch `design/report-marks` (pushed to origin, last commit
0c91827). A worktree may already exist at ../rtm-marks (sibling to
reportsthatmatter) — check before creating a new one. The study, pipeline,
and provenance live in docs/design/2026-09-12-imagery/ in that branch;
`pnpm marks` rebuilds every mark from sources.yaml, `pnpm mark-mockups`
re-renders the archive/header/card comparison pages. Look at
mockups/archive-plate.png and header-plate.png once before doing anything
else, so you're working from the real result, not the description of it.

Direction picked (Rufus, from a rendered artifact review): "plate" — a
treated photograph left rectangular, sourced from the report's own exhibit
rather than a decorative image of the event. Logo: pilcrow-in-seal for
favicon/small sizes, bare wordmark in the navbar (drop the seal there).

Remaining work, roughly in order:
1. Wire marks into src/templates/index.ts (archive row), report.ts (header),
   card.ts (share card). The CSS is already proven out in
   scripts/imagery/mockups.mjs — port it, don't redesign it.
2. Logo: remove the seal from .wordmark in the navbar; swap the favicon/
   small-size asset for assets/brand/candidates/pilcrow-seal.svg — verify
   it's actually centered (measure it, the first pass was eyeballed).
3. Weigh two open quality caveats before shipping (both recorded in
   sources.yaml): hillsborough and psi read weakly at 92px archive-row size
   (thin line art — same failure the drawing direction hit for Challenger);
   leveson's source image is only 276x341, upscaled ~2.4x, softer than the
   other nine up close.
4. Confirm three photo credits before shipping — Deepwater (Transocean),
   the 911 stairwell diagram (credited to Marco Crupi in-report),
   Hillsborough's drawing (Eastwood & Partners). Rufus is unconcerned about
   rights generally for this public-interest project, but hasn't
   specifically blessed these three lines.
5. House rules: ./scripts/verify.sh, before/after screenshots to the
   visual-changelog repo, a docs/CHANGELOG.md entry, redeploy, confirm on
   production.

Consider splitting this bead into the 5 items above before running long
unsupervised stretches — it still has real taste calls in it (does the
wired-up result actually read right, is the pilcrow actually centered),
not pure mechanical wiring, so check in at each step rather than running
to the end and reporting back once.

YOU MUST KEEP RUNNING WITHOUT HUMAN INTERVENTION. Do **NOT** stop for input. just try to complte the task. Only stop if there is no other choice and you need human input.
