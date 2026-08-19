# LoL Matchup Analyzer

A draft analysis tool for League of Legends. Pick ten champions and it breaks the draft down — win probability, lane by lane matchups, minute by minute power curves, objective control, and a game plan for every pick.

**Live: https://captainkar.github.io/lol-analyzer/**

One HTML file. No install, no build step, no server, no account.

---

## What it does

Enter a draft and you get eight columns per side:

| Column | What it means |
|---|---|
| **Win Rate** | Real measured win rate for that champion *in that role*, patch 26.16 |
| **Role Fit** | How well the kit suits the slot it's actually in |
| **Impact** | Win-probability points this pick adds over a replacement-level champion in the same slot |
| **Lane Edge** | Points won or lost in the direct lane matchup |
| **Teamfight 5v5** | Contribution to a straight five-versus-five |
| **Draft Power** | Composite standing against the roster |
| **Profile** | Radar of the champion's shape |

Click **GRAPHS** for the full draft read, or click any champion's row for that champion alone.

### GRAPHS — seven pages

- **Timeline** — win probability every minute from 0 to 50, with a fight-pressure ribbon underneath. Hover any minute for expected kills, who wins a 5v5 right then, and what's happening — objective spawns, power spikes, advantage flips
- **Breakdown** — every factor's share of the gap, summing exactly to the headline, plus how *solid* that read is: whether the edge is broad or resting on one factor, whether the factors agree, and how it compares to the model's own margin
- **Matchups** — lane by lane, including when each edge exists rather than just how big it is: average read, volatility, whether it grows or fades, and the minute a lane changes hands
- **Profile** — eight-axis team radar with live hover, next to every champion's own power curve
- **Objectives** — all seven drakes and what each is worth to each side, plus win probability at every neutral spawn
- **Game Plan** — what kind of draft each side is and how it wins: archetype fit, control budget, damage share
- **Advisor** — win conditions per side, power windows minute by minute, and ban targets

### Per champion — four pages

- **Power Curve** — your curve against your lane opponent, with a compare picker for any enemy champion
- **Game Plan** — what to do in each stretch of the game, plus trading windows showing exactly when the lane is yours
- **Role & Kit** — share of team output, full kit with roster percentiles and the gap to the role average
- **Progress** — six stages with your power and lane read across each

---

## How the model works

Win probability is built on **additive log-odds**. Each pick's measured win rate is converted to log-odds, adjusted for role fit, teamfight contribution and measured lane counters, then summed across the five slots. The difference between the sides goes through a logistic function.

Two things matter about this:

**It's parameter-free.** There's no fitted constant setting how big the gaps are — the scale comes out of the real dispersion in champion win rates. An earlier version had a tuning knob calibrated against random drafts, which produced wildly overconfident numbers because random drafts include nonsense that no real game contains.

**Inputs are normalised before they're weighted.** Combining a 46–54 win rate with a 1–10 subjective rating by simple addition lets whichever has the wider raw range dominate regardless of intent. Everything is converted to standard deviations first, so the stated weights are the actual weights.

The resulting signal is roughly **two thirds measured win rate, one third subjective ratings.**

### Data

- 173 champions, patch **26.16**
- Win rates measured per champion *per role*, from samples of 77K–454K games
- 278 counter relationships across 93 champions
- Champion art from Riot's Data Dragon

---

## Honest limitations

Please read this before trusting a number.

**The model has never been validated against real match outcomes.** No accuracy figure is claimed anywhere in the app, and any that appeared would be invented. Treat it as a tool for *exploring* a draft — seeing where a comp is strong, when its window opens, which lane carries the most risk — not as a predictor of who wins.

**Elite drafts sit near even, and that's correct.** Two well-constructed comps should read close to 50/50. If you're expecting one side to show 70%, the model is disagreeing with you on purpose.

**The Scaling rating doesn't discriminate well.** 127 of 173 champions sit at 7 or 8, and only one is below 6. As a result some lane bullies that fall off hard — Draven, Talon, Lucian, Jayce, Rengar — are modelled as peaking very late. This affects power curves, the timeline and phase analysis. It's a known issue, not a mystery.

**Camille is listed as Support.** She's primarily a Top laner; this distorts her role fit.

**Fight pressure is a model of game rhythm, not a prediction.** It's built from objective timers, fixed level and item spikes, and how much the two drafts can commit to a fight. The per-minute kill figures are scaled so the total matches a realistic combined-kills-per-minute for the whole game — the aggregate is anchored, individual minutes are indicative.

---

## Running it

Open the live link, or download `index.html` and open it in any browser. It works offline apart from champion portraits, which load from Riot's image CDN.

Your picks are saved in your own browser and never leave your device. There's no analytics, no accounts, no server.

### Editing

Everything is in `index.html` — CSS in `<style>`, then three scripts: the champion database, the engine, and the UI. To change it, edit the file and commit; GitHub Pages redeploys in under a minute.

| File | |
|---|---|
| `index.html` | The entire application |
| `og.png` | Link preview image |
| `DEPLOY.md` | Deployment notes |

---

## Disclaimer

LoL Matchup Analyzer is not endorsed by Riot Games and does not reflect the views or opinions of Riot Games or anyone officially involved in producing or managing Riot Games properties. Riot Games and all associated properties are trademarks or registered trademarks of Riot Games, Inc.

Champion art is used under Riot's [Legal Jibber Jabber](https://www.riotgames.com/en/legal) fan-content policy, which permits it on the condition that the project stays **non-commercial**. Please keep it free of ads and paywalls.
