# Review: batch 4 (site #332 privacy copy, site #331 highlights-feed design), 2026-10-16

Reviewer: Claude Sonnet 5.5 agent. I did not touch production: no merge, deploy, publish, seed or D1 write. I ran no `pnpm ship`, no `verify.sh` (no report text or routes changed beyond one static page; the new route test covers it) and no ratchet (no report text).

**Verdict: both MERGE. #332 as is; #331 after one fixup I pushed (a lesson file failed `tests/lessons.test.ts`).**

## Scope

| PR | Branch | Head at start | Head after fixups |
| --- | --- | --- | --- |
| #332 (bght.10.9) | `bght-10-9-privacy-copy` | 809e4347 | 809e4347 |
| #331 (bght.10 design, decision 0023) | `design/public-highlights-feed` | 86d89829 | 7ba11399 |

## Trial

- Worktree `~/src/reportsthatmatter/rtm-reviewbatch4-1016`, branch `trialbatch4-1016`, from origin/main c6b1e196. Merged #332, then #331, then #331's fixup. No conflicts (the PRs touch disjoint files).
- `pnpm bootstrap` pass (`factory/logs/2026-10-16/rtm-reviewbatch4-1016-bootstrap.log`). `pnpm typecheck` pass (`...-typecheck.log`).
- `pnpm test` first run: **1 failure**, `tests/lessons.test.ts` on `docs/design/lessons/2026-10-16-cite-live-code-paths-as-cpu-evidence.md` (`...-test.log`). After the fixup: 73 files pass, 1139 tests pass, 1 skipped (`...-test2.log`).
- `pnpm decisions check`: numbering ok; `pnpm decisions next` now gives 0024.
- Skipped as not applicable (docs and one static template): quality, corpus, editorial, aliases, ratchet.

## Verdicts

| PR | Verdict | Reason |
| --- | --- | --- |
| #332 | MERGE | New copy is accurate against `share.js` and `/api/mark`; "no cookie" verified; renders cleanly at 375 px and 1280 px; test added and passing. |
| #331 | MERGE (at 7ba11399) | Docs only, already design-validated. Decision 0023 does not clash with main (0021 factory, 0022 email capture are on origin/main; 0023 is free). Markdown is not hard-wrapped. Needed a one-character lesson-format fix. |

## #332: what I checked

- **What is sent** (`assets/share.js` `report()`, line 178): on Save, Copy link, Copy quote and a completed Share, a keepalive POST to `/api/mark` with report, section, paragraph, exact words, prefix, suffix, page and kind. "its words are also sent to us" is true; it also sends the 0 to N characters of surrounding context, which "its words" does not cover but is not misleading.
- **What is stored** (`src/index.ts` `/api/mark`, `src/lib/marks.ts`): the event plus `actor`, a hash of a secret salt, the UTC date, the IP (`cf-connecting-ip`) and the user agent, so it changes daily and cannot be linked across days. The IP and UA themselves are not stored. "nothing that names you" is true; strictly the actor is a pseudonymous identifier, not "nothing". Acceptable for the copy; the design doc's opt-out (bght.10.4) is the place to say it more exactly.
- **No cookie, verified three ways:** no `Set-Cookie` or `document.cookie` anywhere in `src/` or `assets/*.js`; live `HEAD /highlights` and a `POST /api/mark` return no `set-cookie`; in headless Chromium on the trial, loading `/highlights` leaves 0 cookies (the page uses `localStorage` only, which is not a cookie). Cloudflare's `report-to`/NEL header is present but is not a cookie.
- **Other places making the claim:** `grep -rn "never sent to us\|none of it is sent" src assets docs` leaves only the 21 August CHANGELOG entry (docs/CHANGELOG.md:642), which is correctly left as published with a dated correction above it.
- **Rendering:** screenshots at 375 px and 1280 px (`/private/tmp/claude-501/-Users-rgrp-src-reportsthatmatter/d3fb3769-be08-4aee-a4de-cbd3b194341d/scratchpad/hl-m.png`, `hl-d.png`): no horizontal overflow (scrollWidth equals viewport), byline reads "Kept in this browser", the explanation sits under the export buttons within the measure. It is small uppercase mono, so it is less readable than body text; not a blocker.
- **Test:** `tests/routes.test.ts` "highlights page privacy copy" passes.

## Findings

- Fixed on #331 (7ba11399): lesson `2026-10-16-cite-live-code-paths-as-cpu-evidence.md` had `(2026-10-16, bght.10, design validator).` with a full stop after the attribution, which the lesson checker rejects. Removed the full stop. This was **found only by the trial test run**.
- Not fixed, low: the privacy line is only on `/highlights`. The popover on report pages (where the POST happens) discloses nothing at the point of saving. bght.10.4 (opt-out and honest copy) in the design covers this; no new bead.
- Not fixed, low: consider moving the explanation above the buttons or setting it in body type for readability. Cosmetic.

## Integrator steps

1. `cd ~/src/reportsthatmatter/reportsthatmatter && git fetch`.
2. Merge #332 (`gh pr merge 332 --merge`), then #331 (`gh pr merge 331 --merge`; head must be 7ba11399). Order does not matter; no conflicts.
3. On main: `pnpm test` (lessons and decisions tests included).
4. Deploy #332 is a template change: it needs a normal `pnpm ship` (Worker deploy), not a content publish. #331 needs no deploy (docs only).
5. After deploy: `VERIFY_BASE=https://reportsthatmatter.org ./scripts/verify.sh`, and `curl -s https://reportsthatmatter.org/highlights | grep -c "never sent to us"` must print 0.
6. Close bght.10.9 on ship. Leave bght.10 and its children open (the feature is not built). Decision 0023 is already marked decided in the PR.
7. Worktree clean-up when done: `git worktree remove ../rtm-reviewbatch4-1016 ../rtm-reviewbatch4fix-1016`.

## Beads

None filed. Verdict lines to append to bght.10.9 and bght.10 (`bd update <id> --append-notes`), not done from this agent, see below.

## Lessons

- A docs-only PR can still fail `pnpm test`: lesson files have a strict one-line format. Run `pnpm vitest run tests/lessons.test.ts` before pushing a lesson.

## Getting better faster (R13)

- The lesson format check could be a pre-commit or `pnpm lessons check` step named in the design-agent skill, so the author sees the failure before review.
