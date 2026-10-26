# Review: site PR #404, launch polish (feed link in every head, scheduled launch post)

Reviewer: Claude (Sonnet 5.5) agent. I did not touch production: no merge, release, deploy, publish, seed or `pnpm ship`. I did not run `verify.sh`, `quality`, `corpus`, `editorial`, `aliases` or the ship rehearsal: the PR touches no report, ingest or pin, only `src/`, one post, its generated bundle and card, and two test files.

**Verdict:** merge (the fixup below is already on the branch; merge at the new head `fb78b8c5`).

## Scope

| PR | Branch | Head at start | Head after fixup |
| --- | --- | --- | --- |
| site #404 | `launchpolish-1024` | `bb4df85e` | `fb78b8c5` |

## Trial

Worktree `rtm-review404-1024` (branch `trial404-1024` from `origin/main`, then `fix404-1024`). Merged `origin/launchpolish-1024` with `--no-ff`: no conflicts. `pnpm bootstrap`, then:

- `pnpm typecheck`: pass.
- `pnpm test`: 91 files, 1456 passed, 1 skipped.
- Same suite with the clock faked to 2026-10-28T12:00Z (a temporary vitest config faking `Date`, removed afterwards): 91 files, 1456 passed, 1 skipped.
- Probe through `app.request`: at 2026-10-28 the post is in /blog, the feed and the sitemap and /blog/<slug> returns 200; at 2026-10-26T23:59:59Z it is in none of them and /blog/<slug> returns 404.
- `pnpm blog` and `pnpm cards --blog` on the PR head: no diff, so the bundle and card are current. Re-run after the fixup: bundle updated, card unchanged, committed.

## Checks against the five points

1. **Feed link.** `renderHead` now defaults `feed` to `/blog/feed.xml`; `options.feed ?? default`, so pages that pass their own (blog index, post, newsletter) render exactly one `<link rel="alternate" type="application/atom+xml">`. Probed: home and /blog each have one. `tests/footer-head.test.ts` covers both cases. Good.
2. **Date gating.** `isPublished(post, now)` is `status === "published" && post.date <= now.toISOString().slice(0, 10)`: UTC, string compare on `YYYY-MM-DD`, correct. Every consumer goes through it: /blog, feed, sitemap, changelog link set, /blog/<slug> (404), /newsletter (`publishedIssues`). The `.filter((post) => isPublished(post))` rewrites in `src/index.ts` are needed so `filter` does not pass the index as `now`. Two notes, neither blocking: (a) `?draft` previews a scheduled post without the "Draft" badge (the badge keys on `status === "draft"`); (b) the feed and sitemap send `max-age=3600`, so the post can appear up to an hour late on 27 Oct, and /blog is not cached by us. Deploying before 27 Oct needs no second deploy.
3. **Post.** Verified against `reports/registry.yaml`, `editorial/*.yaml` and the rendered site:
   - Twenty reports: registry has 20 ids, prerender builds 20, the post lists 20 names with correct years.
   - The first quote is verbatim from `reports/challenger-accident/full.md`, `data-page="3"`; the `?p=committee-found-nasa-s-drive` id is in `published-ids.txt` and the URL redirects (302) to the paragraph on /full. Good.
   - Links resolve in the repo: `/about#contact` (`id="contact"`), `/changelog`, `/blog`, `/newsletter`, GitHub org, Bluesky profile (the handle resolves via the Bluesky API; the site itself links the DID, so the handle link is fine but could use `BLUESKY_PROFILE_URL`). No `/subscribe` (the form posts to `/api/subscribe`; the post does not link it).
   - "No accounts, no tracking cookies", the removal "within a day" and the AI sentence match /about and `docs/brand-voice.md`.
   - **Found and fixed:** the "not whole yet" list missed two partial editions. Lehman is Volumes 1 and 3 only (introduction, executive summary, procedural background, two sections; registry title and editorial say so). The Senate torture report is the declassified executive summary and findings; the full 6,700-page study is classified. Added both to the sentence (commit `fb78b8c5`), bundle regenerated.
   - Voice: plain, exact, no banned words (no launch, finally, excited), no exclamation marks, "I" register matches the blog row. The post says "twenty" and "page 3": the page is the site's `data-page`, which brand-voice would call "PDF p. 3" if it is the file's page; I left it as the author wrote it. Low risk.
4. **Bundle and card.** Regenerated and identical to the PR's (see Trial).
5. **Follow on Bluesky footer.** I agree with leaving it unchanged. `tests/follow.test.ts` asserts it is absent from `/` and `/reports/*/full`, decision 0022 chose Bluesky follow plus the Atom feed as the capture and kept it off reading pages, and the launch post itself carries the Bluesky link in its body. Putting the footer on /blog/<slug> would be a decision change, not polish.

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| #404 | MERGE (at `fb78b8c5`) | All five points hold; one factual omission in the post fixed on the branch. |

## Fixups pushed

- `fb78b8c5`: launch post names Lehman (Volumes 1 and 3) and the Senate torture report (executive summary) among the partial editions; `src/generated/posts-bundle.ts` regenerated.

## What moves

No report, ingest, pin or D1 change. The post goes live on its own on 2026-10-27 (UTC), with the card at `assets/cards/blog/public-reports-link-to-the-sentence.png`.

## Integrator steps

1. `gh pr view 404 --json headRefOid` must be `fb78b8c5...`; merge: `gh pr merge 404 --merge` (or the house method).
2. In a fresh main worktree: `pnpm bootstrap && pnpm typecheck && pnpm test`.
3. Deploy on or before 27 Oct via the normal `pnpm ship` flow; no deploy is needed on the 27th itself. After 00:00 UTC on 27 Oct (allow an hour for the feed and sitemap cache), check `https://reportsthatmatter.org/blog`, `/blog/feed.xml`, `/sitemap.xml` and `/blog/public-reports-link-to-the-sentence`; before then the slug must 404.
4. Do not post the launch link anywhere before 27 Oct 00:00 UTC.
5. Close the PR's bead on ship.

## Beads

None filed. Optional low-priority follow-ups: show a "Scheduled" badge in `?draft` preview; use `BLUESKY_PROFILE_URL` (DID) for the post's Bluesky link.

## Lessons

- A "what's here" list in public copy should be generated from, or checked against, the registry titles and editorial scope notes: both partial editions missed here were stated in the registry and editorial files.
- Faking the clock for a whole suite needs only `vi.useFakeTimers({ toFake: ["Date"] })` in a `setupFiles` entry and a throwaway config; a probe test through `app.request` shows the before/after behaviour directly.

## Getting better faster

Add to `tests/blog.test.ts` (or a new test) a check that every registry report whose title or editorial scope says "Volume", "Executive Summary" or "Volumes" is named in the launch post's partial list; it would have caught Lehman.
