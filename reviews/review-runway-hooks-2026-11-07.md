# Review: 20 runway hooks, 6 Dec 2026 to 22 Jan 2027

Reviewed 2026-11-07 against `.claude/skills/post-writing/SKILL.md` and `docs/brand-voice.md`. Every factual claim in the 20 recommended hooks was checked in `assets/generated/reports/<id>/full-body.html` (paragraph ids as cited in the drafts), the registry (`reports/registry.yaml`) and `editorial/challenger-accident.yaml`. Hook limits below are 300 minus the composed source line (`\n\n— <common_name>, p. N`): Challenger 238/237, Deepwater 245, Philip Morris 239–241, Levin–Coburn 252–254, Valukas 247–249, Mueller 274–276, ICA 252, Leveson 273–274. Replacement hooks were measured against those limits. The two dropped items (9 Dec PCC, 9 Jan Repo 105 leverage) are not reviewed.

**Tally:** 13 ship, 7 revise, 0 drop. No hook contains a fact the report does not support; every revision is for shape, attribution or a soft gloss.

## Summary

| Date | Report | Verdict | Problem in one line |
| --- | --- | --- | --- |
| 6 Dec | Philip Morris, p. 1634 | ship | Card phrase overlap ("smoking causes disease") is the intended contrast, not a giveaway. |
| 7 Dec | Challenger, p. 5 | revise | No investigating body, no turn; opens "Before" four days before 11 Dec's "Six days before". |
| 8 Dec | Levin–Coburn, p. 9 | ship | "turns to" closing clashes with 10 Dec; fix on the 10 Dec side. |
| 10 Dec | ICA, p. 3 | revise | Closes "turns to the releases" two days after "turns to its clients". |
| 11 Dec | Deepwater, p. 2 | ship | Clean once 7 Dec stops opening on "Before". |
| 12 Dec | Valukas, p. 114 | ship | None. |
| 13 Dec | Philip Morris, p. 35 | revise | "the law as a calling" is our gloss; lawyers' conduct stated flat rather than as her finding. |
| 14 Dec | Challenger, p. 11 | revise (light) | Near-identical death sentence to 7 Dec a week apart; vary the wording. |
| 16 Dec | Mueller, p. 4 | ship | None; short but concrete. |
| 17 Dec | Levin–Coburn, p. 349 | revise (one word) | "called it shady when, in 2006" implies he said it in 2006; he told the subcommittee in 2010. |
| 3 Jan | Valukas, p. 9 | ship | None. |
| 4 Jan | Leveson, p. 1782 | ship | None; the hook supplies the clause the card needs. |
| 5 Jan | Philip Morris, p. 1543 | ship | None. |
| 7 Jan | Levin–Coburn, p. 3 | ship | "had words for" is colloquial, not a verdict. |
| 11 Jan | ICA, p. 8 | ship | None. |
| 13 Jan | Mueller, p. 158 | ship | Closes on a fact rather than a turn; acceptable. |
| 15 Jan | Leveson, p. 552 | ship | None; both sides of the exchange are present. |
| 17 Jan | Levin–Coburn, p. 2 | revise (light) | "The subcommittee" is never introduced; "On [date]" opening four days after 13 Jan's. |
| 19 Jan | Valukas, p. 21 | ship | Repo 105 is unglossed in the hook, but the card glosses it. |
| 22 Jan | Mueller, p. 9 | ship (optional tweak) | "three charging decisions" is a slight simplification of "three main components". |

## Per item

### 6 Dec — US v. Philip Morris, p. 1634 — ship

Hook: *In post-trial filings the cigarette companies told the court that "Reynolds Concedes That Cigarette Smoking Causes Disease." The judge set that claim against what was said on the witness stand.* (193)

- Facts: confirmed in `defendants-senior-executives-took-witness`: "Joint Defendants assert in their post-trial Proposed Findings of Fact that 'Reynolds Concedes That Cigarette Smoking Causes Disease'", set against Schindler's trial testimony and the website's "half-hearted concession". "the cigarette companies" for Joint Defendants is fair. Strictly it is one filing (the Proposed Findings of Fact); "In a post-trial filing" would be exact. Optional.
- Card repeat: the hook and card share "smoking causes disease", but the hook quotes the opposite claim (concedes) to the card's (refused to admit). That contrast is what makes the card land; it is not a paraphrase of the card and does not hand over "refused to admit". Acceptable.
- Card answers hook: yes. "what was said on the witness stand" is exactly what the card gives.
- Verdict/motive: none. "The judge set that claim against" is the report's own move.
- Stranger: yes; a company telling a court it concedes something its chairman would not say is a real hook.

### 7 Dec — Challenger (House Science Committee), p. 5 — revise

Hook: *Before Challenger was lost at launch on 28 January 1986, killing its 7 crew, problems with its booster joints had been presented to every level of shuttle management, including in a briefing at NASA Headquarters.* (212)

- Facts: all confirmed (`case-tragic-loss-space-shuttle`, `january-28-11-39-m`, `information-flaws-joint-design-problems`: "presented to all levels of Shuttle management ... at least one detailed briefing at Headquarters"; "Solid Rocket Booster joints" in the preceding block).
- The 7: the report never gives the number (I grepped "seven" against crew/astronaut: nothing). `editorial/challenger-accident.yaml` has it ("killing its crew of seven", verified 2026-09-26 against nasa.gov). The skill explicitly allows the landing-page introduction when the report has nothing, and the affected-community rule requires the count. Keep it. It is a basic, uncontested figure; dropping it would break the rule, not satisfy it.
- Problems: (a) The formula is event, who investigated, turn. This hook has the event and the odd detail but never says who wrote the document and has no turn; the card then arrives unannounced. Only the source line tells a stranger this is a House committee. (b) It opens "Before ..." and 11 Dec opens "Six days before ..." four days later. (c) Candidate 3 fixes (a) and (b) but then closes "A House committee recorded ..." a week before 14 Dec closes "A House committee later went through ...", and repeats 14 Dec's death sentence almost word for word.
- Replacement (221, limit 238), checked against `information-flaws-joint-design-problems` and `like-rogers-commission-committee-concluded`:

  > Challenger was lost at launch on 28 January 1986, killing its 7 crew. Problems with its booster joints had been briefed at NASA Headquarters, the House Science Committee found. Its report records what NASA did about them.

  The card ("NASA chose to continue to fly with a flawed design and to follow a measured, 27-month, corrective program") answers "what NASA did about them". "Briefed at NASA Headquarters" is the report's "at least one detailed briefing at Headquarters on the difficulties with the O-rings". Does not hand over "No one suggested grounding the fleet", the sentence immediately before the card.

### 8 Dec — Levin–Coburn, p. 9 — ship

Hook: *Goldman Sachs designed and underwrote about $100 billion of mortgage securities in 2006 and 2007. In 2007 its bets on a falling market peaked at $13.9 billion. The Senate report turns to its clients.* (199)

- Facts: confirmed (`from-2004-2008-goldman-major`: "In 2006 and 2007 alone, it designed and underwrote 93 RMBS and 27 mortgage related CDO securitizations totaling about $100 billion"; `over-next-two-months-rapidly`: "Throughout 2007 ... At its peak, Goldman's net short position totaled $13.9 billion"). "bets on a falling market" is a fair gloss of net short position and keeps the card's phrase off the hook.
- Card answers hook: yes.
- "The Senate report": the document is a Senate subcommittee's; "The Senate subcommittee's report" is more exact but the common name on the source line carries it. Optional.
- Closing "turns to its clients" clashes with 10 Dec's "turns to the releases" two days later. This one is the better of the two; fix 10 Dec.

### 10 Dec — 2017 Intelligence Community Assessment, p. 3 — revise

Hook: *Putin said publicly it was important the DNC data was exposed to WikiLeaks, while denying Russian "state-level" involvement. The 2017 US intelligence assessment says Russian military intelligence relayed it, then turns to the releases.* (235)

- Facts: confirmed (`early-september-putin-said-publicly`, `we-assess-high-confidence-gru`, `we-assess-high-confidence-russian` for GRU = Russian military intelligence). The Putin sentence is the report's own wording nearly verbatim; it is a public statement the report records, not an assessment, so stating it flat is acceptable.
- Partisan framing: in the report's own terms ("DNC data"). Putin is a current head of state, but he is the subject of the document, not a present-day pairing; the brand-voice rule is aimed at importing current domestic politics. Judgement call; candidate C avoids him if the editor reads the rule strictly.
- Card answers hook: yes, though "the releases" promises a little more than one sentence about forgeries delivers. "adds one sentence on the releases" is more honest about the size of the payoff.
- Problem: closing "turns to the releases" repeats 8 Dec's "turns to its clients" two days later.
- Replacement (246, limit 252):

  > Putin said publicly it was important the DNC data was exposed to WikiLeaks, while denying Russian "state-level" involvement. The 2017 US intelligence assessment says Russian military intelligence relayed it, and adds one sentence on the releases.

  If the Putin opening is unwanted (240): *Russian military intelligence relayed material from the DNC and senior Democratic officials to WikiLeaks, the 2017 US intelligence assessment says with high confidence. It gives Moscow's likely reason, and adds one sentence on the releases.* (All from `we-assess-high-confidence-gru`.)

### 11 Dec — Deepwater Horizon Commission, p. 2 — ship

Hook: *Six days before the Macondo well blew out under the Deepwater Horizon, killing 11 workers, a BP engineer who helped design it called it a "nightmare well" in an email. BP and its partners had budgeted $96.2 million and 51 days.* (227)

- Facts: confirmed. Email 14 April 2010 (`bp-drilling-engineer-brian-morel`, footnote: "e-mail message to Richard Miller and Mark Hafle, April 14, 2010"), blowout 20 April (`dedication-report-dedicated-11-men`): six days. "who had designed the Macondo well with other BP engineers" supports "helped design it". Budget "$96.2 million and 51 days" is in the text (sourced there to "government reports"). 11 deaths: `deepwater-horizon-rig-sank-april`, `pride-transocean-fleet-offshore-drilling`.
- Deaths: stated once, plainly, as a fact inside the event sentence. Correct per the rule. Deepwater is `affected_community: true` with no `context_line`; the rule says the hook must carry the deaths, and it does.
- Card repeat: none ("behind schedule", "over budget" absent). Card answers hook: the budget sentence sets up exactly what the card says.
- Document: the hook never names the Commission; the source line does. Acceptable at this length.
- Opening: once 7 Dec no longer opens "Before", the clash is gone. If 7 Dec is kept as drafted, use the drafter's candidate 3 here instead.

### 12 Dec — Valukas (Lehman examiner), p. 114 — ship

Hook: *In March 2007 Michael Gelband, head of Lehman Brothers' fixed income division, emailed chief executive Richard Fuld that he was trying to cut illiquid risk. In May Lehman said he had "decided to leave". The examiner gives other reasons.* (236)

- Facts: confirmed (footnote 420 to `gelband-removed-from-position-several`: e-mail Gelband to Fuld, Mar. 6, 2007, "trying to get out of as much illiquid risk as possible"; `may-1-2007-lehman-announced`; "Fuld, who was CEO and Chairman of the Board"; "then head of FID" / "then-acting Global Head of FID"). The report itself cites the March e-mail as evidence of the clash over growth, so the juxtaposition is the report's, not ours.
- Card answers hook: yes. Footnote 419 marks Fuld and McDade "contra"; the hook claims nothing about the reasons itself, so that is fine. Worth noting in the PR.

### 13 Dec — US v. Philip Morris, p. 35 — revise

Hook: *Judge Gladys Kessler's 2006 ruling against the cigarette companies turns to their lawyers, who told scientists what research to do and devised policies for destroying documents. Her next sentence is about the law as a calling.* (226)

- Facts: confirmed (`finally-word-must-be-said`: "directed scientists as to what research they should and should not undertake", "devised and carried out document destruction policies"; the card is the next body sentence, with only footnote 2 between). "Ruling against the cigarette companies" is a fair summary of a judgment that found for the Government on most claims.
- Problems: (a) "the law as a calling" is our interpretation; the card says "profession". "Calling" adds a note of reverence the report does not use. (b) "who told scientists ... and devised policies" states the judge's findings as plain fact in a relative clause; a journalist would want them marked as hers.
- Replacement (238, limit 241):

  > Judge Gladys Kessler's 2006 ruling against the cigarette companies turns to their lawyers, who, she found, told scientists what research to do and devised policies for destroying documents. Her next sentence is about the legal profession.

  "Profession" names the card's subject in plain words, which the rule allows; it does not hand over "sad and disquieting" or "honorable and often courageous".

### 14 Dec — Challenger (House Science Committee), p. 11 — revise (light)

Hook: *The launch team's intercom was recorded during Challenger's last countdown. The shuttle was lost at launch in January 1986, killing its 7 crew. A House committee later went through the tapes and transcripts.* (207)

- Facts: confirmed (`conversations-obtained-from-operational-intercommunication`: "Conversations obtained from the Operational Intercommunication System (OIS), used by the launch team during Shuttle countdowns ... The Committee reviewed tapes and transcripts"). The 7: as for 7 Dec.
- Card answers hook: yes, the card is what the tapes showed. "waived", "launch commit criteria" absent from the hook. Good hook; the intercom is the odd concrete detail.
- Problem: "The shuttle was lost at launch in January 1986, killing its 7 crew" is the 7 Dec sentence again a week later (and the 30 Oct post's). The count is required each time; the sentence need not be identical.
- Replacement (204, limit 237):

  > The launch team's intercom was recorded during Challenger's last countdown. The shuttle was lost at launch in January 1986; its 7 crew died. A House committee later went through the tapes and transcripts.

### 16 Dec — Mueller Report, p. 4 — ship

Hook: *The Mueller report says the GRU, the Russian Army's intelligence service, stole hundreds of thousands of documents in 2016. Its summary names the first targets.* (160)

- Facts: confirmed (`same-time-ira-operation-began`: "Main Intelligence Directorate of the General Staff of the Russian Army (GRU)"; `march-2016-gru-began-hacking`: "stole hundreds of thousands of documents"; the paragraph sits under "EXECUTIVE SUMMARY TO VOLUME I", so "Its summary" is right).
- Card answers hook: yes (March 2016, Clinton Campaign volunteers and employees, Podesta). Partisan: report's own terms. Short, but the number carries it.

### 17 Dec — Levin–Coburn, p. 349 — revise (one word)

Hook: *Greg Lippmann, who ran CDO trading at Deutsche Bank, expected the securities to lose value. He called it "shady" when, in 2006, hard-to-sell CDO slices began going into new CDOs. In December 2006 he emailed about where the CDOs were ending up.* (243)

- Facts: confirmed (footnote 1176 "Global Head of Trading of CDOs for Deutsche Bank"; `lippmann-had-unrelentingly-negative-view`; `lippmann-told-subcommittee-considered-shady`; `december-2006-email-lippmann-wrote`, footnote 1339 dated 12/4/2006). Right to leave the recipient out: body says "a client", footnote says "Deutsche Bank employee".
- Problem: "He called it 'shady' when, in 2006, ..." reads as a 2006 remark. The report says he "told the Subcommittee that he considered it a 'shady' practice" (interview 10/18/2010). "Called" is a speech act; the report's verb is a state of mind.
- Card repeat: none ("ponzi scheme" kept off the hook). Card answers hook: yes.
- Replacement (247, limit 252): change "called it" to "considered it", which is the report's own verb and keeps its sentence shape:

  > Greg Lippmann, who ran CDO trading at Deutsche Bank, expected the securities to lose value. He considered it "shady" when, in 2006, hard-to-sell CDO slices began going into new CDOs. In December 2006 he emailed about where the CDOs were ending up.

### 3 Jan — Valukas (Lehman examiner), p. 9 — ship

Hook: *In March 2008 US regulators posted monitors inside Lehman Brothers to watch its liquidity. All year Lehman said its reserves could weather any foreseeable downturn. Three days before its bankruptcy, its own table counted them.* (226)

- Facts: confirmed (`mid-march-2008-after-bear`: SEC and FRBNY monitors "took up residence at Lehman ... with particular focus on liquidity"; "Lehman publicly asserted throughout 2008 that it had a liquidity pool sufficient to weather any foreseeable economic downturn"; footnote 37 "Liquidity Pool Table ... (Sept. 12, 2008)"; bankruptcy 15 September 1:45 a.m., `lehman-no-longer-had-sufficient`). "Reserves" for "liquidity pool" is a plain-words gloss. No figure given, which is right: the text has $40bn, $41bn and $42bn for the same call.
- Card answers hook: yes, exactly. Good hook.

### 4 Jan — Leveson Report, p. 1782 — ship

Hook: *Lord Justice Leveson said that even if every major publisher joined an independent regulator the industry set up itself, he would still recommend a statute behind it. Some said that went too far.* (195)

- Facts: confirmed (`7-6-let-me-further`: "if an adequately independent regulatory body were to be established by industry and signed up to by all major news publishers, I still recommend the underpinning statute"; `7-8-challenge-goes-too`: "As for the challenge that this goes too far").
- "Some said that went too far" is the clause the card omits; the card's "these provisions" needs that antecedent. This is the mechanism-that-makes-the-card-make-sense pattern that won in the test, not a giveaway: the card's content (no chilling effect) is not in the hook.
- Contested sentence, hook neutral. Ship.

### 5 Jan — US v. Philip Morris, p. 1543 — ship

Hook: *The tobacco blend, the paper, the filter, the additives: cigarette makers studied how each one affects the nicotine a cigarette delivers. In 2006 a federal judge set out what that work had cost them.* (199)

- Facts: confirmed (`defendants-dedicated-substantial-resources-devising`: "the tobacco blend, the paper, the filter, additives, and the manufacturing process -- affects nicotine delivery"; signature `s-august-17-2006-gladys`).
- Card answers hook: yes, "many millions of dollars and thousands of scientist hours". No key phrase on the hook. The list opening is a good change of shape.

### 7 Jan — Levin–Coburn, p. 3 — ship

Hook: *In 2003 Washington Mutual's CEO, Kerry Killinger, compared his ambitions for the bank to Wal-Mart's. Its subprime securitisations, mostly through its Long Beach arm, grew sixfold by 2006. Its president had words for Long Beach.* (227)

- Facts: confirmed (`many-years-wamu-mid-sized-thrift`, footnote 105; `case-study-focuses-how-one`: "sixfold, primarily through its subprime lender, Long Beach Mortgage Corporation ... 2003 ... 2006"; `other-key-members-bank-s`: "President Steve Rotella").
- Card answers hook: yes. The hook makes clear the words are the president's, so nobody reads "terrible", "a mess" as the subcommittee's.
- "had words for" is colloquial and implies criticism, but says nothing the card does not. Not a verdict. Ship.

### 11 Jan — 2017 Intelligence Community Assessment, p. 8 — ship

Hook: *The Kremlin spends $190 million a year distributing RT, says a December 2012 analysis reprinted in the 2017 US intelligence assessment. It quotes RT's editor-in-chief, Margarita Simonyan, on what a channel abroad is for.* (220)

- Facts: confirmed (`kremlin-spends-190-million-year`; annex note "originally published on 11 December 2012 by the Open Source Center"; `rt-s-criticism-us-election`: "RT Editor in Chief Margarita Simonyan"; `simonyan-claimed-popular-arts-magazine`, whose quote continues "not having our own foreign broadcasting is the same as not having a ministry of defense", which supports "a channel abroad").
- Attribution is right: the figure is the 2012 annex's ("says"), and the card is Simonyan's words, which the hook makes unmistakable. Card answers hook: yes.

### 13 Jan — Mueller Report, p. 158 — ship

Hook: *On 19 June 2017 the President dictated a message for Attorney General Sessions to his former campaign manager, Corey Lewandowski, who wrote it down as fast as possible. The message was not delivered.* (199)

- Facts: confirmed (`efforts-curtail-special-counsel-s`; `during-june-19-m-eeting`: "wrote as fast as possible to make sure he captured the content correctly"; the card's own paragraph: "Lewandowski and Dearborn did not deliver the President's message to Sessions").
- Card answers hook: the hook ends on a fact, not a turn, and the card then generalises (efforts mostly unsuccessful because people declined). It works as instance-then-pattern, and "not delivered" is from the card's paragraph, not the card. If a turn is wanted, "Volume II of the Mueller report says why." would replace the last sentence (same length), but it is not needed.
- Partisan: "the President", the report's term. Avoids McGahn (1 Nov). Ship.

### 15 Jan — Leveson Report, p. 552 — ship

Hook: *Asked about his company's coverage of one story, Richard Desmond, proprietor of Express Newspapers, told the Leveson inquiry "we did do wrong" and apologised. Then he talked about regulation. The judge gave his view of that answer.* (231)

- Facts: confirmed. `3-21-self-same-logic` ("the proprietor of Express Newspapers, Richard Desmond, when he was asked about this topic") and its quoted answer ("we did do wrong"); the quoted answer under `3-22-notwithstanding-language-deployed` has both "once again I do apologise to the McCanns" and the turn to "free speech ... any more regulation"; the card (3.23) is Leveson's view of that second answer.
- Private individuals: the McCanns are not named; right.
- The card is a judge's adjective-laden view of a named witness. The hook gives Desmond's acknowledgement and apology first, marks the view as the judge's, and adds no adjective of ours. Ship.

### 17 Jan — Levin–Coburn, p. 2 — revise (light)

Hook: *On 25 September 2008, 119 years to the day after its founding, Washington Mutual was seized and sold to JPMorgan Chase for $1.9 billion. Nearly $17 billion in deposits had left in 8 days. The subcommittee says what rode on the sale.* (232)

- Facts: confirmed (`many-years-wamu-mid-sized-thrift`: "On September 25, 2008, 119 years to the day of its founding, WaMu was seized by its regulator ... sold to JPMorgan Chase for $1.9 billion"; `september-15-2008-lehman-brothers`: "Over the span of eight days starting on September 15, nearly $17 billion in deposits left the bank"). Right to omit total assets ($300bn vs $307bn in the text).
- Problems: (a) "The subcommittee" has not been introduced; a stranger does not know which. (b) Opens "On [date]" four days after 13 Jan opens "On 19 June 2017"; and 7 Jan, ten days earlier, also opens with a year and Washington Mutual.
- Card answers hook: yes.
- Replacement (237, limit 254):

  > Washington Mutual was seized on 25 September 2008, 119 years to the day after its founding, and sold to JPMorgan Chase for $1.9 billion. Nearly $17 billion in deposits had left in 8 days. A Senate subcommittee says what rode on the sale.

### 19 Jan — Valukas (Lehman examiner), p. 21 — ship

Hook: *Matthew Lee, a Lehman Brothers senior vice president, alleged accounting improprieties in a May 2008 letter to management. On 12 June he told the auditors, Ernst & Young, about Repo 105. Their response is in the examiner's report.* (230)

- Facts: confirmed (`may-2008-lehman-senior-vice`: "In May 2008, a Lehman Senior Vice President, Matthew Lee, wrote a letter to management alleging accounting improprieties"; "Ernst & Young was advised by Lee on June 12, 2008 that Lehman used $50 billion of Repo 105 transactions"; `lehman-did-not-disclose-its`: "Lehman's auditors, Ernst & Young").
- "Repo 105" is unglossed in the hook, which the test says loses readers. I tried glossing it; every version that keeps the letter, the date and the auditors runs over the 248 limit. The card itself supplies the gloss ("$50 billion of temporary, off-balance sheet transactions"), and the 30 Nov, 4 Dec and 5 Dec posts have glossed it for followers. Ship as drafted.
- Card answers hook: yes ("took no steps" is the response).

### 22 Jan — Mueller Report, p. 9 — ship (optional tweak)

Hook: *Volume I of the Mueller report sets out three charging decisions. The first: Russia's two principal interference operations violated US criminal law. The second concerned the Trump Campaign.* (190)

- Facts: `section-v-report-provides-detailed`: "the Office's charging decisions, which contain three main components"; `first-office-determined-russia-s`; `second-while-investigation-identified-numerous`. "Three charging decisions" slightly flattens "three main components" (the first component holds several decisions: IRA charged, GRU officers charged, Stone and Trump Jr. not). Optional exact version (202): *Volume I of the Mueller report sums up its charging decisions in three parts. The first: Russia's two principal interference operations violated US criminal law. The second concerned the Trump Campaign.*
- Partisan: both findings in the report's own words; "Trump Campaign" is the report's term and on the card. Neutral.
- Card answers hook: yes. The structural opening is the kind that lost in the test, but the two findings carry it. Ship.

## Set-level checks

- Openings after the revisions: filing, Challenger lost, Goldman, Putin, Six days before, In March 2007, Judge Kessler, intercom, Mueller report says, Lippmann, In March 2008, Lord Justice Leveson said, a list, In 2003 WaMu, the Kremlin, On 19 June 2017, Asked about, Washington Mutual was seized, Matthew Lee, Volume I. No two within ten days share a shape.
- Closings after the revisions: 8 Dec "turns to its clients" and 10 Dec "adds one sentence on the releases" no longer match; 7 Dec "Its report records what NASA did about them" and 14 Dec "A House committee later went through the tapes and transcripts" differ; 12 Dec "The examiner gives other reasons" and 19 Jan "Their response is in the examiner's report" are five weeks apart.
- Deaths: Challenger (7 Dec, 14 Dec) and Deepwater (11 Dec) each state the deaths once, plainly, inside the event sentence; no victim named, no bereaved, no anniversary. The Challenger count comes from the landing-page introduction because the report text has none; the skill allows that source when the report has nothing, and the rule requires the count. Keep it.
- Partisan pairing: the Mueller (16 Dec, 13 Jan, 22 Jan) and ICA (10 Dec, 11 Jan) hooks all use the report's own terms ("the President", "Trump Campaign", "DNC data", "Clinton Campaign" on the card). The only judgement call is opening 10 Dec on Putin's words; he is the document's subject, and the alternative is given above.
- Drop: none. 10 Dec's card is the thinnest payoff of the set, but the finding is the assessment's own and the hook is honest about it.

## Three issues that matter most

1. **Repeated shapes inside ten days.** "turns to" closes 8 Dec and 10 Dec two days apart; 7 Dec and 11 Dec both open on "Before"; the two Challenger posts carry the same death sentence a week apart; 13 Jan and 17 Jan both open "On [date]". The replacements for 7 Dec, 10 Dec, 14 Dec and 17 Jan clear all four.
2. **7 Dec has no document and no turn.** It is the one hook that forgets the formula: a stranger is not told a House committee wrote this or why the card follows. The replacement names the committee and ends where the card begins.
3. **13 Dec is the only verdict-adjacent wording in the set.** "The law as a calling" is ours, not the judge's, and the lawyers' conduct is stated flat instead of as her finding. Everything else factual in the 20 hooks checks out against the text; the remaining fixes (17 Dec "called" to "considered", 17 Jan "A Senate subcommittee") are single words.
