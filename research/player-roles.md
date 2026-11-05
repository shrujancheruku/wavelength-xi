# Player role and archetype models from stats

Research for issue #3 (map #1, MVP Draft mode spec). Researched 2026-11-05.

Question: how do existing analytics models turn player stats into playstyle roles or archetypes, and how do they measure compatibility between players? Which pieces would make Wavelength XI's synergy model believable and explainable?

## Answer in brief

- **Roles are soft, multi-label scores, not one hard label.** The two best-documented models (SciSports' 21 roles and Soccerment's 13 "functions") both give each player a probability or score per role, and both find that most players score high on several roles. Store a role vector per Player-season, not one role.
- **Two ways to build roles, and a game wants the supervised one.** Unsupervised clustering (k-means, UMAP plus Gaussian mixtures) finds clusters you have to name afterwards. Supervised per-role classifiers (SciSports) start from expert-defined, named roles with stated duties, and use interpretable linear models whose weights say *why* a player fits a role. The second gives the game its explanation vocabulary for free.
- **Pair compatibility in published work is measured from outcomes of the pair's joint actions, then predicted from role scores.** Bransen and Van Haaren (SciSports) measure offensive chemistry as the summed value (VAEP) of two-action interactions between the pair, per 90 minutes together. To predict chemistry for pairs who never played together, they regress it on both players' role scores, physical indicators and familiarity. That is exactly the Draft mode's problem (pairs from different clubs and eras).
- **Familiarity is a real, separate effect.** In the same model, matches played together had large predictive weight, rising fast over the first few matches and flattening after about 50. Shared nationality or language barely mattered.
- **No game models playstyle compatibility.** EA FC chemistry is pure club/nation/league links (positional links were removed in FIFA 23). Football Manager has roles, duties and an editorial notion of "complementary role pairs", plus partnership familiarity, but no published compatibility formula. This is the open gap HANDOFF.md already identified.
- **Evidence that output depends on teammates is solid in general, thin for named anecdotes.** Plus-minus models, context-aware event models and the Özil-after-Sánchez chemistry drop all show context effects. I found no quantitative primary source for the "Pogba for France vs Manchester United" effect, only journalism.

## 1. Turning stats into roles

### 1.1 Supervised, expert-named roles: SciSports (Aalbers and Van Haaren, 2018)

Source: B. Aalbers, J. Van Haaren, "Distinguishing Between Roles of Football Players in Play-by-play Match Event Data", MLSA workshop at ECML PKDD 2018, LNCS 11330 (Springer 2019). arXiv: https://arxiv.org/abs/1809.05173

- **Roles:** 21 roles across six positions (goalkeeper, centre-back, full-back, central midfield, winger, forward). Built from a media search and **Football Manager 2018's role list**, refined with SciSports' scouts, who added "mobile striker" and "ball-playing goalkeeper". Related roles are linked in a graph (full-back and wing-back share defensive duties, wing-back and winger share attacking ones).
- **Role definitions are duties, written in plain language.** The five central-midfield roles: Ball-Winning Midfielder (aggressively closes down, simple passing), Holding Midfielder (passively protects the defensive line, dictates tempo), Deep-Lying Playmaker (dictates tempo and creates from deep, build-up over defence), Box-to-Box (positioning both ways, arrives late in the box), Advanced Playmaker (creator between the lines). Each lists reliance on technique, game intelligence, strength, pace or endurance.
- **Features:** 242 base statistics per player-season (e.g. interceptions in the middle zone, passes into the box, long balls received in the final third), each normalised two ways: **per player action** (fair across strong and weak teams) and **per team action** (finds players who dominate the ball, such as playmakers). Plus 31 metrics including passing-network centrality. Standardised, clipped to plus or minus 2 SD, and combined into 181 "key features" by domain knowledge.
- **Per-role "optimal range" transform:** for each role and feature, the 25th-75th percentile of labelled players is the ideal band. A player's value becomes its distance outside that band (0 = ideal). This turns each role into a template: "inside the band on these five stats".
- **Model:** one binary logistic classifier per role (SGD, logistic loss), chosen *because coefficients are interpretable to coaches and scouts*. Labels came from scouts for 356 of 1,910 players (900+ minutes). Negatives include only 25% of players from *connected* roles, so the model learns role differences, not just position differences.
- **Finding that matters for us:** players score high on several roles at once (e.g. top box-to-box midfielders also score high as ball-winners). Advanced playmakers were the exception: high only on their own role.

### 1.2 Unsupervised clusters with soft membership: Soccerment, via a StatsBomb conference paper

Source (secondary for Soccerment, primary for the WSL work): M. Trower, N. Graham, N. Cottrell, Y. Hengster, "Clustering women's football players: identifying functional patterns for performance optimisation", StatsBomb Conference 2023. https://blogarchive.statsbomb.com/uploads/2023/10/Clustering-Womens-Football-Players-Identifying-Functional-Patterns-for-Performance-Optimisation.pdf

As described there, Soccerment's 2022 framework:

- Opta season and event data, about 2,000 outfield players with 1,800+ minutes, averaged over four seasons.
- **Normalisation choice worth copying:** defensive and basic passing stats normalised per touch (so possession share doesn't decide style), other stats per 90.
- **Excluded xG and xA on purpose**, because they measure quality, not style. This is the same split Wavelength XI needs between "ratings" and "playstyle".
- UMAP for dimensionality reduction, then a Gaussian mixture model, giving each player a probability per cluster. Five macro-clusters, sub-clustered into 13 named functions: Ball Stopper, Build-Up Initiator, First Line Breaker, Wide Controller, Wide Creator, Ball Stealer, Build-Up Director, Box-To-Box Raider, Chance Creator, One-To-One Explorer, Mobile Finisher, All-Round Finisher, Target Man.
- "Hybrid" = no cluster above two-thirds probability.
- Team-level finding: top modern teams are over-represented in technical functions and under-represented in Ball Stopper, Wide Controller and Target Man.

I could not reach Soccerment's original 2022 publication; the details above are as reported by Trower et al.

Other clustering work, for completeness: a k-means study of 1,988 top-five-league outfielders on eight per-appearance metrics giving five archetypes ("Data-Driven Tactical Archetypes in European Professional Football", INDECS, DOI 10.7906/indecs.24.4.5; the Hrčak page timed out, so only the search listing was seen). American Soccer Analysis notes that per-90 normalisation stops clusters splitting starters from substitutes (https://americansocceranalysis.com/home/2020/8/3/defining-roles-how-every-player-contributes-to-goals, not read in full).

### 1.3 Spatial style vectors: Player Vectors (Decroos and Davis, 2019)

Source: T. Decroos, J. Davis, "Player Vectors: Characterizing Soccer Players' Playing Style from Match Event Streams", ECML PKDD 2019 (DOI 10.1007/978-3-030-46133-1_34). Read from the authors' slides: https://tomdecroos.github.io/reports/playing-style-wide-v2.pdf

- Style is defined as "a player's preferred area(s) on the field and which actions he performs in each of these locations".
- One heatmap per action type (shot, pass, dribble, cross, tackle, ...), on a 50 x 50 grid, smoothed, flattened and compressed with **non-negative matrix factorisation** to a few components per action type (e.g. 4 for shots). Components are readable ("left flank", "centre"), so the vector stays human-interpretable.
- Useful for "where does he do things", which is exactly what clashes like "both your 8s occupy the same zone" need. Needs event coordinates, which matters for issue #2 (data sources).

### 1.4 Games

- **Football Manager.** Roles plus duties (defend, support, attack) are the player-facing vocabulary, and SciSports built their 21 roles from FM's list, which shows FM's vocabulary is credible to professionals. SI's own guidance says to think of tactics as "combinations of complementary roles", and role behaviour (movement, pressing) is hard-coded into each role ("How to Create Successful Role Partnerships", Wednesday Wisdom, FM20 era: https://footballmanager.com/node/270, page content seen only via search snippet). FM26 splits roles into separate **in-possession and out-of-possession roles** with two formations (official: https://www.footballmanager.com/fm26/features/possession-out-possession-fm26s-new-tactical-evolution, via search summary). FM's role-suitability ratings come from attributes and are not published as a formula. The detailed pair lists (destroyer + creator and so on) are community guides (FM Scout, Passion4FM), not SI.
- **EA FC Ultimate Team.** Chemistry is links only: players in the starting XI sharing club (2/4/7 players = 1/2/3 chem), nation (2/5/8) or league (3/5/8), max 3 per player. Positional links and squad chemistry were removed in FIFA 23, and low chemistry no longer lowers attributes; chemistry only scales the boost from a "chemistry style" consumable (EA, "Pitch Notes: FIFA 23 FUT Chemistry Update": https://www.ea.com/games/battlefield/news/pitch-notes-fifa-23-fut-chemistry-update). Later versions only adjusted special cases (Heroes, managers, special cards). No playstyle element at all.
- **eFootball.** Each player has one "playing style" label (Goal Poacher, Fox in the Box, Box-to-Box, Classic No. 10, ...) tied to compatible positions. I found only fan-site lists, no official Konami documentation, so how team playstyle fit is calculated is unverified.

Takeaway: games either use editorial roles without a compatibility model (FM) or compatibility without roles (EA). None does both, and none explains it.

## 2. Measuring compatibility between players

### 2.1 Player chemistry from joint actions (Bransen and Van Haaren, 2020)

Source: L. Bransen, J. Van Haaren, "Player Chemistry: Striving for a Perfectly Balanced Soccer Team", MIT Sloan Sports Analytics Conference 2020 (best paper, Other Sports). https://arxiv.org/abs/2003.01712. Results appendix: https://github.com/SciSports-Labs/player-chemistry (no licence file on the repo, so treat it as all rights reserved).

- Data: Wyscout events, 106 competitions, 361 seasons since 2015/16, converted to SPADL with the `socceraction` package (MIT licence, https://github.com/ML-KULeuven/socceraction).
- **Joint Offensive Impact (JOI):** an *interaction* is two consecutive on-ball actions (pass, cross, dribble, take-on, shot) by player p then q. Its value is the sum of both actions' VAEP values (VAEP = change in the team's scoring and conceding probability; Decroos et al., KDD 2019). JOI90 = sum over both directions, per 90 minutes the pair shared the pitch.
- **Joint Defensive Impact (JDI):** for each opponent, expected offensive impact (from his earlier matches) minus actual, shared across defender pairs by a **responsibility share** based on distance between positions on a 5 x 5 position grid (e.g. centre-back plus right-back own the opposing left winger). Per 90 together.
- **Predicting unseen pairs:** CatBoost regressors on a pair feature vector: both players' age, position line, height, weight, nationality, region, language, four physical indicators (air duel, ground duel, speed, work rate), **scores on 22 SciSports player roles**, same-nationality/language/region flags, and matches played together. Trained on pairs with 700+ minutes together.
- **Accuracy is modest.** Test RMSE: JOI 0.0446 vs baseline 0.0545; JDI 0.8891 vs baseline 0.8908, i.e. the defensive model barely beats predicting the mean.
- **What drives predicted chemistry:** role scores for Mobile Striker, Deep-Lying Playmaker and Ball-Playing Defender (offence); Holding Midfielder and Ball-Winning Defender (defence). **Matches played together** had a large effect: low for new pairs, rising quickly over a few matches, much weaker past about 50 matches. Cultural features "exhibit only limited predictive power"; the authors conclude complementary styles matter more.
- **Team Builder:** a mixed-integer program picks 11 players maximising the sum of pairwise chemistry, weighted by an offence/defence slider alpha, under formation constraints (1 GK, 3-5 DEF, 3-5 MID, 1-3 FWD). A direct template for a draft "best synergy XI" or AI squad selection.
- Visual: the paper draws chemistry as a lineup graph with green (high) and red (low) edges, which is the "synergy web" HANDOFF.md proposes.

### 2.2 Teamwork value between pairs (Beal, Changder, Norman, Ramchurn, 2020)

Sources: University of Southampton news, 27 Feb 2020 (https://www.ecs.soton.ac.uk/node/9507); R. Beal PhD thesis (https://eprints.soton.ac.uk/473478/1/RB_Final_Thesis_Unsigned.pdf). Full paper not read.

- Values teamwork between pairs from historic interaction chains, and forms teams by maximising overlapping pair values. Reported to predict passes, shots on target and goals, and that higher teamwork makes a win more likely. Venue (listed on dblp as BealCNR20) not confirmed.

### 2.3 Skill-level synergy (basketball): Maymin, Maymin and Shen, 2013

Source: "NBA Chemistry: Positive and Negative Synergies in Basketball", International Journal of Computer Science in Sport 12(2), 2013. Full text not read; findings via abstract and ESPN TrueHoop coverage.

- Splits each player into skill ratings (scoring, ball-handling, rebounding, offence and defence), then estimates synergy **between skills**, not between named players. Examples reported: offensive ball-handling and scoring are positive together; offensive rebounding and scoring negative; rare-event skills (steals) tend to synergise, common ones (defensive rebounds) tend to clash, because two players compete for the same opportunities.
- Implication: a player's value depends on the other players on the floor, and a free agent's value depends on the roster. Team synergy differences were worth up to about six wins.
- This "skill x skill" structure is the cleanest model for an explainable game: a small table of role-pair or trait-pair effects, each with a reason ("two players competing for the same ball").

### 2.4 Passing networks

- J. López Peña, H. Touchette, "A network theory analysis of football strategies", 2012, https://arxiv.org/abs/1206.6904: 2010 World Cup passing networks; centrality (closeness, betweenness) measures a player's importance and the effect of removing him.
- J. M. Buldú et al., "Using network science to analyse football passing networks: dynamics, space, time, and the multilayer nature of the game", Frontiers in Psychology 9:1900, 2018 (CC BY 4.0), https://doi.org/10.3389/fpsyg.2018.01900: player, pitch-zone and player-in-zone networks at micro, meso and macro scales.
- Use for the game: a simulated match produces a passing network, so "who connects with whom" can be shown after the match as evidence for synergy claims, and centrality can flag a "hub" whose removal hurts.

## 3. Evidence that output changes with teammates

- **Plus-minus models.** T. Kharrat, I. McHale, J. López Peña, "Plus-minus player ratings for soccer", European Journal of Operational Research 283(2), 2020, arXiv https://arxiv.org/abs/1706.04943. Rates players by team outcomes (xG, expected points) while on the pitch, adjusting for teammates and opponents. Follow-ups: Hvattum 2020; Bajons and Hornik 2024 (possession-sequence RAPM, https://arxiv.org/abs/2407.17832). Not read in full.
- **Context-dependent performance.** T. Mendes-Neves, L. Meireles, J. Mendes-Moreira, "Estimating Player Performance in Different Contexts Using Fine-tuned Large Events Models", 2024, https://arxiv.org/abs/2402.06815: simulates hypothetical transfers and finds that "contextual analyses reveal narrower gaps in performance within specific team frameworks".
- **Partner loss.** Bransen and Van Haaren (above): Özil's chemistry "rapidly started declining" after Alexis Sánchez left Arsenal in 2018. Their intro uses Carroll vs Suárez at Liverpool (2011) as a fit example.
- **Pogba, France vs Manchester United.** Only journalism (Sky Sports, Goal, ESPN, Planet Football): deeper free role beside Kanté (ball-winner) and Matuidi (shuttler who covered when Pogba advanced) for France, vs a less defined role at United. I found no season-level quantitative comparison. Usable as a design story ("a destroyer and a shuttler free the creator"), not as evidence.

## 4. What makes a synergy model believable and explainable

Pieces to take into issues #6 (profile shape) and #7 (synergy model):

1. **A role vector, not a role label.** Store a score (0-1) per role per Player-season, as both SciSports and Soccerment do. Show the top one or two as the player's label, but compute fit from the vector. Hybrids are then natural.
2. **Expert-named roles defined by duties, with an in-possession and out-of-possession side.** FM's vocabulary is trusted by professionals (SciSports borrowed it) and FM26 now splits IP and OOP roles. Explanations like "your press broke because your 6 and 8 are both deep-lying" are statements about OOP duties.
3. **Interpretable role models.** Per-role linear or logistic scores over a few normalised stats, or percentile "optimal bands" (SciSports), so each role score can say which stats earned it. Generated Dynasty players can then be given the same profile by sampling stats, or role scores directly.
4. **Normalise style per touch or per action, keep quality separate.** Soccerment left out xG and xA for style. Style decides fit; ratings decide how well a player executes. This matches "synergy weighted above ratings".
5. **Pair synergy as a small, readable table of role-pair (or trait-pair) effects, each with a mechanism.** Following Maymin et al.: complements (ball-winner + playmaker), clashes where two players compete for the same zone or job (two deep-lying 8s, two target men), and coverage gaps (two attacking full-backs with no holding midfielder). Each cell carries a one-line reason, which is the explanation.
6. **Zone overlap as a second clash signal.** Player-vector-style heatmaps (Decroos and Davis) give "same zone, same actions" overlap, if the data source has coordinates.
7. **Familiarity as its own term.** Seen strongly in Bransen and Van Haaren (steep over the first few matches, flat after about 50). In Draft, cross-era players start at zero and gain it during the season, which gives the season an arc and gives Dynasty a reason to keep a core together. Real teammates from the same club-season could start with familiarity.
8. **Weight pairs by pitch proximity.** JDI's responsibility share (position-grid distance) is a simple way to say which pairs matter: CB-FB and the two 8s, not the GK and striker.
9. **Calibrate against real chemistry where possible.** Published pairs (Salah-Firmino 2017/18 UCL, Messi-Suárez 2015/16, Sané-Sterling 2018/19 PL) can be sanity checks for the hand-written table. If the event data allows, JOI90 between real teammates can tune weights. Expect noisy data: even SciSports' defensive chemistry model barely beat the mean.
10. **Team selection as optimisation.** Bransen's MIP (max pairwise chemistry under formation constraints, with an attack/defence slider) is a ready-made approach for AI clubs' lineups and for a "suggested XI".

## Not verified

- Full texts not read: Maymin et al. 2013, Beal et al. 2020, Kharrat et al. 2020, Player Vectors paper (slides only), INDECS archetypes paper, Soccerment 2022 original.
- FM pages were seen only through search summaries; FM's internal role-suitability formula is not public.
- No official Konami documentation for eFootball playing styles found.
- No quantitative primary source for the Pogba France vs United effect.
- Licences: SciSports player-chemistry repo has no licence file; Wyscout, Opta and StatsBomb data used in these papers are commercial (see issue #2). `socceraction` is MIT.
