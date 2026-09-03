<p align="center">
  <img src="brand/logo-256.png" width="150" alt="Scry">
</p>

<h1 align="center">Scry</h1>

<p align="center"><strong>Read the draft before the game is played.</strong></p>

<p align="center"><em>The mark is Summoner&rsquo;s Rift seen through glass &mdash; lane paths in gold, the two<br>
nexuses lit blue and red, and the draft&rsquo;s own radar shape held in the middle.</em></p>

---

**Scry** is a draft analysis tool for League of Legends. Pick ten champions and it breaks the draft down — win probability, lane by lane matchups, minute by minute power curves, objective control, and a game plan for every pick.

**Live: https://captainkar.github.io/Scry/**

One HTML file. No install, no build step, no server, no account.

---

## What it does

Enter a draft and you get eight columns per side:

| Column | What it means |
|---|---|
| **Win Rate** | Real measured win rate for that champion *in that role*, patch 26.17 |
| **Role Fit** | How well the kit suits the slot it's actually in |
| **Impact** | Win-probability points this pick adds over a replacement-level champion in the same slot |
| **Lane Edge** | Points won or lost in the direct lane matchup |
| **Teamfight 5v5** | Contribution to a straight five-versus-five |
| **Draft Power** | Composite standing against the roster |
| **Profile** | Radar of the champion's shape |

Click **GRAPHS** for the full draft read, or click any champion's row for that champion alone.

### GRAPHS — eight pages

- **Timeline** — win probability every minute from 0 to 50, with a fight-pressure ribbon underneath. Hover any minute for expected kills, who wins a 5v5 right then, and what's happening — objective spawns, power spikes, advantage flips
- **Breakdown** — every factor's share of the gap, summing exactly to the headline, plus how *solid* that read is: whether the edge is broad or resting on one factor, whether the factors agree, and how it compares to the model's own margin
- **Matchups** — lane by lane, including when each edge exists rather than just how big it is: average read, volatility, whether it grows or fades, and the minute a lane changes hands
- **Economy** — where each side's gold actually goes, and whether it goes to picks that can spend it: gold aim, farm security, concentration, wasted share, and the minute every pick completes its first three items
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

Six factors feed the headline, and the Breakdown page shows exactly what each contributed:

| Factor | Typical size | What it is |
|---|---|---|
| **Win Rate** | 3.0 pts | measured per-champion, per-role performance |
| **Protection** | 1.1 pts | peel and frontline against the *enemy's* dive threat |
| **Role Fit** | 1.1 pts | kit suited to the slot it was put in |
| **Economy** | 0.9 pts | is the gold aimed at picks that convert it |
| **Combo** | 0.7 pts | how the five picks chain together |
| **Counters** | 0.6 pts | measured lane counter data |

(mean absolute contribution over 4,000 random drafts)

### Economy

Every team gets the same minions, so raw income is not what separates two drafts — **who the gold lands on** is. A team is economically coherent when the picks that convert gold into power most efficiently are also the picks that receive the most of it. A Jinx in the ADC slot sits on a quarter of team income and turns every coin into damage; a Malzahar in the same slot is most of a Malzahar at level 6 with one item, so that quarter is largely wasted.

Two components: **aim** (gold-weighted item dependence) and **security** (can the draft actually collect it — waveclear and sustain). Raw "gold generated" is deliberately excluded: income differences in a real game come from winning fights, which is the thing being predicted, so feeding it back in would be circular.

### Peel and Frontline

Stored per champion but scored as an interaction, because neither is worth anything in isolation — five points of peel matter enormously in front of a Jinx and not at all in front of a Tryndamere. The model asks: how much of this team's damage sits on picks that can't save themselves, is anyone stopping that, and can the enemy actually reach it?

Peel and Frontline are kept as separate ratings on purpose. They correlate only **+0.28** across the roster: Sion is elite frontline and near-zero peel, Braum is the reverse. A single merged "defence" number would hide exactly the gap that decides fights.

The resulting signal is roughly **two thirds measured win rate, one third structural ratings.**

### Data

- 173 champions, patch **26.17**
- Win rates validated against live aggregator data for 65 champion-role pairs: mean difference **+0.10**, sd 0.82, 52 of 65 within one point
- Win rates measured per champion *per role*, from samples of 77K–454K games
- 278 counter relationships across 93 champions
- Champion art from Riot's Data Dragon

---

## Honest limitations

Please read this before trusting a number.

**The model has never been validated against real match outcomes.** No accuracy figure is claimed anywhere in the app, and any that appeared would be invented. Treat it as a tool for *exploring* a draft — seeing where a comp is strong, when its window opens, which lane carries the most risk — not as a predictor of who wins.

**Elite drafts sit near even, and that's correct.** Two well-constructed comps should read close to 50/50. If you're expecting one side to show 70%, the model is disagreeing with you on purpose.

**The subjective ratings are judgement, not measurement.** Scaling, Peel, Frontline and item dependence are assigned from champion class and resource curve, not fitted to outcome data. They are defensible and they discriminate — Scaling now spreads across seven values instead of clustering 73% of the roster on two — but a different analyst would place individual champions differently.

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
| `brand/` | Logo and favicon in SVG, PNG (16&ndash;512px), `.ico`, and the SCRY wordmark lockup |

---

## Disclaimer

Scry is not endorsed by Riot Games and does not reflect the views or opinions of Riot Games or anyone officially involved in producing or managing Riot Games properties. Riot Games and all associated properties are trademarks or registered trademarks of Riot Games, Inc.

Champion art is used under Riot's [Legal Jibber Jabber](https://www.riotgames.com/en/legal) fan-content policy, which permits it on the condition that the project stays **non-commercial**. Please keep it free of ads and paywalls.
