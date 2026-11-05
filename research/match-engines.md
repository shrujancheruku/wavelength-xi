# Football match simulation engine approaches

Research for [#4](https://github.com/shrujancheruku/wavelength-xi/issues/4) (feeds [#10](https://github.com/shrujancheruku/wavelength-xi/issues/10), the match engine decision). Researched 2026-11-05.

## The question

How do existing games and models simulate a football match? For each one: granularity, how attributes feed probabilities, how tactics change outcomes, cost (can it simulate 380 matches on a phone?), and whether outcomes can be traced back to causes.

Our constraints (from map #1): a live ticking match with events on a pitch and in-match tweaks; an always-available "sim to next match"; Expo, web first; synergy must outweigh ratings; every result must be explainable.

## Answer in one paragraph

Use a **zone-based possession-chain engine**: a Markov-style state machine over pitch zones in which each step either keeps the ball and moves it to another zone, loses it, or ends in a shot. Hattrick, Bygfoot and Sarah Rudd's Markov model all work at this level. Every transition probability is a **sum of named factor terms** (player-season skill, synergy link, tactic, fatigue) passed through a logistic or ratio function, and each step writes those terms to a trace. The live view and "sim to next match" run **the same loop**: the live view just paces it and draws it. Bygfoot and Hattrick Organizer both work this way. A toy version in JS simulates a 380-match season in about 15–20 ms on a laptop. Agent-based physics engines (FM, Google Research Football, open-football, footballSimulationEngine) look better but cost far more, and their causes come out of emergent behaviour, so they are hard to explain. Poisson score models are cheap and their factors are easy to read, but they produce no events. Their best use is as a calibration target and for pre-match predictions.

## Comparison

| Approach | Granularity | Attributes → probabilities | Tactics | Cost (380 matches on a phone) | Traceable? |
|---|---|---|---|---|---|
| **Football Manager** (SI) | Agent-based: every player and official decides every 0.25 s "slice" [1] | Each player picks the action he judges best "based on their attributes"; shot selection and spatial awareness tied to attributes [1]. Player Preferred Moves bias actions [3] | Team and player instructions change positioning and decisions; PPMs link to instructions [1][3] | Heavy. FM itself uses a cheaper "quick match engine" for less important matches (see [4]) | Poor. Results emerge from thousands of micro-decisions; FM gives stats and analysis, not causes |
| **Championship Manager** (text era, 1992–) | Unknown internals. UI was a clock, 3 possession meters and one-line commentary [5][6] | Unknown | Unknown | Ran on early-90s home computers, so cheap | Not exposed. Notable design idea: text lets players picture the match "in their heads" [6] |
| **Hattrick** (official) | A fixed number of attacks (chances) per match spread over 90 minutes | Lineup turns into 7 sector ratings: midfield, 3 defence, 3 attack. Midfield decides who gets each attack; the attack rating in the chosen sector against the opposing defence rating decides whether it scores [7][8] | Attack middle/wings shift which sector attacks; counter-attack turns failed opponent attacks into chances; pressing cancels attacks for both sides; attitude changes possession [7][8] | Trivial | High. Every chance has a sector and a rating comparison; users reason about it all the time |
| **Hattrick Organizer** (HO) predictor, LGPL-3.0 [9] | Per-minute `advance()` for a timeline, plus a fast `simulate()` that runs 10 attack slots | `possession = mid / (mid + oppMid)`; sector chance `= atk / (atk + oppDef)` mapped through a sigmoid-like "effectiveness" curve [9] | Tactic type and level change the zone split (middle 40% ± level·3), pressing cancels chances, counter-attack generator [9] | Trivial; runs thousands of predictions | High: each `Action` records minute, side, area and result |
| **Bygfoot** (GPL-2.0) [10] | Sequence of "live game units", each with minute, possession and area (defend/midfield/attack) | Team values per area (defend/midfield/attack) pitted as a ratio raised to an exponent: `(own·luck / opp·luck)^exp` [10] | `style` scales attack up and defence down; `boost` raises fouls and costs money [10] | Trivial | Medium: unit stream says what happened, not why |
| **footballSimulationEngine** (MIT, JS, npm) [11] | Agent-based iterations on a continuous pitch (ball has x, y, z); user picks iterations per half | 10 skills (passing, shooting, tackling, saving, agility, strength, penalty_taking, perception, jumping, control) [11] | Team `intent` (attack/defend) only | Unbenchmarked; one call per iteration for 22 players | Low: an iteration log of strings |
| **Google Research Football** (Apache-2.0, C++) [12] | Physics engine, 3000 steps per full match [13] | Built-in AI plus RL agents | Via agent behaviour | About 25 M steps per day on a 6-core machine [12], so about 8,300 matches a day, or roughly 1 hour per season on a desktop. Not runnable in Expo | Low |
| **open-football** (Apache-2.0, Rust) [14] | Agent-based physics engine (ball flight, contests, player states) with seeded, reproducible results and a diagnostics module [14] | Per-player decisions, environment modifiers (pitch, weather) that "SHALL be consumed by at least one live decision" [14] | Tactics spec | Heavy enough that its README advertises multi-core runs [14] | Medium: diagnostics exist, but for debugging, not for players |
| **Markov possession chain** (Rudd 2011) [15] | 39 states: 30 zone × defensive-pressure states, 7 set pieces, 2 absorbing (Goal, End of Possession) | Transition probabilities estimated from event data (EPL 2010/11, about 800 actions a match); matrix powers converge on P(goal) for each state (n=20) [15] | Compares teams' transition patterns; values each action by the change in P(goal) [15] | Trivial | High: every action has a P(goal) delta credited to a player |
| **Expected threat (xT)** (Singh 2019) [16] | 16×12 zones; actions are move or shoot | `xT(z) = s(z)·g(z) + m(z)·Σ T(z→z')·xT(z')`, solved by iterating; 4–5 iterations were enough [16] | None: no opponent or context terms [16] | Trivial | High per action (xT gain), but describes only how a team behaves, not a contest between two teams |
| **Markov match model with tactics** (Hirotsu & Wright 2002/2003; Hirotsu et al. 2024) [17] | Markov process over match states; the 2024 version uses 9 pitch zones and a two-team zero-sum Markov game | Offensive and defensive strengths estimated by maximum likelihood from EPL data [17] | Built for the best timing of substitutions and tactic/formation changes [17] | Trivial | High |
| **Poisson score models** (Dixon & Coles 1997) [18] | One draw per match (goals per side), or a per-minute goal hazard | Log-linear: home goal rate `λ = exp(home + attack_home − defence_away)`, fitted by maximum likelihood on EPL 1992–95 data [18] | Only if added as covariates | Trivial (microseconds per match) | High for the factors (additive in log space), but no events to show |

Numbers in brackets are sources, listed at the end.

## What the precedents teach

### 1. Abstract zone engines are the sweet spot for this game

Hattrick has run a management game for two decades on an engine whose whole state is "which sector is attacking, and how do its ratings compare". Players learn it, argue about it and plan around it, which is the explainability we want. Bygfoot is the same at finer grain: a possession state plus one of three areas, advanced unit by unit. Rudd's model and xT show that a **zone grid with transition probabilities** is also how analysts describe real football. That means the engine's vocabulary (progressing from zone A to zone B, turning the ball over under pressure, a chance created from a zone) maps straight onto real event data and onto the language of explanations.

### 2. Live and fast paths should be one loop

- Bygfoot runs a single loop (`live_game_create_unit` → `live_game_evaluate_unit`) until the match ends or the user pauses or makes a change. On a change it recomputes team values and resumes [10]. Watching a match only adds presentation on top of the loop.
- HO keeps two paths over the same ratings: a per-minute `advance()` that produces a timeline, and a quicker `simulate()` with 10 attack slots [9]. That is a warning: two paths can drift apart statistically.
- FM gives the other warning. Its quick match engine is a separate model for unwatched and less important matches. In view-only leagues, results are reportedly driven mostly by club reputation [4]. That is fine for background leagues, but in our game every one of the 380 matches involves the user's season, so a separate fast model would break the promise that synergy decides results.

Recommendation: one seeded engine. "Sim to next match" runs the other 9 fixtures of each round, and your own matches when you skip them, through the same function with presentation turned off.

### 3. Make every probability a sum of named terms

Hattrick-style ratio and logistic comparisons are easy to decompose. If `logit(p_keep) = Σ wᵢ·fᵢ` over named factors such as "6 and 8 both deep-lying: −0.4" or "opponent high press: −0.3", the engine can log for each step the factor contributions that pushed the outcome. Explanations then come from aggregating the log ("you lost the ball 14 times in zone C4; 60% of the push came from the double-pivot clash") rather than from guessing afterwards. Poisson models have the same property in log space [18], which makes them good for **pre-match predictions** ("expected goals 1.4 vs 1.1, mainly because of X") and for calibration.

### 4. Tactics fit naturally as modifiers on zones and transitions

Every precedent turns tactics into probability modifiers:

- Hattrick: attack middle/wings changes the zone distribution; pressing cancels attacks for both sides; counter-attacks turn the opponent's failed attacks into chances, and the wiki says they need an inferior midfield [8][9].
- Bygfoot: style moves weight from defence to attack [10].
- Hirotsu & Wright: tactic and substitution changes are explicit Markov state changes, so their timing can be optimised [17].

That fits our in-match tweaks: a change at minute 60 swaps the factor table from the next step onward, and the trace records it.

### 5. Agent-based engines don't suit this project

They give the best pitch visuals, but:

- they are expensive (GRF takes about an hour per season on a desktop [12][13]);
- the hard parts are AI tuning and physics (FM has refined its engine for decades and its lead says it will never be "finished" [3]);
- causes are emergent, so "why did we lose?" has no clean answer.

The live screen (#5) can still show dots: the zone engine emits events with a zone and a player, and the renderer animates plausible positions between events. That is presentation, not simulation.

## Cost estimate

A throwaway benchmark (not kept) ran a possession-chain engine in Node. It used 6×3 zones, one action every 6 match-seconds (900 actions per match), seeded RNG, and per-action factor traces. Results on an Apple M1 Pro:

- without traces: 3,800 matches in 181 ms, about 18 ms per 380-match season;
- with traces: 3,800 matches in 154 ms, about 15 ms per season. The two are equal within noise.

Hermes on phones has no JIT and is slower than V8 on a desktop. Even with an assumed 10–50× slowdown (not measured), a full season stays under about 1 s, and a round of 10 matches is well under 100 ms. **Not verified on a device.**

Trace size matters more than CPU: 900 trace entries × 380 matches is about 340k entries per season. Keep full traces only for the user's matches. For other matches, keep aggregated factor totals.

## Implications for other tickets

- **#10 Match engine model**:
  - Start from "zone possession chain, one action per few match-seconds, named-factor logits, single seeded loop".
  - Decide the zone grid. Rudd used 30 zone × pressure states and Hirotsu used 9 zones; around 6×3 is enough to say "your left half-space".
  - Decide the action set: move, carry, cross, shot, foul, set piece.
  - Calibrate to real EPL per-match rates of shots, goals and possession splits, using event data from #2.
- **#7 Synergy model**: synergy has to produce zone- and transition-level modifiers, not just one team number. For example, the 6/8 pairing changes `p_keep` and `p_progress` from midfield zones. Each modifier needs a stable id and a label for the trace.
- **#8 Tactics model**: express each lever as a modifier on zone distribution or transition probabilities: pressing height raises turnovers in the opponent's zones but costs fatigue (Hattrick pressing drains stamina [8]), and width shifts the zone mix. Counters then fall out of how modifiers interact.
- **#6 Player-season profile**: the engine needs skills that map to transitions (retention under pressure, progressive passing, carrying, crossing, finishing, aerial, tackling or interception, keeping). Event data from #2 gives rates per zone.
- **#5 Live match screen**: events come with zone, player and factor tags. Any dot movement is interpolated. A commentary ticker works well with this model (the CM precedent [6]).
- **Runtime architecture** (not yet specified): a seeded, pure, deterministic engine in shared TS can run on the client. A replay is seed plus inputs plus mid-match changes, so saved runs stay small.
- **Pre-match screen**: a Poisson or closed-form Markov estimate over the same factor tables can give the prediction and its confidence cheaply.

## Licences of reusable code

- MIT: footballSimulationEngine [11], socceraction (xT implementation).
- Apache-2.0: GRF [12], open-football [14].
- LGPL-3.0: HO [9].
- GPL-2.0: Bygfoot [10]. Read it for ideas only; copying its code would force GPL on the app, which closes off a commercial release.

None of these engines should be adopted as-is. The ideas are what transfer.

## Not verified

- **FM internals beyond the official FM21 article.** The quick match engine and how view-only leagues resolve come from the FM2019 manual (via search snippet; the page returned 403) and a community forum thread (403). Treat them as reported, not confirmed.
- **Hattrick official pages** (hattrick.org help, wiki.hattrick.org) sit behind a Cloudflare challenge. The claims above come from search snippets of those pages and from HO's community reimplementation [9], which is an approximation of the real engine, not the engine itself. The exact number of chances per Hattrick match is not confirmed: HO uses 10 slots, and forums say about 10.
- **Championship Manager text-era internals.** No technical source found. Only the UI and design philosophy are documented [5][6].
- **Dixon–Coles low-score correction and time weighting.** These are widely attributed to the paper, but only the abstract was read [18].
- **On-device performance.** Benchmarked on a desktop only; the Hermes slowdown factor is assumed.

## Sources

1. Football Manager, "Match Engine AI in FM21" (official): https://footballmanager.com/node/404
2. Google Research Football scenario config, `game_duration = 3000`: https://github.com/google-research/football/blob/master/gfootball/scenarios/11_vs_11_stochastic.py
3. FM Scout, Miles Jacobson interviews Paul Collyer (match engine lead), 2009: https://www.fmscout.com/i-631-Miles-interviews-game-guru-Paul-C.html
4. FM2019 manual, "Advanced Game Setup" (quick match engine vs full detail; read via search snippet): https://community.sports-interactive.com/manual/football-manager-2019/enhancing-your-experience-further; SI community thread on view-only leagues (via search snippet): https://community.sigames.com/topic/422150-playable-vs-view-only-leagues/
5. Wikipedia, Championship Manager (video game): https://en.wikipedia.org/wiki/Championship_Manager_(video_game)
6. Game Developer, "The ingredient that turned Football Manager into a massive hit" (Collyer brothers quotes): https://www.gamedeveloper.com/design/the-ingredient-that-turned-i-football-manager-i-into-a-massive-hit
7. Hattrick, "Match: The basics" (official; via search snippet): https://hattrick.org/Help/Rules/MatchBasics.aspx
8. Hattrick wiki: Counter-attacks, Pressing, The ABC of Tactics (via search snippets): https://wiki.hattrick.org/wiki/Counter-attacks, https://wiki.hattrick.org/wiki/Pressing, https://wiki.hattrick.org/wiki/The_ABC_of_Tactics
9. Hattrick Organizer source, `core/prediction/engine` (`BaseActionGenerator.java`, `ActionGenerator.java`, `MatchPredictionManager.java`), LGPL-3.0: https://github.com/ho-dev/HattrickOrganizer/tree/master/src/main/java/core/prediction/engine
10. Bygfoot source, `src/live_game.c` and `src/game.c` (GPL-2.0 mirror of the SourceForge repo): https://github.com/tstellar/bygfoot/blob/build-improvements/src/live_game.c
11. footballSimulationEngine README v5.0.0 (MIT): https://cdn.jsdelivr.net/npm/footballsimulationengine@5.0.0/readme.md; repo: https://github.com/GallagherAiden/footballSimulationEngine
12. Google Research blog, "Introducing Google Research Football" (2019): https://research.google/blog/introducing-google-research-football-a-novel-reinforcement-learning-environment/; paper: https://arxiv.org/abs/1907.11180
13. See [2].
14. open-football (Apache-2.0), README and `openspec/specs/core/match/engine/*`: https://github.com/ZOXEXIVO/open-football
15. Rudd, S. (2011), "A Framework for Tactical Analysis and Individual Offensive Production Assessment in Soccer Using Markov Chains", NESSIS 2011 slides: https://nessis.org/nessis11/rudd.pdf
16. Singh, K. (2019), "Introducing Expected Threat (xT)": https://karun.in/blog/expected-threat.html; socceraction xT docs (MIT): https://socceraction.readthedocs.io/en/latest/documentation/valuing_actions/xT.html
17. Hirotsu, N. & Wright, M. (2002), "Using a Markov process model of an association football match to determine the optimal timing of substitution and tactical decisions", JORS 53, 88–96: https://eprints.lancs.ac.uk/id/eprint/43683; Hirotsu & Wright (2003), JORS 54; Markov game extension, IMA J. Management Mathematics (2024). Abstracts and index records only.
18. Dixon, M. J. & Coles, S. G. (1997), "Modelling association football scores and inefficiencies in the football betting market", JRSS Series C 46(2), 265–280: https://eprints.lancs.ac.uk/id/eprint/19492
