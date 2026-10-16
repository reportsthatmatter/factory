# Next session — reportsthatmatter (written 2026-09-26)

Start with `bd ready` in `reportsthatmatter/` and `bd show` the beads named here; the beads hold the specs, and this file only points at them.

## State at handoff

- Main is clean and current (`2f80f91`); ingest main is at `e70c248` (tag `v0.12.20`). No worktrees are left open; every branch from this session is merged.
- Seven reports have approved, live introductions: Jack Smith, Wall Street (PSI), Litvinenko, Saville, Hillsborough, Leveson and 9/11. Their highlights are seeded in D1.
- The site runs `@rtm/ingest` v0.12.20. Nine reports were re-ingested, published to R2 and reindexed for search. Columbia stays on its v0.11.0 pin on purpose, because a newer `columns` pass garbles the Mission Control transcript.
- Rufus's rule (in memory): ship introductions **approved**, without waiting for his review. He gives feedback later.

## Priorities, in order

1. **`reportsthatmatter-hxq`: staged shipping.** Write down the pipeline: stage 1 is the transcribed text, stage 2 the introduction, stage 3 imagery. Record it in the docs and the `report-introduction` skill, with one bead per stage per report. It is small, and it shapes everything below.
2. **`reportsthatmatter-cdp`: hero imagery epic.**
   - `.1` design and `.2` sourcing research can run in parallel. `.2` is research work that can go to a Sonnet subagent.
   - `.3` pilots the result on Hillsborough and Jack Smith.
   - `.4` records the decision, updates the guide and skill, and creates one image bead per report.
   - Rufus: a specific photograph beats a logo (Hillsborough, the Capitol riot). Fair use is fine; record the credit. Toned-down colour is allowed on the landing page only.
   - Build on the plate marks (`reportsthatmatter-xs3`, `docs/design/2026-09-12-imagery/`) and the louder retro-TV idea (`reportsthatmatter-bea`).
3. **Remaining introductions, `g0w.7.6`–`.9`:** Challenger, Columbia, Deepwater Horizon and Philip Morris. These are stage 2, so they can ship before the imagery work lands.

## Also open (P2–P3)

- `reportsthatmatter-h0l` is not yet adopted on the site. The `listedHeadings` pass is released (ingest ≥ v0.12.19), but `us-psi-financial-crisis/ingest.ts` does not enable it yet. Re-ingest PSI with it, read the headings diff, then publish.
- `reportsthatmatter-626`: about 17 PSI notes run over whole pages and still print in the body.
- `reportsthatmatter-vpx`: the 9/11 endnotes pages are misread as footnote blocks.
- `reportsthatmatter-w8g`: 9/11 chapter banners are fused with the first subsection and printed in all caps.
- `reportsthatmatter-v4w`: in a fresh worktree, `pnpm editorial` writes a stub `editorial.ts` unless `pnpm prerender` has run first.

## How to work

- Use sibling worktrees (`git worktree add ../rtm-<task> -b <branch> origin/main`, then `CI=true pnpm install`). Never switch branches in the shared `reportsthatmatter/` checkout.
- A changed report's text needs four steps to reach production:
  1. Bump the pin in the site and in the report repo.
  2. Run `pnpm ingest run <id>` and `pnpm ingest baseline <id>`, then commit and push the report repo.
  3. Run `verify.sh`, read the corpus moves, then `pnpm corpus accept`. Open the PR, merge, and run `./scripts/deploy-cloudflare.sh`.
  4. **Publish:** `RTM_PUBLISH_SECRET=$(cat ~/.rtm-publish-secret) pnpm publish-report <id> --base https://reportsthatmatter.org`. Report pages are served from R2, so a deploy alone does not update them.
- Tag ingest releases on the squash-merged commit, not the PR branch.
- **Use subagents for mechanical steps.** Give verify, PR, deploy, publish and seed chains, and bulk re-ingests, to Sonnet or Haiku. Keep fact-checking, reading diffs and design judgment on Opus.
- Keep running without stopping for input; batch any real questions at the end.
