# Teammate effects: measurement, size, formation and bases

Research for the synergy model (issue #7). It follows on from `research/player-roles.md` on the `research/player-roles` branch and tries not to repeat it. That file covered role taxonomies, FM and EA chemistry, the JOI/JDI headline numbers and passing-network basics. This one goes into full texts on how teammate effects are measured, how big they are, how they form and what drives them.

Confidence tags used throughout:
- **[full text]**: I read the paper itself.
- **[abstract]**: I saw only the abstract, a publisher page, a press release or a secondary summary.
- **[unverified]**: a claim I could not trace to a primary source.

## Answer in brief

1. **Teammate effects are real, but they are mostly structural.** The strongest evidence comes from models where synergy arises from a nonlinear production function plus players' skill profiles, not from free pair-specific terms. Maymin's Skills Plus Minus and Arcidiacono et al.'s spillover model both work this way. Free pair-interaction terms, fitted directly, mostly shrink to zero (Gramacy et al. in the NHL) or vanish once you control for selection and timing (Fourie in cricket).
2. **The size is modest.** In the NBA, lineup synergy runs from about −0.8 to +1.2 points per game per team, worth up to about 6 wins (Maymin). A 1 SD better "spillover" (making teammates better) is worth about as much as a 1 SD better own-scoring ability, roughly 28-40% of the cross-team SD of point differential (Arcidiacono). In football, pair-level event models explain about a third of the variance in offensive pair chemistry and almost none of the defensive (Bransen & Van Haaren).
3. **Familiarity matters most at the start and saturates fast.** The best-evidenced role of familiarity is buffering disruption, not a big main effect. Net of team quality the main effect is small or nil (Le Coz et al.; Fourie). Pass-level familiarity still helps (Le Coz et al.; Grund 2016), and the curve flattens after about 50 matches (Bransen & Van Haaren).
4. **Composition rules beat pair magic.** Stacking players who compete for the same scarce resource (the ball, shots, the same zone) has diminishing or negative returns. Stacking rare-event skills (ball-winning, offensive rebounding) has positive returns (Maymin). Talent overall shows diminishing returns but no decline: the "too-much-talent" effect does not replicate (Gula et al.; Long et al.).
5. **Culture, language and nationality effects are tiny and mixed.** They are at most a tie-breaker.

## 1. How teammate effects are measured

| Method | What it estimates | How it handles confounders | Needs | Key source |
|---|---|---|---|---|
| With/without and on/off splits | Team output with player A, with and without B | Barely. It is confounded by opponent, game state, injuries, rotation, and the reasons the manager picked that lineup | Lineups, minutes, goals | Folk analytics; critiqued throughout the RAPM literature |
| Pair plus-minus / RAPM with interaction columns | Pair coefficient over and above the two players' main effects | Ridge or Bayesian shrinkage controls collinearity. It does not fix selection (who gets picked together) | Lineup-segment data (substitution times) | Gramacy, Jensen & Taddy 2013; Kharrat et al. 2017 |
| Structural production function with skills | Synergy as combined output minus the sum of parts, implied by a nonlinear model of event probabilities | Identified from many different lineup combinations. Synergy is a function of skills, so there is no free pair parameter to overfit | Play-by-play events per possession | Maymin, Maymin & Shen 2013 |
| Spillover econometrics | A player's effect on teammates' scoring rates, separate from his own | Identified from players switching teammates (trades, injuries, rotation) with fixed effects | Possession-level data with all ten players | Arcidiacono, Kinsler & Price 2017 |
| Pair VAEP / xT chains (JOI, JDI) | Value of actions passed between A and B, or jointly defended | Normalised per 90 minutes together. Not causal: it partly measures who the coach plays together and team style | Event data with player ids | Bransen & Van Haaren 2020 |
| Passing networks | Who passes to whom; intensity and centralisation | Descriptive. Team-level regressions control for opponent and quality in some papers | Event data (pass origin and receiver) | Grund 2012; Grund 2016 |
| Familiarity / shared-experience regressions | Effect of time played together on output | Usually team fixed effects plus quality controls. Quality confounding is the main threat | Minutes or matches together (basic stats suffice) | Pasarakonda et al. 2023; Le Coz et al. 2025; Mukherjee et al. 2019 |
| Natural experiments (injury, red card) | Effect of losing a partner unexpectedly | The best design for causality, but events are rare and power is low | Lineups, event timestamps | Pasarakonda et al. 2023 (red cards); Fourie 2026 (injuries) |

### Notes on specific methods

**RAPM in football.** Kharrat, López Peña & McHale built football plus-minus on 129,988 lineup segments covering 10,983 players. They needed ridge regularisation because the design matrix is nearly singular: teammates share most of their minutes. They also used time-decay weights and league-strength terms, and they did not fit pair terms [full text] ([arXiv 1706.04943](https://arxiv.org/abs/1706.04943)). If the main effects already need heavy shrinkage, pair terms, at roughly 50 times as many parameters, are hopeless without strong structure.

**Pair interactions in hockey.** Gramacy, Jensen & Taddy added every observed player-pair interaction (about 27,000 pairs) to a regularised logistic model of NHL goals. They found "little evidence of significant player interaction effects": only four pairs came out non-zero. One was a large negative for Krejci–Wideman, and one was a positive for Thornton–Marleau. Adding the interactions changed the individual player estimates negligibly [full text] ([arXiv 1209.5026](https://arxiv.org/abs/1209.5026), Appendix B; JQAS 9(1) 97-111, 2013). Lesson: free-form pair effects are mostly noise at realistic sample sizes.

**Structural synergy (Maymin).** Maymin, Maymin & Shen model each possession as a chain of nested probits for ball-handling (turnover or steal), then scoring, then rebounding. Each player contributes offensive and defensive skill ratings. The data cover 2006/07-2009/10: 987,343 plays and 360 players. A lineup's synergy is its predicted output minus the sum of its players' individual contributions. Synergy therefore falls out of the nonlinearity and the skill mix, with no pair-specific parameter. The authors check the model with calibration plots: actual against predicted steals and offensive rebounds, which fall inside the 95% bands [full text] (IJCSS 12(2), 2013, "NBA Chemistry: Positive and Negative Synergies in Basketball"). This is the structure issue #7 already proposes to copy, and the full text supports it.

**Spillovers (Arcidiacono et al.).** The probability that player i scores on a possession scales as exp(o_i · (1 + Σ s_k)) over his teammates' spillover parameters s_k, with a matching defensive term. Spillovers are identified only through players changing teammates. The data are NBA possessions from 2006-2009: 905,378 possessions and 656 players [full text of the 2013 working paper] ([SOLE 2013 PDF](https://sole.memberclicks.net/assets/docs/14086.pdf); published as *Journal of Labor Economics* 35(1) 191-225, 2017, [doi:10.1086/687529](https://doi.org/10.1086/687529)). Their productivity measure correlates 0.78 with APM and only 0.42 with PER. Box-score stats miss most of the spillover.

**The confounder that kills naive pair estimates.** Fourie's cricket study has 1,355,340 bowler-overs. With fixed effects only, it finds partner effects of ±2-6%. After controlling for match timing (captains choose when to bowl whom), the effect is −0.43% per SD, 95% CI [−1.30%, +0.45%]. The familiarity coefficient is +0.000153 (SE 0.000131), which is not significant, and pair-specific excess output does not predict held-out output. An injury-shock design had only 93 events, giving a minimum detectable effect about 30 times the largest timing-controlled coefficient [full text; a 2026 working paper, not peer-reviewed, which the author says was AI-assisted, so treat it as weak]. The general point is well established in the coworker-peer-effects literature: managers choose who plays together and when, so co-selection masquerades as chemistry. Mas & Moretti (2009, AER) is the classic non-sports example, cited in both Arcidiacono and Fourie [I did not read Mas & Moretti directly].

## 2. How big the effects are

### Lineup and pair synergy

- **NBA lineup synergy (Maymin).** Team-level synergy ranges from Orlando at +1.2 points per game to Minnesota at −0.8. Summed player contributions run about 7-32 points over replacement, so synergy is a few percent of lineup value. Converted through Pythagorean expectation, the swing is up to about 6 wins a season. Teams were about as likely to show negative chemistry as positive [full text].
- **Self-synergy and cross-synergy coefficients (Maymin)**, from adding a second unit of the same skill or pairing two skills:

  | Skill pairing | Synergy |
  |---|---|
  | Offensive ball-handling with itself | −0.825 |
  | Offensive scoring with itself | −0.826 |
  | Defensive rebounding with itself | −0.394 |
  | Defensive scoring with itself | −0.284 |
  | Defensive ball-handling (steals) with itself | +0.307 |
  | Offensive rebounding with itself | +0.293 |
  | Offensive ball-handling × offensive rebounding | +0.550 |
  | Offensive ball-handling × offensive scoring | +0.550 |
  | Offensive scoring × defensive rebounding | +0.254 |
  | Offensive scoring × offensive rebounding | −0.191 |

  The units are the paper's synergy scale, not points [full text].
- **Trades (Maymin).** 222 of the possible trades (2%) were mutually beneficial purely through synergy. Chris Paul for Deron Williams is one example, because Utah already had steal-makers and steals synergise. Nelson–Turkoglu was a good fit and Gomes–Foye a bad one: both are ball-protecting scorers, so their common-event skills clash. Free-agent rankings changed depending on the roster [full text].
- **Spillover magnitude (Arcidiacono).** A 1 SD improvement in a player's own offensive intercept, spillover or defence raises team point differential by 0.032, 0.024 and 0.035 points per possession. Against a cross-team SD of 0.087, that is about 28-40% per 1 SD. Spillover is roughly as valuable as own scoring skill. Point guards are the biggest spillover producers. Carmelo Anthony drops 1.31 SD in value once his negative spillover is counted, and Chris Andersen's spillover is about +2.5 SD. Simulated moves: LeBron James to Miami raises predicted point differential from 0.043 to 0.219, and Amar'e Stoudemire was worth more to the Knicks than to Nash's Suns [full text]. The scoring part has a substitution effect because players share one ball.
- **The market misprices spillover (Arcidiacono).** Pay rises +37% per SD of own offence and +17% per SD of defence, but almost nothing per SD of spillover in single-season regressions, and about +12% over a career [full text]. This suggests intuition-based ratings undervalue facilitators. A game that showcases facilitator value would be telling the player something true and non-obvious.
- **Football pair chemistry (Bransen & Van Haaren).** The top JOI90 pairs, which needed 900+ minutes together, were:
  - Salah–Firmino, 0.7077 (Liverpool, 2017/18 Champions League);
  - Suárez–Messi, 0.6497 (Barcelona, 2015/16 Liga);
  - Ibrahimović–Di María, 0.5972.

  Predicting chemistry for unseen pairs, the model cut JOI RMSE from 0.05448 to 0.04464, about 33% of the variance explained (1 − ratio²). For JDI it went from 0.89075 to 0.88906, about 0.4% [full text] ([arXiv 2003.01712](https://arxiv.org/abs/2003.01712)). Offensive chemistry is partly predictable from profiles; defensive chemistry, as measured, is not.
- **NHL pair interactions (Gramacy).** Effectively zero, apart from four pairs [full text].

### Familiarity and shared history

- **Prior shared success (Mukherjee, Uzzi et al.).** This is the weighted density of past wins a squad has shared, counted in the EPL from 2002/03. On the 2013/14 EPL season, adding it to a skill-only model raised R² from 0.284 to 0.310 and correct predictions from 73% to 76%. The other leagues:
  - NBA: R² 0.244 to 0.256;
  - IPL cricket: R² 0.269 to 0.425 (correct predictions 71% to 78%);
  - MLB: R² 0.064 to 0.105.

  Skill explains 6.4-28.4% of variance; shared success adds 1.2-15.6 points [full text] (*Nature Human Behaviour* 3, 2019, [doi:10.1038/s41562-018-0460-y](https://doi.org/10.1038/s41562-018-0460-y)). Caveat: winning together is itself a marker of quality, so this is not clean evidence of chemistry.
- **Red-card disruption (Pasarakonda et al.).** EPL 2006/07-2008/09, 2,280 team-games. A red-card loss costs about −0.01 goals per minute. High *prior* familiarity buffers the loss, with an interaction of +0.01 (p = .036). Teams explain 5% of the variance in goals but 37% of the variance in coordination (pass accuracy). Current familiarity has a U-shaped relationship with coordination, not the hypothesised inverse U [full text of the first half via [PMC11999323](https://pmc.ncbi.nlm.nih.gov/articles/PMC11999323/); *Group & Organization Management* 50(3) 840-895].
- **Shared experience at match level vs pass level (Le Coz, Carling, Houde & Imbach 2025).** Across about 19,700 international matches, shared experience has no independent effect on results once team strength and FIFA ranking are controlled. Across 1,602 matches, shared experience combined with successful prior interactions predicts pass success. A 20-day window and a 12-month window both help; exponential decay adds nothing [abstract] (*Journal of Big Data* 12:157). The ResearchGate version gives 16,720 matches, so the match count differs between versions.
- **Pass choice (Grund 2016).** Pairs pass to each other more when they have played together before, controlling for selection. Familiarity does not make the team's passing more decentralised [abstract] (*American Behavioral Scientist* 60(10) 1260-1280).
- **NBA shared experience (Berman, Down & Hill 2002).** 23 teams, 1980/81-1993/94. Shared experience has an inverted-U relationship with wins, which the authors call "ossification". Losing teams that kept their roster together won about 5.7 more games the next season, against about +1.2 for teams that churned [abstract and University of Washington press release] (*Academy of Management Journal* 45(1) 13-31).
- **NBA coordination errors (Sieweke & Zhao 2015).** U-shape between familiarity and coordination errors, moderated by the leader's experience [abstract] (*Journal of Organizational Behavior* 36(3) 382-402).
- **Familiarity curve in football (Bransen & Van Haaren).** Chemistry is low for pairs with few matches together, rises quickly over the first few matches, and the effect is much weaker beyond about 50 matches [full text].
- **Cricket (Fourie).** No familiarity effect net of timing [full text, weak source].

### Losing a key partner

- **Özil after Sánchez (Bransen & Van Haaren).** Özil's chemistry fell after Sánchez left in January 2018. By 2019/20, Arsenal's top-5 pairs averaged below the Premier League average [full text; descriptive, n = 1].
- **Star portability (Groysberg, Lee & Nanda 2008).** Among sell-side analysts from 1988 to 1996, stars who moved firms suffered an immediate performance drop that lasted at least five years. The drop was worst for solo movers and for moves to weaker firms [abstract] (*Management Science* 54(7) 1213-1230). A secondary review says stars who moved *with their team* showed almost no decline [unverified against the full text]. This is the clearest non-sports evidence that part of "individual" performance belongs to the partnership.
- **Injury burden in football.** Higher match availability is associated with higher league ranking (p = .031) in the UEFA Champions League injury study (Hägglund et al. 2013, *BJSM*) [abstract]. This is team-level availability, not partner-specific.

### Talent stacking

- **Swaab et al. 2014** claimed an inverse U, where too much talent hurts, in football and basketball but not baseball [abstract].
- **Gula et al. 2021** re-analysed 64 NBA seasons (N = 1,417 team-seasons). The turning point sits at the edge of the data. A two-lines test gives a positive second slope (b = 0.31). Talent is "never detrimental"; there are only diminishing returns [full text] (*Psychological Science* 32(2) 301-304).
- **Long, Graf & Bilalić 2024** looked at 7 football leagues, 2016/17-2021/22, N = 780 team-seasons. Talent share correlates with points at r = 0.80-0.88, and a linear model gives R² of about 0.78. Diminishing returns appear only at the 85-90% talent cut-offs, with no decline [full text] (*PLoS One*, e0290147).

## 3. How synergy forms

### Familiarity over time

The football and basketball evidence agrees on the shape: steep early gains, then a plateau.
- Bransen & Van Haaren find a plateau after about 50 matches.
- Le Coz et al. find that short windows (20 days) carry signal, as do 12-month windows, and that a decay term adds nothing.
- Berman et al. and Sieweke & Zhao find U-shapes or inverted Us in the NBA at team level. Too much tenure may "ossify".
- Pasarakonda et al. find the clearest role of familiarity is *resilience*: familiar teams cope better when disrupted.

How fast synergy decays when a pair splits up is not measured anywhere I found. The Le Coz finding (decay adds nothing) is the closest evidence, and it suggests history does not need to fade fast.

### Complements vs substitutes

This is the most robust mechanism.
- **Shared resources clash.** When players compete for one ball, one shot or one zone, adding a second similar player has diminishing returns. In Maymin this shows up as negative self-synergy for scoring and ball-handling. In Arcidiacono it is the substitution effect in scoring.
- **Rare-event skills compound.** Each extra steal or offensive rebound creates possessions the others can use, and Maymin's self-synergies for steals and offensive rebounds are positive.
- **Hockey.** Chan, Cho & Novati (2012, *Interfaces* 42(2) 131-145) clustered NHL players into types and regressed team results on type mix. Goaltending dominated, and they did not confirm a strong complementarity [abstract and press only].

### Role context: the "Pogba effect"

Whether a player's role and output change with the players around him is directly supported in basketball. In Arcidiacono, Stoudemire with or without Nash and LeBron with Miami are model counterfactuals. In Maymin, free-agent rankings re-order by roster [full text]. In football, Bransen & Van Haaren show pair chemistry varies with partner, Özil being the example. I found **no quantitative study of Pogba** or of role shift between club and country. The examples in circulation are journalism [unverified].

### Communication, language, nationality, personality

- **Bransen & Van Haaren.** Cultural and language features had limited predictive power for chemistry; players "communicate well with their feet" [full text].
- **Haas & Nüesch 2012.** Bundesliga 1999/00-2005/06, 4,284 team observations, Blau nationality-diversity index from 0 to 0.92. The standardised effect on points is β = −0.061 (p = .03), on goal difference −0.051, and on expert rating −0.055. For comparison, home advantage is β 0.195-0.279 [full text] (*International Journal of Human Resource Management*). Diversity is a small negative.
- **Kahane, Longley & Simmons 2013.** NHL 2001/02-2007/08. More Europeans helps, especially if they come from the same country [abstract] (*Review of Economics and Statistics* 95(1) 302-314).
- **Gasparetto 2025.** Six leagues, 2015/16-2020/21. The number of nationalities is not significant, and each foreign player adds about +0.42 percentage points of win percentage [full text] (*PLoS One* e0325025). Its review lists mixed findings:
  - Maderer et al. 2014: negative;
  - Tovar 2019: nonlinear;
  - Addesa 2022: negative in Serie A;
  - Ben-Ner et al. 2017: diversity helps defence and may hurt offence.

  I saw these only as cited by Gasparetto.
- **Personality.** I found no football pair-level personality study with data [not found].

### Spatial proximity and passing style

- **Passing networks (Grund 2012).** EPL, 283,259 passes, 760 matches. Higher passing intensity goes with more goals, and higher centralisation with fewer [secondary sources only] (*Social Networks* 34(4) 682-690). Synergy in football flows along pass links, which are mostly between players in neighbouring zones. That supports weighting role-pair effects by pitch proximity, as issue #7 proposes.
- **Bransen & Van Haaren.** Their chemistry-prediction features include positions and preferred foot. The paper does not report a separate effect size for footedness [full text].

## 4. What synergy is based on

| Basis | Evidence | Strength |
|---|---|---|
| Skill complementarity (rare vs common events) | Maymin's self- and cross-synergies; Arcidiacono's spillovers | **Robust** (basketball; structural models, calibrated) |
| Competing for the ball or shots (substitutes) | Maymin's negative scoring and ball-handling self-synergy; Arcidiacono's substitution effect | **Robust** in basketball; plausible in football by analogy |
| Facilitation (making others better) | Arcidiacono: point guards; spillover is undervalued by pay | **Robust** in basketball |
| Zone overlap and proximity | Passing networks (Grund); pair metrics are concentrated in adjacent roles (Bransen & Van Haaren's top pairs are forward combinations) | **Moderate**, mostly descriptive |
| Familiarity | Steep early gains, plateau around 50 matches; buffering; small or nil once quality is controlled | **Contested** as a main effect; **moderate** for pass-level and resilience effects |
| Passing style | Pass intensity is good and centralisation bad at team level (Grund 2012) | **Weak to moderate** |
| Pace, physicality | No direct pair-level study found | **Not evidenced** |
| Footedness | Used as a feature by Bransen & Van Haaren; no effect size; Carey et al. 2001 on foot preference not read | **Not evidenced** |
| Nationality and language | Small negative (Haas & Nüesch), nil (Gasparetto), same-country positive (Kahane) | **Weak, mixed** |
| Diminishing returns to similar stars | Talent: diminishing but never negative (Gula; Long) | **Robust** for diminishing; the too-much-talent decline is **refuted** |

## 5. Implications for the Wavelength XI synergy model

Constraints from HANDOFF.md and issues #1 and #7:
- the model must be explainable, with every effect logged as a named factor;
- it must work with basic stats only (1992/93 onward: appearances, minutes, goals, assists, position) and also with event data (about 2009/10 onward);
- it must output zone- and transition-level modifiers.

### Recommendations

1. **Make synergy structural, as Maymin does.** Make the chain engine's event probabilities nonlinear in combined skills, for example logistic or probit in the sum of the relevant players' ratings for that zone and transition. Synergy is then just predicted-minus-sum, and it can be logged per factor. Do not fit free pair parameters: Gramacy and Fourie show they are mostly noise or confounding.
2. **Tag each role skill as common-event or rare-event.**
   - Common-event, ball-sharing skills such as finishing, ball-carrying and chance-creating-on-the-ball get a *same-job clash* when stacked in the same zone. This is Maymin's −0.83 self-synergy pattern.
   - Rare-event skills such as ball-winning, pressing, aerial wins and set-piece threat get a *complement* bonus when stacked. This is Maymin's +0.3 pattern.

   This gives the role-pair table in #7 an empirical reason for each cell.
3. **Add a facilitator spillover multiplier, as Arcidiacono does.** A player with high assists-per-goal-involvement, or a creator role, multiplies the output of teammates in adjacent zones. This can be computed in the basic tier from assists and position, and refined in the event tier from xA, progressive passes and VAEP received by teammates. The literature also suggests this is a "hidden value" fans underrate, which makes it good explanation copy.
4. **Weight pair effects by pitch proximity.** Football synergy flows through pass links between adjacent roles (Grund; Bransen & Van Haaren's top pairs). Keep #7's proximity weighting.
5. **Familiarity: small, fast-saturating, mostly protective.**
   - Use a saturating curve on minutes together, e.g. f(m) = 1 − exp(−m/τ) with τ set so the curve is about 90% saturated by about 50 matches. Bransen & Van Haaren's plateau supports this. The exact curve is a design choice: no paper gives a fitted football curve.
   - Start at 0 for cross-era pairs, as #7 already says.
   - Keep the familiarity term small relative to role fit. Its best-evidenced use is reducing the penalty from disruption (Pasarakonda): red cards, injuries, an out-of-position cover player.
   - Don't add an ossification penalty. The inverted-U evidence is NBA-only and contested.
6. **Nationality and language: leave them out, or use them only as a tiny cosmetic tie-breaker.** Effects are small and mixed (Haas & Nüesch β ≈ −0.06; Gasparetto nil).
7. **Talent stacking: diminishing, never negative.** Apply concave aggregation of overall quality, for example via the nonlinearity in recommendation 1. Do not punish a team for having too many stars as such. Punish only same-job and same-zone clashes.
8. **Calibrate the size honestly, then choose deliberately.** Real chemistry is a few percent of lineup value (Maymin), and spillover is comparable to own skill for facilitators (Arcidiacono). HANDOFF's "synergy matters more than ratings" is a game-design choice, not an empirical finding. If the game amplifies synergy, it should still keep the *direction* of each effect true to the evidence, so that explanations stay honest.
9. **Data tiers.**
   - Basic tier: infer roles from position plus goals and assists shares and minutes. Familiarity comes from shared appearances and minutes. Facilitator scores come from assists. All the role-pair and familiarity rules above run on these inputs alone.
   - Event tier: use pair VAEP / JOI-style metrics only to *calibrate and validate* the role-pair table. For example, check that Salah–Firmino, Messi–Suárez and Sané–Sterling come out as complements. Don't use them as a separate synergy source, or event-era players would get synergy that basic-era players can't.
10. **Validation targets.** The JOI top pairs (Salah–Firmino 0.71, Suárez–Messi 0.65, Ibrahimović–Di María 0.60) make good positive controls. Özil after Sánchez is a "partner loss" check. Two ball-dominant finishers in the same zone should come out negative.

## 6. Robust vs weak or contested

**Robust:**
- Spillovers and teammate effects exist and are roster-dependent (Arcidiacono; Maymin).
- Common-event skills stack badly and rare-event skills stack well (Maymin; basketball only).
- There are diminishing returns to talent, without decline (Gula; Long).
- Free pair-specific interaction terms are mostly noise (Gramacy; Kharrat's need for shrinkage).
- Offensive pair chemistry in football is partly predictable from player profiles (Bransen & Van Haaren, about 33% of variance).

**Moderate:**
- Familiarity improves pass-level coordination and buffers disruption (Le Coz; Grund 2016; Pasarakonda).
- The familiarity curve saturates after about 50 matches (Bransen & Van Haaren).
- Prior shared success adds predictive power, but it is confounded with quality (Mukherjee et al.).

**Weak or contested:**
- A familiarity main effect on results net of quality (Le Coz nil; Fourie nil; Berman positive but NBA and older).
- An inverted-U from ossification (Berman; Sieweke & Zhao give U-shapes in a different direction).
- Diversity and nationality effects (mixed).
- The too-much-talent decline (refuted by replications).
- Defensive pair chemistry (JDI not predictable).
- The Pogba effect and role shifts by partner in football (journalism only).
- Synergy decay rates after separation (not measured).

## 7. Not verified

- **Seen only as abstracts or secondary summaries:**
  - Le Coz et al. 2025 (publisher, HAL and ResearchGate were all blocked);
  - Grund 2012 and 2016;
  - Berman, Down & Hill 2002;
  - Sieweke & Zhao 2015;
  - Swaab et al. 2014;
  - Kahane, Longley & Simmons 2013;
  - Chan, Cho & Novati 2012;
  - Groysberg, Lee & Nanda 2008 (the "moved with team shows no decline" claim comes from a secondary review);
  - Hägglund et al. 2013;
  - the diversity papers cited only in Gasparetto's review.
- **Not read:**
  - Mas & Moretti 2009;
  - Gould & Winter 2009 (baseball complements and substitutes);
  - Azoulay et al. 2010 (superstar extinction);
  - Carey et al. 2001 (footedness);
  - the Carron et al. 2002 cohesion meta-analysis;
  - Hvattum 2019 (plus-minus review; I did not check whether it covers interaction terms).
- **Read only in part:** Pasarakonda et al. 2023 (first half of the full text).
- **Weak source:** Fourie 2026 is an unrefereed working paper the author says was AI-assisted. It is used only as supporting evidence for the confounding point, which the econometrics literature makes independently.
- **No quantitative source found:**
  - the Pogba effect;
  - pace and physicality complementarity;
  - footedness pair effects;
  - personality pair effects;
  - synergy decay rates.

## Sources

- Arcidiacono, P., Kinsler, J., & Price, J. (2017). Productivity spillovers in team production: Evidence from professional basketball. *Journal of Labor Economics* 35(1), 191-225. [doi:10.1086/687529](https://doi.org/10.1086/687529). Working paper read at [sole.memberclicks.net/assets/docs/14086.pdf](https://sole.memberclicks.net/assets/docs/14086.pdf).
- Berman, S. L., Down, J., & Hill, C. W. L. (2002). Tacit knowledge as a source of competitive advantage in the National Basketball Association. *Academy of Management Journal* 45(1), 13-31.
- Bransen, L., & Van Haaren, J. (2020). Player chemistry: Striving for a perfectly balanced soccer team. MIT Sloan Sports Analytics Conference. [arXiv:2003.01712](https://arxiv.org/abs/2003.01712).
- Chan, T. C. Y., Cho, J. A., & Novati, D. C. (2012). Quantifying the contribution of NHL player types to team performance. *Interfaces* 42(2), 131-145.
- Fourie, J. (2026). Working paper on co-worker effects among cricket bowlers. Stellenbosch University. Unrefereed.
- Gasparetto, T. (2025). *PLoS One* e0325025.
- Gramacy, R. B., Jensen, S. T., & Taddy, M. (2013). Estimating player contribution in hockey with regularized logistic regression. *JQAS* 9(1), 97-111. [arXiv:1209.5026](https://arxiv.org/abs/1209.5026).
- Groysberg, B., Lee, L.-E., & Nanda, A. (2008). Can they take it with them? The portability of star knowledge workers' performance. *Management Science* 54(7), 1213-1230. [IDEAS](https://ideas.repec.org/a/inm/ormnsc/v54y2008i7p1213-1230.html).
- Grund, T. U. (2012). Network structure and team performance: The case of English Premier League soccer teams. *Social Networks* 34(4), 682-690.
- Grund, T. U. (2016). The relational value of network experience in teams: Evidence from the English Premier League. *American Behavioral Scientist* 60(10), 1260-1280.
- Gula, B., et al. (2021). Never too much: The benefit of talent to team performance in the National Basketball Association. *Psychological Science* 32(2), 301-304.
- Haas, H., & Nüesch, S. (2012). Are multinational teams more successful? *International Journal of Human Resource Management*.
- Hägglund, M., et al. (2013). Injuries affect team performance negatively in professional football. *British Journal of Sports Medicine*. [PubMed 23645832](https://pubmed.ncbi.nlm.nih.gov/23645832/).
- Hvattum, L. M. (2019). A comprehensive review of plus-minus ratings for evaluating individual players in team sports. *IJCSS* 18(1), 1-23. [doi:10.2478/ijcss-2019-0001](https://doi.org/10.2478/ijcss-2019-0001). Not read.
- Kahane, L., Longley, N., & Simmons, R. (2013). The effects of coworker heterogeneity on firm-level output: Assessing the impacts of cultural and language diversity in the National Hockey League. *Review of Economics and Statistics* 95(1), 302-314.
- Kharrat, T., López Peña, J., & McHale, I. (2017/2020). Plus-minus player ratings for soccer. [arXiv:1706.04943](https://arxiv.org/abs/1706.04943).
- Le Coz, C., Carling, C., Houde, C., & Imbach, F. (2025). *Journal of Big Data* 12:157. Abstract only.
- Long, C., Graf, R., & Bilalić, M. (2024). *PLoS One* e0290147.
- Maymin, A., Maymin, P., & Shen, E. (2013). NBA chemistry: Positive and negative synergies in basketball. *International Journal of Computer Science in Sport* 12(2).
- Mukherjee, S., Huang, Y., Neidhardt, J., Uzzi, B., & Contractor, N. (2019). Prior shared success predicts victory in team competitions. *Nature Human Behaviour* 3, 74-81. [doi:10.1038/s41562-018-0460-y](https://doi.org/10.1038/s41562-018-0460-y).
- Pasarakonda, S., et al. (2023/2025). *Group & Organization Management* 50(3), 840-895. [PMC11999323](https://pmc.ncbi.nlm.nih.gov/articles/PMC11999323/).
- Sieweke, J., & Zhao, B. (2015). The impact of team familiarity and team leader experience on team coordination errors: A panel analysis of professional basketball teams. *Journal of Organizational Behavior* 36(3), 382-402.
- Swaab, R. I., Schaerer, M., Anicich, E. M., Ronay, R., & Galinsky, A. D. (2014). The too-much-talent effect. *Psychological Science* 25(8), 1581-1591.
