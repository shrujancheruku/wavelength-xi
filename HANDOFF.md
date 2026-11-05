# Wavelength XI: handoff

Status as of 2026-11-05: concept, market research and planning. No code yet.

**Planning now lives on the wayfinder map: [Wayfinder: MVP Draft mode spec](https://github.com/shrujancheruku/wavelength-xi/issues/1).** It holds the destination, the standing decisions, the open tickets and the research. The concept, name and competitor notes below are still current; where the map and this file disagree, the map wins. Terms are defined in `GLOSSARY.md`.

## Concept

A football squad builder where **synergy between players** is the core mechanic. Advanced stats are used to profile each player's playstyle. Compatibility between playstyles gives bonuses for good fits and penalties for clashes.

Two modes:

- **Draft** (the MVP): build a squad from historic player-seasons and play one Premier League season in a chosen year, taking the place of a newly promoted side.
- **Dynasty** (later): manage one team across seasons, with generated players coming up through the lower leagues.

The handoff's earlier names for these were Roulette and Perpetual.

## Name

**Wavelength XI**, from "on the same wavelength", meaning two players who understand each other. It names the synergy mechanic directly, and the "XI" makes a clash with other products unlikely.

Domains checked on 2026-11-05 against the registries' own records (whois and RDAP). Prices and premium status were not checked.

| Domain | Status |
|---|---|
| wavelengthxi.com | free |
| wavelengthxi.app | free |
| wavelengthxi.io | free |
| wavelengthxi.gg | free |
| wavelengthxi.football | free |
| wavelengthfc.com, wavelength11.com | free (could redirect to the main site) |
| playwavelength.com | taken |

Plan: register wavelengthxi.com and point .app or .gg at it. None are registered yet.

**Trademark risk:** *Wavelength* is a well-known party game by CMYK Games (board game 2019, free mobile app 2022). It is a different genre, but in the same broad trademark category (games). Do a trademark search before going commercial.

Names considered and rejected: Linkup (linkup.com belongs to another company), Gelled (.com taken), Telepathy XI, OneTwo XI. Avoid "Chemistry", because it is EA FC's term for its own mechanic.

## Competitors

Scope: web and mobile draft games. EA FC, UFL and eFootball were deliberately left out as a different category.

Almost all of them use the same template:

1. Spin a slot machine for a club or nation plus an era.
2. Pick one player from that squad and put him in a slot.
3. Repeat 11 times, with one or two rerolls.
4. A simulated season produces a record or grade, which you share before playing again.

Common extras: difficulty levels that change reroll counts, a blind mode that hides ratings, a choice of formation, and a daily challenge.

| Game | What sets it apart |
|---|---|
| 38-0 (web + app) | Market leader. Async duels shared by link (same spins, record to beat), daily leaderboard, spin-first or position-first drafting, match-by-match reveal, "weak link" verdict, World Cup and England editions, live multiplayer in the app |
| 20-0 / 82-0 (NFL and NBA versions from the same studio) | Deepest design. Published position weights, era-adjusted ratings, a random elite season for each star, ranked ladder with divisions, one guaranteed legend era per run, many rule variants |
| MY-11 / Drafted XI | Drafts specific player *seasons*. Has a multi-season career mode with transfers and cups (the closest to the perpetual mode). Claims "team chemistry" without explaining it |
| 38-0-0 | Players can only go in positions they really play. Head-to-head beta, extra leagues, a watch-an-ad re-spin |
| WorldCupXI | Non-linear rating curve, players vote on rating changes, all-time top-50 leaderboard |
| 7-0 | World Cup squads, tactical style setting (defensive, balanced or attacking), multiplayer with friends |
| Kings of the World | XI dropped into the real 2026 World Cup draw. Copy is now out of date |
| Eleven DNA | In development. Badges for how the team plays ("Fortress Defence", "Wing Merchants") |
| FootballGenius Draft XI | Trivia twist: type a player's name. Score is the sum of market values, with no simulation |
| Unbeatable Football | Pools are specific team-seasons, with a daily streak |
| Golazo, Draftinho, 38, Ultimate 11, UndefeatedXI | App-store versions of the same template (only their listings were read) |

## Gaps no competitor solves

1. **No real synergy.** Players are scored one at a time. At most a game checks position fit and balance between the lines of the team.
2. **Drafting comes down to "take the highest rating."** With ratings shown, the draft is solved. With them hidden, it is a memory test. No pick changes the value of the next one.
3. **The simulation is a black box.** No game explains why a season went the way it did, so players can't learn the system and get better at it.
4. **The run ends at the draft.** There are no decisions during the season.
5. **Little carries over between runs.** Only MY-11 (career mode) and Eleven DNA (Hall of Fame) have any persistence.
6. **Shallow data.** Ratings are editorial or crowdsourced overalls. Nobody uses advanced stats.

## Product direction

The goal is to lead the category with a tight, exciting game loop built on synergy:

- **Every pick changes the value of the others.** A lower-rated player who connects with your midfield can beat the highest-rated name on the wheel, which puts a real decision into every spin.
- **A synergy web you can see.** Links light up or clash after each pick, giving small payoffs throughout the draft instead of one at the end.
- **Results you can explain.** For example, "Your press broke down because your 6 and your 8 are both deep-lying."
- **Roulette feeds the perpetual mode.** Synergies discovered or players collected in roulette runs carry into the managed team.

Features players now expect: daily runs and duels shared by link (from 38-0), and a ranked ladder with published weights (from 20-0).

## Next steps

Work through the [wayfinder map](https://github.com/shrujancheruku/wavelength-xi/issues/1) by running `/wayfinder` on it. The next ticket is **Player-season profile shape**.

Not on the map, because it's out of scope for the MVP spec: register wavelengthxi.com (and .app or .gg), run a trademark search, and check image rights for real player names before going commercial. Candidate marketing quotes are in `docs/marketing/quotes.md`, but they aren't in use yet and some attributions are unverified.

## Answered questions

- **Platform:** Expo (iOS, Android and web from one codebase), released on web first.
- **How far back do advanced stats go?** Basic stats go back to 1992/93. Opta-derived per-player stats go back to about 2009/10, and xG to 2014/15. So player profiles are either **measured** or **inferred** (see the data-sources research linked from the map).
- **Real or generated players in Dynasty?** Generated players, who start in the lower leagues.
- **Hobby or commercial?** A hobby for now, but no choices that rule out going commercial.

## Sources

- 38-0: https://38-0-game.com/
- 20-0: https://www.20-0.com/how-to-play
- 82-0: https://www.82-0-challenge.com/about
- Drafted XI: https://peerpush.com/p/drafted-xi
- MY-11: https://my-11.com/
- 38-0-0: https://38-0-0-staging.pages.dev/
- WorldCupXI: https://worldcupxi.pages.dev/
- 7-0: https://7a0.org/en
- Kings of the World: https://kingsoftheworld.pages.dev/
- Eleven DNA: https://twoey22gmailcom.itch.io/eleven-dna
- FootballGenius Draft XI: https://footballgenius.pages.dev/game/draft/rules/
- Unbeatable Football: https://www.producthunt.com/products/unbeatable-football/makers
- Wavelength party game: https://www.wargamer.com/wavelength/free-app
