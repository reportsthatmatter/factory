# Lighthouse mobile on production, 2026-10-25

Lighthouse 12.8.2, default mobile profile (simulated throttling), performance category only, after the self-hosted fonts change (3gx4) and the Leveson /full change (te56) were deployed. One run per page, so expect a few points of noise.

| Page | Score | LCP | CLS | TBT |
|---|---|---|---|---|
| / | 99 | 1.6 s | 0.01 | 0 ms |
| /reports/uk-grenfell-tower-inquiry (landing) | 100 | 1.0 s | 0 | 0 ms |
| /reports/uk-grenfell-tower-inquiry/chapter-2-executive-summary (section) | 98 | 2.2 s | 0.032 | 0 ms |
| /reports/uk-leveson-inquiry/full | 28 | 14.6 s | 0.008 | 1,730 ms |
| /reports/us-911-commission (landing, for comparison) | 100 | 1.3 s | 0 | 0 ms |

## Against earlier figures

- 9/11 landing: audit 2026-10-09 score 81, LCP 2.9 s; 3gx4 PR "before" 1.71 s simulated; now 100, 1.3 s. No regression from self-hosted fonts; the review's worry (+0.4 s simulated) did not show in production.
- Leveson /full: te56 audit score 25, LCP 15.1 s, TBT 3.5 s; now 28, 14.6 s, TBT 1.7 s. Essentially unchanged for a browser reader, as expected: te56 (decision 0019) only gives preview crawlers a head-only page and de-emphasises the link on mobile. The page is still about 10.9 MB of HTML.
- CLS: all under 0.1 (a0rx's 0.28 font-swap shift is gone). The section page's 0.032 is the highest; fine.
- Home and Grenfell landing show a "render-blocking" saving (720 ms and 90 ms estimated), the site stylesheet; no figure to compare with and scores are 99-100, so not filed.

Verdict: no regression after 3gx4; no bead filed.
