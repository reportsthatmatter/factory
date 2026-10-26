# Agent protocol note, 2026-10-24

On top of `reportsthatmatter/docs/agent-protocol.md` and `agent-protocol-2026-10-16.md`. Plan context: `factory/handoffs/2026-10-24-coordination-plan.md`.

- **Ship unless irreversible** (Rufus, 2026-10-24). Make the reversible choice, record it, ship it. Stop and ask only before something that cannot be rolled back (deleting data with no export, spending money, posting under a new public identity for the first time).
- **Reports back are short.** Lead with what shipped (links), then only the decisions Rufus must make, each with your recommendation. No narrative. Keep the "Getting better faster" retro to three lines.
- **Fable for critique.** For design, copy and plans, have a Fable reviewer subagent critique before you ship.
- Worktrees are named `*-1024`; logs in `factory/logs/2026-10-24/`; reviews `factory/reviews/review-<scope>-2026-10-24.md`.
- 18 reports are live. Bluesky posting goes live from Sun 2026-10-25, before the public launch. Target public launch: Tue 2026-10-27.
- Atomatic decommission is approved (Rufus, 2026-10-24): export D1 first.
- Coordinator session is `rtm`. If you are blocked on something only Rufus can do, say so in your final report; don't wait.
- Ownership this run: `poster-live` owns `marketing/queue.yaml` and the poster's queue tooling; `worker-cron` owns any new posting Worker; `platform` is the site integrator and deploys main; `share-cards` owns OG/card images; `launch-pack` owns marketing drafts and the brand doc; `newsletter-blog` owns the blog and signup; `next-reports` owns new report repos. Touch someone else's area only through a PR they can see.
