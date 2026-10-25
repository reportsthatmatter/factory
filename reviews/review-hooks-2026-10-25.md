# Review: hooks for the 8 needs-hook items (#395) and the poster's skip of unhooked items (#396), 2026-10-25

Reviewer: Fable agent (reviewer, hooks batch). I did not touch production: no merge, deploy, release, publish, seed, D1 query, Bluesky call or `wrangler deploy`. Fixups pushed to #395 only (one commit, two hooks). Nothing pushed to #396.

**Verdict: MERGE both, #395 after the fixup already on its branch (`f84cbdc8`), then #396; redeploy the poster Worker after #396 (not urgent).**

## Scope

| PR | bead | branch | head at start | head after review |
|---|---|---|---|---|
| #395 Hooks for the 8 needs-hook queue items | irro | `hooks-1024` | `b4610492` | `f84cbdc8` (one fixup commit) |
| #396 Poster skips an unhooked needs-hook item instead of halting | irro | `hookskip-1024` | `50e1e543` | `50e1e543` (unchanged) |

Base: origin/main `9853540f` (#392). Both branches are one commit on top of it.

## Trial

Worktree `~/src/reportsthatmatter/rtm-review395-1024`, branch `trial395-396-1024` from `9853540f`, `pnpm bootstrap`, then `git merge --no-ff` of `hooks-1024` and `hookskip-1024` in that order. Both merged clean (disjoint files: #395 touches only `marketing/queue.yaml`; #396 touches none of it). After the fixup, `origin/hooks-1024` (`f84cbdc8`) merged into the trial again, clean.

| check (on the trial merge of both) | result |
|---|---|
| `pnpm typecheck` | pass |
| `pnpm test` | pass, 1450 passed, 1 skipped (90 files) |
| `pnpm posts --check` | pass (exit 0): "Queue check passed"; 0 items waiting for a hook; the one skipped candidate (`us-psi-financial-crisis/case-study-examines-detail-four`) is the same on main |
| `pnpm posts --verify` | pass, "marketing/queue.yaml is up to date" (before and after the fixup) |
| plain `pnpm posts` | exit 1 on main, on #395 alone and on the trial, for the same reason: the PSI candidate's `?h=` link does not read back. Pre-existing (noted in the #392 review); not this PR's |
| `pnpm quality check`, `pnpm corpus check`, `pnpm editorial`, `pnpm aliases check` | pass (20 reports within budget; 20 unchanged; 20 editorial files checked) |
| `pnpm quality ratchet --dry-run` | nothing to lower |
| `VERIFY_SHARED=1 ./scripts/verify.sh` | pass, "All checks passed." One warning, peer noise (R6): `reports/pipeline.yaml drifts from the artefacts` (neither PR touches reports). Log: `factory/logs/2026-10-25/rtm-review395-1024-verify.log` |
| After the fixup merge (`2cd39884`): `pnpm posts --check`, `pnpm posts --verify`, `vitest run tests/posts.test.ts tests/poster.test.ts tests/skills.test.ts` | pass; pass; 163 passed |
| `pnpm ship --plan --state build/review-ship-state.json` | plan prints; nothing run |
| Poster dry run (#396), crafted queue: the 29 Nov item unhooked + one hooked item re-dated 30 Nov, `POSTER_QUEUE=… POSTER_TODAY=2026-11-29 pnpm post-next` | trial: "Nothing to post: nothing due until 2026-11-30 (skipped, waiting for a hook: uk-leveson-inquiry:4-21-…)"; on 11-30 "would post" the hooked item. Same queue on main's code: `POSTER FAILED: … text is 357 graphemes, over Bluesky's 300` |
| `git status --short` after all checks | clean |

## Verdicts

| PR | verdict | reason |
|---|---|---|
| #395 | MERGE (at `f84cbdc8`) | Queue diff touches exactly the 8 items and only `hook`, `text`, `needs_hook` (parsed main vs branch: 43 items, 35 byte-identical). Every claim in the 8 hooks checked against `full-body.html` and the editorial files (table below); no hook repeats its card's words or key phrase; all 8 texts are 204 to 295 graphemes. Two hooks rewritten by the reviewer for the skill's "no two posts in ten days close the same way" rule and the card-repeat rule (fixups below). |
| #396 | MERGE, then redeploy the Worker | `selectNext` is the one chooser for both posters (`runPoster` calls it before and after `overlayAccount`; the Worker and `post-next.mjs` both call `runPoster`), and `validateItem` runs only on the chosen item, so a skipped item is never validated or published. `--check` no longer fails on a due unhooked item, so the workflow no longer goes record-only for it. Docs updated. Dry run above proves the behaviour. |

## Fact-check of the eight hooks

Checked by the reviewer against `assets/generated/reports/<id>/full-body.html` (paragraph ids given) and `editorial/<id>.yaml`, independently of the PR body's table.

| Date | Hook (final) | Checked |
|---|---|---|
| 29 Nov, Leveson 4.21 (p. 564) | A landlord was arrested on 30 December 2010 over the murder of his tenant in Bristol, held for three days and released on bail; another man was later convicted. Eight of the articles printed while he was held were put before the Leveson inquiry into the press. | ¶4.1 (`4-1-joanna-yeates-tenant`, p. 558): tenant of Jefferies, flats in "the Clifton area of Bristol". ¶4.2: arrested "at about 7am on 30 December 2010" by Avon and Somerset Constabulary, custody "lasted for three days, whereupon Mr Jefferies was released on police bail"; Tabak "convicted of Ms Yeates' murder on 28 October 2011". ¶4.5: "the 'eight worst offending articles' which were published in six separate newspapers over a three day period"; section head p. 559 "Press reporting while Christopher Jefferies was in custody". Card (the inquiry's finding on that reporting) answers the hook. No victim named. |
| 30 Nov, Lehman p. 7 | Lehman Brothers filed for bankruptcy on 15 September 2008, the largest filing ever. Its court-appointed examiner found it had moved about $50 billion off its balance sheet with a device called Repo 105, and asked its financial controller why. | p. 2 (`january-29-2008-lehman-brothers`): "On September 15, 2008, LBHI sought Chapter 11 protection, in the largest bankruptcy proceeding ever filed". p. 6 (`lehman-did-not-disclose-however`): "an accounting device (known within Lehman as 'Repo 105') … temporarily removing approximately $50 billion of assets from the balance sheet at the end of the first and second quarters of 2008". Card's fn 26: Examiner's Interview of Martin Kelly (the Global Financial Controller). "Court-appointed": `editorial/us-lehman-examiner.yaml` (court ordered the appointment; the U.S. Trustee appointed Valukas). |
| 1 Dec, Post Office 1.4 (p. 6) | Post Offices ran Horizon from 1999. Some Fujitsu staff knew before roll-out it could show false losses. | ¶1.1: roll-out "in about September 1999"; ¶1.2: Legacy Horizon supplied by ICL Pathway, "Fujitsu" per fn 2; ¶1.3: "Prior to roll out, some employees of Fujitsu had discovered that Legacy Horizon was capable of producing data which was false … losses or gains … which were illusory". Institutions only; the context line carries the inquiry. |
| 2 Dec, Leveson 2.16 (p. 546) | On 4 July 2011 the Guardian reported that News of the World journalists had hacked a missing schoolgirl's voicemail in 2002. It later corrected one claim in the story. Lord Justice Leveson's inquiry into the press ruled on the rest. | ¶2.8 and ¶2.16: the Guardian story of 4 July 2011; ¶2.9 to ¶2.17: disappearance 21 March 2002, hacking before 13 April 2002; ¶2.14 (Guardian, 9 May 2012): "Our error, as we acknowledged and corrected last December, was to have written about the cause of the deletions as a fact"; ¶2.15 and ¶2.16: the Inquiry's conclusions on the deletions and on the gravamen. Victim not named in the hook (the card quote names her; that card was accepted in #392). |
| 3 Dec, Grenfell 2.19 (p. 12) | Grenfell Tower was refurbished from 2012 to 2016, with new cladding and insulation on its outside walls. Phase 2 of the inquiry asked how it came to be in that condition. | ¶4.1 (p. 37), ¶4.39, ¶6.2: "the refurbishment … between 2012 and 2016"; ¶2.21: Reynobond panels "used in the external wall"; `editorial/uk-grenfell-tower-inquiry.yaml` background: "new cladding and insulation were fixed to its outside walls … Phase 2 asked how the building came to be in a condition that allowed the fire to spread". Deaths only in the context line. |
| 4 Dec, Lehman p. 742 | Repo 105 was Lehman Brothers' way of moving assets off its balance sheet for about a week at quarter-end. In late March 2008 Bart McDade, its head of equities, asked for a cap on it. A week later a colleague e-mailed to ask if he knew of it. | p. 732 (`lehman-employed-off-balance-sheet`): "known within Lehman as 'Repo 105' … to temporarily remove securities inventory from its balance sheet, usually for a period of seven to ten days"; p. 7: "to reduce balance sheet at the quarter-end"; p. 742 (`lehman-dramatically-ramped-up-its`): "Head of Equities (2005–2008)"; fn 2881 e-mail McDade to Hyung Lee, Apr. 3, 2008; "A week earlier, McDade had recommended to Lehman's Executive Committee that the firm set a cap on the use of Repo 105 transactions" (fn 2882). The card gives his reply. |
| 5 Dec, Lehman p. 745 | Ten days before its quarter closed in May 2008, Lehman Brothers' fixed income division was working to a balance sheet target set by senior management. The bankruptcy examiner quotes an e-mail exchange from that day. | p. 745 (`lehman-s-reliance-upon-repo`): "dated May 21, 2008 – ten days before Lehman's second quarter close"; p. 744 (`lehman-s-fixed-income-division`): FID "employed Repo 105 transactions to reach quarter-end balance sheet targets set by senior Lehman management"; the card is the second 21 May e-mail and its response. |
| 15 Dec, Post Office 6.205 (p. 158) | About 188 claims in GLOS, the redress scheme for those who sued the Post Office, were still unresolved. | ¶6.203: "By my arithmetic 188 claims (or perhaps a few less …) remain unresolved" (also ¶4.233, ¶6.115: as of 30 April 2025); ¶4.1 (p. 46): GLOS is "a scheme to provide additional financial redress for the claimants in the Group Litigation". No individual named. |

Also read: the eight composed `text`s (hook, `— common_name, p. N`, context line for Post Office and Grenfell), the week of 26 Nov to 5 Dec together for repeated openings and closings, and the strategy §8 rules (no deaths or victims as hooks, institutions as subjects, context lines present on all three affected-community items; 1 Dec and 15 Dec Post Office are 14 days apart).

## Fixups pushed

- **#395 `f84cbdc8`** (reviewer): two hooks rewritten, `pnpm posts` rebuilt their texts, `--check` and `--verify` pass. Sources also posted as a comment on the PR.
  - **4 Dec (Lehman p. 742)**, medium: the hook restated the card's own first clause (April 2008 e-mail, McDade, head of equities, Repo 105 to reduce balance sheet), so a reader met the setup twice; and it closed "The bankruptcy examiner quotes his reply" the day before 5 Dec's "The bankruptcy examiner quotes an e-mail exchange" (the skill's "A Senate subcommittee read the emails twice is once too many"). Now leads with what Repo 105 was and the cap McDade asked for, and ends where the card begins. 294 graphemes.
  - **29 Nov (Leveson p. 564)**, low: closed "Lord Justice Leveson's press inquiry went through …" three days before 2 Dec's "Lord Justice Leveson's inquiry into the press ruled on …". Now ends on the eight articles put before the inquiry. 286 graphemes.

## Findings not fixed

- **#396, minor, accepted**: an item hooked late posts out of its slot, and `queueViolations` spaces Post Office and Grenfell items by `scheduled`, not by posting day, so a late hook could put two such posts within 14 days. The PR body says so. Rare, visible in `pnpm posts` output, recoverable by re-dating. No bead.
- **#396, note**: the skip is also in the Worker only once `rtm-poster` is redeployed; until then the Worker would still throw on a due unhooked item and the workflow (which runs this code from main) would post it after 15:00 UTC instead. With #395 merged nothing is unhooked, so there is no window to worry about.
- **#395, note**: 29 Nov and 2 Dec both open with a date ("A landlord was arrested on 30 December 2010", "On 4 July 2011"), as do several earlier posts in the queue. Openings differ in subject; left.
- **Bead irro's text** still says `--check` fails on a due unhooked item; true until #396 merges. Close it on ship rather than rewrite.

## Found only by the trial merge

Nothing. Disjoint files; either merge order is clean.

## Integrator steps

1. `bd dolt pull`. Check origin/main is still `9853540f` and the heads are #395 `f84cbdc8`, #396 `50e1e543` (`gh pr view <n> --json headRefOid`). If anything else has landed on main that touches `marketing/queue.yaml`, see step 2's conflict recipe.
2. `gh pr merge 395 --squash`. If `marketing/queue.yaml` conflicts with a poster write-back on main (a `posted_url`/`posted_at` on an earlier item), take main's file on the branch, re-apply the 8 `hook:` values (they are in the PR diff), run `pnpm posts && pnpm posts --check && pnpm posts --verify`, commit, merge. Merge before 13:40 UTC on 29 Nov at the latest; it is ready now.
3. `gh pr merge 396 --squash`.
4. Pull the shared checkout `--ff-only`; in a worktree at the new main: `pnpm bootstrap && pnpm typecheck && pnpm test && pnpm posts --check && pnpm posts --verify`.
5. No site deploy is needed for #395 (both posters read the queue from main). For #396, redeploy the poster Worker when convenient: `pnpm wrangler deploy -c workers/poster/wrangler.toml` (the GitHub workflow already runs the new code from main). Not urgent: nothing in the queue is unhooked.
6. Close on ship: `bd close reportsthatmatter-irro`.

## Beads

- Noted on `reportsthatmatter-irro`: the verdicts and this file. Nothing new filed.

## Lessons

- A hook's "turn" should not be the card's own first clause. When the card quote is a long sentence whose payoff is its last words, the hook should lead with a fact from the neighbouring paragraphs and stop before the card's setup, or the reader reads the setup twice.
- Read a batch of hooks as a week before judging any one: the two repeats here (consecutive Lehman days; two Leveson posts three days apart) were invisible per item and obvious in the list `node -e` printed from the queue.

## Getting better faster

- The two-second `pnpm post-next` dry run with `POSTER_QUEUE` and `POSTER_TODAY` on a crafted two-item queue proved #396 on both code versions; it belongs in the pr-review skill for any poster PR.
- Fact-checking eight hooks against four reports took most of the time; a small `para.py` that prints a paragraph id with neighbours and page numbers from `full-body.html` made it fast. Worth a `pnpm` script if hooks become weekly.
- A reviewer rewrite of copy is itself copy to check: the first draft of the 4 Dec hook was 321 graphemes with the 53-character Lehman source line; count before committing.
