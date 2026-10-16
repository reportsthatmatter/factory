# Next session — reportsthatmatter (written 2026-09-27)

Start with `bd ready` in `reportsthatmatter/` and `bd show` the beads named here. The beads hold the specs; this file only points at them.

## State at handoff

- Main is clean and current at `efc3bf3`. Ingest is at tag `v0.12.21`, the 9/11 `endnotes()` and `numberedSections()` passes. No worktrees are open.
- **All 11 reports are through stages 1–3**: text, an approved introduction, and a hero photograph. The pipeline is in `docs/report-pipeline.md`. Hero images are covered by `docs/hero-images.md` and `docs/design/2026-09-27-hero/`.
- Also shipped this session:
  - The PSI `listedHeadings` fix (h0l).
  - 9/11: endnotes and section headings (vpx, w8g).
  - `pnpm editorial` now refuses to run without a pre-render (v4w).
  - `verify.sh` picks a free port and a per-run temp dir, so parallel agents no longer collide (9ew).

## For Rufus to look at (he said feedback later, so nothing blocks on this)

- **Litvinenko hero:** the hospital photograph (Weitsz/Getty, fair use) is the closest to the dignity line. It was kept because he asked for it to be published and his family released it. The fallback is the grave.
- **Saville hero:** soldiers at the barricade rather than Fr Daly's handkerchief photograph, which shows a dying boy.
- **Wall Street hero:** it is Lehman on 15 Sep 2008, but Lehman is not one of the report's case studies. The Goldman hearing would be more specific if a large copy turns up.
- **9/11 introduction:** the Moussaoui reading-guide entry now points at section 8.2, with a reworded "why" line.
- **Fair-use images** ship at 2400px, the same as free ones. That was decided in `docs/design/2026-09-27-hero/README.md`.

## Priorities

1. **Launch (`reportsthatmatter-y2t`)**: y2t.1 is P0 and needs Rufus (Bluesky account, Search Console). Then y2t.2 (rewrite the launch thread for 11 reports) and y2t.3 (`pnpm posts`).
2. **Report text beads filed while writing the intros.** They're worth fixing because intros had to route around them:
   - `eyc` Deepwater: even pages are read as block quotes with no ids.
   - `9ek` Philip Morris: about 4,000 findings of fact have no ids.
   - `4k6`: paragraph ids with ʼ are missing from the section index.
   - `tk8` Columbia structure; `a0z` Deepwater headings; `m2y`, `c1y` Challenger; `72f` Philip Morris headings; `5fn`, `60p`, `u88` 9/11 notes and contents.
   - Also `626` (PSI notes that run over pages) and `r19` (Hillsborough headings).
3. **New reports:** Chilcot, Valukas and Duelfer (`rcz`, `b7z`, `9br`), each with stage 2 and 3 children.

## How to work

- The conventions in `AGENTS.md` and the 2026-09-26 handoff still apply. Use sibling worktrees and `CI=true pnpm install`, and never switch branches in the shared checkout.
- **Subagents:** give one Opus agent per report or fix to judgment work (intros, image choice, pipeline fixes), each in its own worktree. Hand the release chains (verify, PR, deploy, publish, seed) to Sonnet. When several branches touch `src/generated/editorial.ts`, have them commit only their source files and let one integrator regenerate and ship.
- Keep running without stopping for input; batch any real questions at the end.
