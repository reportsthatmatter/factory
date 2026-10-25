# Review: newsletter signup and blog (PR #359), 2026-10-24

Reviewers: a general-purpose code reviewer and a Fable copy critic (subagents of newsletter-blog). Verdict: ready to merge after fixes, all applied in the third commit.

Code findings, all fixed: `pnpm cards --blog --highlights` would delete every quote card (now refused); /api/subscribe accepted cross-site posts and unbounded bodies (now same-site check, 4 KB cap, 403/413); removed addresses landed in the D1 usage ledger (now labelled); privacy line omitted the time (fixed); weak focus outline (fixed); honeypot named `website` (renamed `rtm_hp`); CSV formula injection (guarded); export path (only build/ or temp). Open: per-IP rate limit, bead reportsthatmatter-bvl7 (platform).

Copy findings, all applied to issue 1: Lehman context (Antoncic was CRO until 2007; email of 8 Apr 2008, fn 440), Leveson "system" gloss, PSI attribution (S&P, Dec 2006), Deepwater commission's full name, intro no longer overreaches, title now the report's own words.

Integrator steps: apply migration 0006 on the Datopian D1 before deploying (`pnpm wrangler d1 migrations apply reportsthatmatter-marks --remote --env "" --profile datopian`), deploy, then on production submit the form once with a test address, check `pnpm subscribers count --remote --yes`, and remove it with `pnpm subscribers remove <address> --remote --yes`. verify.sh (VERIFY_SHARED=1) passed: factory/logs/2026-10-24/newsletter-verify.log.

For launch-pack: the announce post goes in content/posts/<slug>.md (status: draft), then `pnpm blog` and `pnpm cards --blog`. Issue 1's first paragraph should link it once it has a slug.
