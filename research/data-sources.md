# Historical player data sources and coverage

Research for issue #2. Researched 2026-11-05.

**Question:** which sources can supply per-player, per-season data for every Premier League squad (about 500 player-seasons a year) from 1992/93 to today, and what is each source's licence status for hobby use vs commercial use?

**Short answer:** basic stats (appearances, starts, minutes, goals, assists, cards) exist for every player-season back to 1992/93. Advanced or event data exists only from about 2009/10 on sites that forbid reuse, and from 2014/15 for free xG. **No free source of advanced player data for the Premier League is licensed for commercial use**, apart from one full season (Wyscout 2017/18, CC BY 4.0). Plan for a two-tier profile: *measured* (advanced data) where it exists and *inferred* (basic stats, positions and editorial tags) everywhere else, built on a commercially clean backbone. Budget for a paid licence (Opta/Stats Perform, Hudl StatsBomb or similar) before going commercial.

Not legal advice. Licence readings are from each provider's own published terms where those could be read. Gaps are flagged in [Could not verify](#could-not-verify).

## Coverage by era

"Commercial OK" means the provider's own terms permit commercial reuse without a separate deal.

| Era | What exists per player-season | Best free source (hobby) | Commercially clean? |
|---|---|---|---|
| 1992/93 to 2002/03 | Appearances, starts, sub appearances, minutes, goals, assists (assists compiled back to 1992/93), cards, broad position (GK/DF/MF/FW). Per-match lineups exist in some form. No event data, xG, heatmaps or formations. | FBref basic tables | No free licensed source. Facts only via Wikidata (identity, clubs, positions) or self-compiled |
| 2003/04 | As above, plus StatsBomb event data for **Arsenal's 38 league matches only** (both teams' events in those matches, with lineups, positions and formations) | FBref + StatsBomb open data | No (StatsBomb open data is non-commercial) |
| 2004/05 to 2008/09 | Basic only | FBref | No |
| 2009/10 to 2013/14 | Basic, plus Opta-derived per-player stats and match ratings on WhoScored, Sofascore and similar *(start season unverified)* | FBref; WhoScored/Sofascore (terms forbid scraping) | No |
| 2014/15 | Adds Understat xG, xA, xGChain, xGBuildup, shots and key passes per player-season | Understat | No terms found; site offline at time of research |
| 2015/16 | Adds **StatsBomb full-season event data** (all 380 matches): every pass, carry, pressure and duel with locations, so heatmaps, average positions, lineups and formations can be derived | StatsBomb open data | No (non-commercial, no redistribution) |
| 2016/17 | Understat; FPL data begins (archived from 2016/17) | Understat, FPL archive | No |
| 2017/18 | Adds **Wyscout public event dataset** (all 380 matches, events with locations, players with roles) | Wyscout/Pappalardo dataset | **Yes: CC BY 4.0** (attribution required) |
| 2018/19 to 2024/25 | Understat; FPL (ICT index; xG fields added in later seasons); Sofascore/FotMob heatmaps and ratings. FBref's Opta advanced tables existed but were **deleted in January 2026** | Understat, FPL archive | No |
| 2025/26 to now | As above; FPL live API exposes tackles, recoveries, CBI, defensive contribution, xG, xA, xGC per player | FPL API, Understat | No |

**Where advanced stats stop:** before 2014/15 there is no free xG; before about 2009/10 there are no per-player event-derived stats at all (apart from Arsenal 2003/04). For 1992/93 to 2008/09, roughly 8,500 player-seasons, only basic stats and positions exist.

## Sources

### StatsBomb (Hudl) open data

- **Fields:** full event data (passes, carries, pressures, duels, shots with freeze frames) with x/y locations; lineups with positions played and minutes; starting formations and tactical shifts. Heatmaps and average positions can be derived.
- **Premier League coverage:** 2015/16 (380 matches) and 2003/04 (38 matches, Arsenal only). Checked against `data/competitions.json` and the per-season match files in the repo. [1]
- **Access:** JSON on GitHub (now `hudl/open-data`, formerly `statsbomb/open-data`). [1]
- **Licence:** StatsBomb Public Data User Agreement (last updated 8 Sept 2023). Users may not "distribute, reproduce, sell or in any way provide the data to any external or third party" (1.2.1) or "commercially exploit the data or any analysis derived from the use of the Service" (1.2.2). Publications must credit StatsBomb with its logo (1.4). [2]
  - **Hobby:** fine for private analysis. Shipping it inside a public app, even a free one, arguably counts as providing it to third parties, so **even hobby release is doubtful** unless only derived, non-reversible outputs ship.
  - **Commercial:** no. A paid Hudl StatsBomb licence is needed.

### Wyscout public dataset (Pappalardo et al., 2019)

- **Fields:** events with locations and tags, matches with lineups and substitutions, players with role, teams, coaches, referees, plus a "PlayeRank" score.
- **Coverage:** 2017/18 for the top five leagues (Premier League: 380 matches, 11 Aug 2017 to 13 May 2018, checked in `matches_England.json`), plus World Cup 2018 and Euro 2016. [3]
- **Access:** figshare download. [3]
- **Licence:** **CC BY 4.0** on figshare (checked through the figshare API on the Events item). Cite Pappalardo et al., *Scientific Data* 6:236 (2019). [3]
  - **Hobby and commercial:** both allowed with attribution. It is the only commercially clean event data found, but it covers a single season.

### FBref (Sports Reference)

- **Fields today:** basic per-player season and match data (appearances, starts, minutes, goals, assists, cards, broad position). Assists cover every Premier League match back to 1992/93 (Dec 2023 blog post). [4] Player pages show minutes for 1992/93.
- **The 2026 change** (the ticket says 2025): on 20 Jan 2026 Sports Reference said its advanced-data provider (widely reported to be Opta/Stats Perform) terminated access and required the data to be deleted immediately. xG, progressive actions, shot-creating actions and the other advanced tables are gone. Any future advanced data "will certainly have a dramatically smaller scale". [5] Before this, FBref had moved from StatsBomb to Opta data in late 2022. [6]
- **Access:** HTML only, with no API. Sports Reference's bot policy limits FBref to about 10 requests a minute and locks out offenders. [7]
- **Licence:** Sports Reference says most of its data comes from third parties whose licences bar a download or API. Its data-use page warns against building tools or sites on scraped data, creating a competing database, and training AI without permission. [7][8]
  - **Hobby:** a private, rate-limited scrape for personal use is a grey area at best.
  - **Commercial:** no.

### Understat

- **Fields:** per-player season games, minutes, goals, assists, shots, key passes, xG, xA, npxG, xGChain, xGBuildup; shot maps with locations.
- **Coverage:** Premier League from 2014/15 (third-party listings agree; not confirmed first-hand).
- **Access:** JSON embedded in the HTML pages; no official API.
- **Licence:** no published terms found. On 2026-11-05 understat.com served an error or maintenance page ("We will come back soon").
  - **Hobby:** usable if it comes back, with no explicit permission.
  - **Commercial:** no. There is no licence, and its xG model is proprietary.

### WhoScored

- **Fields:** Opta-derived per-player stats, match ratings, positions, formations, and event data embedded in match centre pages.
- **Coverage:** about 2009/10 onward for the Premier League *(unverified)*.
- **Licence:** terms page returned 403. Third-party library docs (socceraction) say scraping it violates WhoScored's terms. The underlying data is Opta's.
  - **Hobby:** against the terms.
  - **Commercial:** no.

### Sofascore / FotMob

- **Fields:** per-player ratings, heatmaps, average positions, lineups and formations, Opta-style stats.
- **Coverage:** historical depth for the Premier League not verified.
- **FotMob licence:** own terms (fotmob.com/tos.txt, dated 22 Oct 2026): use of FotMob data "for any purpose" without express written consent is prohibited; "automatic services (robots, spiders, etc.) ... systematic, regular, or bulk retrieval of data, is forbidden"; the software licence covers "personal, non-commercial use". [9] worldfootballR and soccerdata reportedly dropped their FotMob scrapers after takedown requests.
- **Sofascore licence:** own terms not read. Third-party tools say Sofascore does not license API access because of its data-provider agreements.
  - **Hobby and commercial:** no for both.

### Transfermarkt

- **Fields:** squads, appearances, minutes, goals, cards, positions (main and secondary), market values, transfers, lineups.
- **Coverage:** deep historical squad and appearance data. Whether minutes are complete back to 1992/93 is unverified.
- **Licence:** site refused automated fetch. Secondary sources quote its terms as banning bots, scraping, AI training and text and data mining (reserved under German law), and say reproduction "even in part" needs prior written consent. [10]
- **dcaribou/transfermarkt-datasets** (Kaggle/GitHub): 12 tables, 1.89M appearances, labelled **CC0**. [11] The CC0 label is the scraper author's and cannot grant rights Transfermarkt holds. Treat it as Transfermarkt data.
  - **Hobby:** grey.
  - **Commercial:** no.

### Fantasy Premier League (FPL)

- **Fields (live API, checked):** 100+ per-player fields including minutes, starts, goals, assists, clean sheets, saves, bonus, BPS, ICT index, tackles, recoveries, clearances/blocks/interceptions, defensive contribution, xG, xA, xGI, xGC, set-piece order, `opta_code`. [12]
- **Coverage:** current season only. The community archive vaastav/Fantasy-Premier-League holds 2016/17 to 2026/27 (no stated licence). [13]
- **Licence:** Premier League website terms allow only private, personal use; prohibit commercial use; and bar reproducing or re-utilising content, "including ... creating a database (electronic or otherwise) that includes material downloaded". [14] The FPL-specific terms page did not render.
  - **Hobby:** personal use only.
  - **Commercial:** no.

### EA Sports FC / FIFA ratings (sofifa, Kaggle)

- **Fields:** overall plus about 35 attributes (pace, passing, vision, tackling and so on), positions, work rates.
- **Coverage:** sofifa reportedly covers FIFA 07 onward *(unverified)*. Kaggle "complete player dataset" uploads cover recent editions. No 1992/93 to 2005/06 coverage in these databases.
- **Licence:** the ratings are EA's content. Since FC 24 they are built with Stats Perform data. [15] No licence from EA or sofifa allows reuse. Using them also invites comparison with EA's "Chemistry" (see HANDOFF.md).
  - **Hobby:** risky if published.
  - **Commercial:** no.

### Commercially clean backbones

- **Wikidata:** structured data is **CC0**. [16] It covers player identity, date of birth, nationality, positions, and club spells with dates, with cross-IDs to Transfermarkt and other databases. Good for the Player entity and squad membership. Season stats are thin and patchy.
- **Football-data.co.uk:** match results back to 1993/94 and team match stats from about 2000/01. No player data. Terms not read (HTTP 429).
- **Wikipedia club-season articles:** squad appearance and goal tables for many seasons, **CC BY-SA** (share-alike would apply to a redistributed dataset).

### Paid licences (the commercial path)

- **Opta / Stats Perform:** official Premier League statistics partner through Football DataCo. It sells historical data through Opta APIs, with custom pricing. Its archive depth for the Premier League is unverified (sales enquiry). [17]
- **Hudl StatsBomb:** commercial event data (360 freeze frames from about 2020+). Pricing on request.
- **API-Football, Sportmonks and similar:** cheap ($19+/month according to third parties), with per-season coverage flags for lineups and player stats. Their historical Premier League depth and rights chain are unverified.

## Recommended source stack

1. **Backbone (commercially clean):** Wikidata for Player identity, positions and club spells. Self-compile per-season basic stats (appearances, starts, minutes, goals, assists, cards) as facts, cross-checked against FBref, Wikipedia and Transfermarkt by hand or at low volume. In the UK and EU the risk is database right (substantial extraction from one database), not copyright in the facts. Sourcing from several places and recording provenance per field lowers it.
2. **Profile calibration (hobby only, never shipped raw):** StatsBomb 2015/16, Wyscout 2017/18 (shippable), Understat 2014/15+ and FPL 2016/17+. Use them to *learn* how basic stats and positions map to playstyle archetypes, then apply that mapping to every era. Ship only the game's own derived profiles. Keep source and derived layers separate so a source can be swapped out.
3. **Editorial layer:** hand-written playstyle tags for notable players, especially before 2009. This is the game's own IP, and it fills the eras without data.
4. **Before going commercial:** replace the hobby-only calibration sources with a paid licence (Opta/Stats Perform is the obvious one for the Premier League, Hudl StatsBomb the alternative), or re-derive the mapping from Wyscout 2017/18 alone. Separately, real player names and likenesses raise image-rights questions (EA licenses these through FIFPRO). This is out of scope here, but it matters commercially.
5. **Avoid entirely:** EA/sofifa ratings, WhoScored, Sofascore, FotMob (all explicitly or effectively prohibited).

## Implications for other tickets

- **#3 Archetypes and #6 Player-season profile shape:** profiles must carry a *provenance/confidence* field (measured vs inferred vs editorial). Pre-2009/10 player-seasons (about 8,500) will be inferred from basic stats, broad position and tags only. Synergy cannot depend on fields such as progressive passes, pressures or heatmaps unless they can be inferred.
- **#7 Synergy model:** design rules around attributes every era can supply (role or archetype, position, minutes share, goal and assist output), with advanced data refining them rather than being required.
- **#8 Tactics model and AI clubs:** per-match formations and average positions are free only for 2015/16 and 2017/18 (and Arsenal 2003/04). For most seasons, the "plausible tactics" of the 19 AI clubs must be editorial or inferred from lineups, not measured.
- **Runtime architecture:** shipped player data must be the game's derived dataset, never raw third-party files (StatsBomb, FBref and FPL terms all bar redistribution).

## Could not verify

- Sports Reference terms of use, data-use page, bot policy and the Jan 2026 blog post returned HTTP 403. Their wording comes from search-engine extracts of those pages. FBref now reportedly credits "Data Sports Group" for its football data (unconfirmed).
- Understat terms and exact coverage: the site was offline, so coverage comes from third-party listings.
- WhoScored, Sofascore and Transfermarkt terms: pages were blocked, so readings come from secondary sources.
- The season each of WhoScored, Sofascore and FotMob starts in for the Premier League.
- sofifa's earliest edition. Whether FBref and Transfermarkt minutes and per-match lineups are complete for 1992/93 to 1990s seasons.
- The FPL-specific terms page (JavaScript-rendered). The season FPL added xG fields.
- Opta, StatsBomb and API-Football historical depth and prices (sales enquiries).
- Football-data.co.uk terms (HTTP 429).

## References

1. hudl/open-data `data/competitions.json` and `data/matches/2/27.json`, `44.json` — https://github.com/hudl/open-data
2. StatsBomb Public Data User Agreement (LICENSE.pdf) — https://github.com/hudl/open-data/blob/master/LICENSE.pdf
3. Soccer match event dataset (figshare collection 4415000; Events item licence CC BY 4.0) — https://figshare.com/collections/Soccer_match_event_dataset/4415000 ; paper https://www.nature.com/articles/s41597-019-0247-7
4. Sports Reference blog, Dec 2023, Premier League assists back to 1992/93 — https://www.sports-reference.com/blog/2023/12/coverage-of-match-by-match-premier-league-assists-greatly-expanded/
5. Sports Reference blog, "FBref & Stathead Data Update", Jan 2026 — https://www.sports-reference.com/blog/2026/01/fbref-stathead-data-update/
6. Sports Reference blog, Nov 2022, Opta conversion — https://www.sports-reference.com/blog/2022/11/fbref-shot-level-xg-now-on-match-reports-for-20-competitions/
7. Sports Reference bot traffic policy — https://www.sports-reference.com/bot-traffic.html
8. Sports Reference "SR and Data Use" — https://www.sports-reference.com/data_use.html ; terms https://www.sports-reference.com/termsofuse.html
9. FotMob terms of service — https://www.fotmob.com/tos.txt
10. Transfermarkt legal notice as quoted on r-package-devel (2018) — https://stat.ethz.ch/pipermail/r-package-devel/2018q1/002345.html
11. dcaribou/transfermarkt-datasets — https://github.com/dcaribou/transfermarkt-datasets
12. FPL API — https://fantasy.premierleague.com/api/bootstrap-static/
13. vaastav/Fantasy-Premier-League — https://github.com/vaastav/Fantasy-Premier-League
14. Premier League website terms and conditions — https://www.premierleague.com/en/terms-and-conditions
15. Stats Perform, ratings data for EA FC 24 — https://www.statsperform.com/news/stats-perform-to-supply-ea-with-real-time-player-data-for-the-upcoming-fc24/
16. Wikidata licensing — https://www.wikidata.org/wiki/Wikidata:Licensing
17. Stats Perform, Opta historical archives — https://www.statsperform.com/resource/power-of-historical-data-unlock-insights-opta-archives/
