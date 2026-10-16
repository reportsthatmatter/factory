# Agent protocol note for the 2026-10-16 session

On top of `~/src/reportsthatmatter/reportsthatmatter/docs/agent-protocol.md` (the standing rules; read it first). Where they differ, this note wins for this session.

- **Logs go in the factory.** Write every log to `~/src/reportsthatmatter/factory/logs/2026-10-16/<kind>-<slug>-<what>.log` (e.g. `rtm-ay85-1016-verify.log`), never at `~/src/reportsthatmatter/` top level. Reviews go to `~/src/reportsthatmatter/factory/reviews/review-<scope>-2026-10-16.md`. Worktrees are unchanged: `~/src/reportsthatmatter/rtm-<slug>-1016` etc.
- **Worktrees named `*-1016`.**
- **Rufus is not answering questions during this run.** Decide and proceed (R11); log real questions as `needs-user` or `decision` beads.
- **The launch (planned 13 Oct) has not happened yet**; the poster is not live. Anything dated for "launch week" in beads or drafts is stale: re-date relative to a start date passed as a parameter, not hard-coded.
- **18 reports are live** (registry in `reports/registry.yaml`). Don't hard-code a count in copy.
