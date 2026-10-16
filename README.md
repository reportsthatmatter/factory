# Reports that Matter — factory

The coordination layer for Reports that Matter: how the agents that build the site and its reports are run, and the record of what each session did. The app (site, beads, house rules, skills, decisions, lessons) stays in `../reportsthatmatter/`; the ingest library in `../ingest/`; each report in its own sibling repo. This repo holds what used to sit loose in `~/src/reportsthatmatter/`.

## Layout

- `handoffs/` — `next-session-prompt-<date>.md`: the supervisor's handoff to the next session. The newest one is where a new supervisor starts (after `AGENTS.md` and `bd ready` in the site repo).
- `protocols/` — `agent-protocol-<date>.md`: a session's dated note on top of the standing rules in `../reportsthatmatter/docs/agent-protocol.md`. Where they differ, the dated note wins for that session.
- `reviews/` — `review-<scope>-<date>.md`: reviewer agents' review files (PRs and releases), plus scorecards.
- `backlog/` — triage and backlog snapshots.
- `logs/<YYYY-MM-DD>/` — command output from agents (verify, ship, publish, ingest runs). Gitignored: kept locally for debugging, never committed.
- `scratch/` — throwaway working directories that are not git worktrees. Gitignored.

Git worktrees (`rtm-<slug>-<MMDD>`, `ingest-<slug>-<MMDD>`, `<site>-reports/`) stay at `~/src/reportsthatmatter/` beside the repos they belong to: `git worktree` records their paths, and the protocol names them there.

## Where agents write

- A log: `~/src/reportsthatmatter/factory/logs/<YYYY-MM-DD>/<kind>-<slug>-<what>.log`, e.g. `logs/2026-10-16/rtm-ay85-1016-verify.log`. Never at `~/src/reportsthatmatter/` top level.
- A review: `~/src/reportsthatmatter/factory/reviews/review-<scope>-<YYYY-MM-DD>.md`.
- A handoff: `~/src/reportsthatmatter/factory/handoffs/next-session-prompt-<YYYY-MM-DD>.md`.

## Status

Public at https://github.com/reportsthatmatter/factory since 2026-10-16 (decision 0021 in the site repo). Logs and scratch stay local (gitignored): they can hold command output not fit to publish.
