# CS32-Final-SSJ-
2026 FIFA World Cup Simulator

**CS 32 Final Project**

Our project simulates the real 2026 FIFA World Cup, the tournament co-hosted by Canada, Mexico, and the United States in summer 2026.

It uses:

- The real group-stage draw from the FIFA Final Draw
- Real FIFA World Rankings as of April 1, 2026, so France is the top-ranked team, followed by Spain, Argentina, England, Portugal, and Brazil in the top 6
- Real team data from the football-data.org API, including live squad rosters

The program simulates the entire tournament — group stage, Round of 32, Round of 16, Quarterfinals, Semifinals, and Final — to predict a champion.

Each match is simulated using a weighted probability algorithm that accounts for:

- Offense and defense ratings derived from each team's FIFA ranking
- Host-nation home advantage for Canada, Mexico, and the USA (default 8% rating boost)
- Star player injuries that reduce a team's effective rating (default 8% penalty)
- Adjustments based on real squad data from the football-data.org API

Higher-ranked teams are significantly favored to win, but upsets are possible, just like in real soccer.

In response to George's feedback, we also built a parameter-sweep experiment system in `experiments.py` that runs the simulator hundreds of times across different parameter values and produces matplotlib graphs showing how the simulation responds.

## Project files

- `simulator.py` — the simulator itself. Run this directly to play out a single tournament with full text output.
- `experiments.py` — parameter sweep experiments that run the simulator hundreds of times under different conditions and produce matplotlib graphs.
- `experiment_exponent.png`, `experiment_home_advantage.png`, `experiment_injury.png` — the graphs produced by `experiments.py`.
- `README.md` — this file.

We deliberately separated the simulator and the experiments so the simulator stays clean and can be run on its own, while the experiments live in a dedicated file that imports from `simulator`.

## The 2026 World Cup format

For the first time in history, the World Cup features 48 teams (expanded from 32):

- 12 groups of 4 teams play a round-robin group stage
- Top 2 teams from each group advance (24 teams)
- Plus, the 8 best third-place teams advance (32 teams total)
- Knockout bracket: Round of 32 → Round of 16 → Quarterfinals → Semifinals → Final

## The real 2026 groups

Group A: Mexico, South Korea, South Africa, Czechia
Group B: Canada, Switzerland, Qatar, Bosnia-Herzegovina
Group C: Brazil, Morocco, Scotland, Haiti
Group D: USA, Paraguay, Australia, Turkiye
Group E: Germany, Ecuador, Ivory Coast, Curacao
Group F: Netherlands, Japan, Tunisia, Sweden
Group G: Belgium, Iran, Egypt, New Zealand
Group H: Spain, Uruguay, Saudi Arabia, Cape Verde
Group I: France, Senegal, Norway, Iraq
Group J: Argentina, Austria, Algeria, Jordan
Group K: Portugal, Colombia, Uzbekistan, DR Congo
Group L: England, Croatia, Panama, Ghana

## How FIFA rankings translate to team ratings

The program uses a function `ranking_to_rating()` that converts each team's FIFA ranking into a numeric rating:

- Rank #1 (France) → 95
- Rank #10 (Germany) → 86
- Rank #30 (Canada) → 73

This rating is then split into offense and defense with small random variation. In a match, we raise each team's rating to the 4th power before calculating win probability, which makes stronger teams clearly favored (a top-10 team beats a bottom-20 team about 80% of the time) while still allowing realistic upsets.

## How the API data is used

The `fetch_team_info()` function pulls live team data from football-data.org, including each national team's current squad list. When the simulator runs with a valid API key, each team gets a small rating modifier of up to ±1.5 points based on squad size — the idea being that deeper squads have more options if a star player is injured. The effect is intentionally small so that real FIFA rankings remain the dominant signal.

If the API is unreachable or no key is provided, the program falls back to the hard-coded rankings and runs identically.

## How to run it

You'll need Python 3, plus a couple of packages:

'pip install requests matplotlib'

**To run a single tournament with full output:**

'python simulator.py'

**To run the parameter-sweep experiments and generate graphs:**

python experiments.py            # run all three experiments
python experiments.py exponent   # just the rating-exponent sweep
python experiments.py home       # just the home-advantage sweep
python experiments.py injury     # just the injury-probability sweep


The experiments take a few minutes to run because each one simulates 100 full tournaments at every parameter value. The graphs are saved as `.png` files in the same directory.

**Setting up the API key (recommended):**

1. Register for a free API key at https://www.football-data.org/client/register 
2. Open `simulator.py` and find the line `API_KEY = "API_HERE"` near the top
3. Replace `"API_HERE"` with your key in quotes, e.g. `API_KEY = "abc123def456..."`

The program runs fine without a key — the real 2026 draw and FIFA rankings are built into the code, and the API data is only used as a small refinement. If the key is missing or invalid, you'll see an error message and the simulation will fall back to the hard-coded data automatically.

## The parameter sweep experiments

Following our TF's suggestion, we built three experiments that vary one parameter at a time and graph how the simulation responds. Each experiment runs 100 full tournaments at every parameter value, then plots the mean (line) and ±1 standard deviation (shaded band).

**Experiment 1: rating exponent vs. number of upsets.** 
We sweep the exponent in our probability calculation from 0 to 6. At exponent 0, every match is a coin flip, so we see ~50 upsets per tournament. As the exponent grows, the favorite wins more often and the upset count drops to about 25 by exponent 6. Our default of 4 sits where soccer-realistic upset rates live (about 28 upsets per tournament). This experiment came out as a clean, smooth, monotonically decreasing curve — strong evidence that the rating exponent is the dominant lever for tuning realism.

**Experiment 2: home advantage vs. host championship rate.** 
We sweep the host-nation rating multiplier from 1.00 (no advantage) to 1.20 (extreme advantage). The y-axis is the probability that a host (Canada, Mexico, or USA) wins the whole tournament. With no home advantage, hosts win about 5% of the time; at our default 1.08 they win about 9%; and at an extreme 1.20 multiplier they win about 32%. The shaded band is wide because the metric is binary (either a host won or didn't), but the upward trend is clear and confirms that the home-advantage parameter is meaningfully biasing outcomes.

**Experiment 3: injury probability vs. champion FIFA ranking.** 
We sweep the per-team injury probability from 0% to 50% and plot the average FIFA rank of the champion. We expected that higher injury rates would produce weaker champions on average, because top teams would more often be hobbled. Our data showed something different: the average champion rank stayed roughly flat (between about 6.4 and 8.4) across the entire range, well within the noise band. **This is a null result, and we think it's an honest and informative one.** The reason it makes sense is that injuries in our model are distributed uniformly at random — every team is equally likely to lose a star, so the effect cancels out at the tournament level. Strong teams still beat weak teams on average. To see a real effect, we would need to bias injuries toward top-ranked teams, which we left as future work.

## New concepts used (beyond what we covered in class)

- **The `requests` library** — for making HTTP requests to the football-data.org API. Learned from https://requests.readthedocs.io/.
- **Parsing JSON API responses** — converting JSON to Python dictionaries and pulling out fields like `squad`.
- **Error handling with `try`/`except`** — so the program runs even if the API is unavailable or returns an error code.
- **Weighted random choices (`random.choices`)** — for realistic soccer scores.
- **Multi-key sorting with lambda functions** — for ranking teams by points, then goal difference, then goals scored (real FIFA rules).
- **Mapping real-world rankings to numeric ratings** — we wrote a piecewise function to convert FIFA ranks into ratings that produce realistic match outcomes.
- **`matplotlib` for plotting** — for the parameter-sweep graphs. We used `plt.plot` for the mean line and `plt.fill_between` for the ±1 std shaded band. Learned from the matplotlib quickstart at https://matplotlib.org/stable/users/explain/quick_start.html.
- **The `statistics` module** — for `mean` and `stdev` across each batch of tournament runs.
- **A configuration class (`SimConfig`)** — we refactored all the "magic numbers" (rating exponent, home advantage, injury probability, etc.) into a single class so `experiments.py` can sweep them without modifying the simulator. This builds on the class-based design pattern from chapter 11.

## Team members

- Andreas Savva
- Sophian Lovato
- Jason Broome

## Credits and external resources (AI tool usage)

We used Claude (Anthropic) as a coding assistant during development.

**Original (FP Design) version:**

- *Debugging help:* Claude helped us fix indentation errors in our initial `simulate_match` function.
- *Design suggestions:* After mentor feedback, Claude helped us restructure our code into clear functions, suggested the API fallback design, and suggested using weighted random choices for realistic scores.
- *README structure:* Claude helped us outline the original README.

**Final submission revisions (in response to TF feedback):**

After our TF suggested we add parameter-sweep experiments with matplotlib graphs and that we make use of the API data we were already fetching, we used Claude to help with the refactor:

- *Refactoring into a config class:* We described our TF's suggestion to Claude and asked how to make our parameters tunable without breaking the simulator. Claude proposed extracting the parameters into a `SimConfig` class and threading it through the existing functions; we reviewed and adopted that structure.
- *Parameter-sweep helper:* Claude wrote the initial version of the `sweep_parameter` and `plot_sweep` helper functions in `experiments.py`. We chose which parameters and metrics to study, decided on the ranges, and reviewed the implementation. The shaded ±1 std region was specifically suggested by our TF.
- *API integration:* Claude suggested using the squad-size field from the API as a small rating modifier. We chose the magnitude (±1.5 points) so it wouldn't dominate the FIFA rankings, and tested it end-to-end with our own API key.
- *Interpreting the null result:* When our injury experiment came back with a flat line, Claude helped us reason about why the effect canceled out (uniform-random injuries don't preferentially weaken favorites) and helped us articulate that null result honestly in this README rather than overclaiming.

We did NOT use AI for the core probability algorithm — that is our own original design from FP Design. We also wrote the tournament bracket and rating-conversion logic ourselves.

## Data sources

- **Group-stage draw:** Official FIFA Final Draw, referenced via NBC Sports: https://www.nbcsports.com/soccer/news/2026-world-cup-groups-confirmed-full-draw-groups-details
- **FIFA rankings:** Official FIFA World Ranking via ESPN and FIFA.com: https://www.espn.com/soccer/story/_/id/46664763/fifa-mens-top-50-world-rankings
- **Live team data:** football-data.org API: https://www.football-data.org/

## Testing

1. **100-tournament simulation (original FP Design sanity check)** — During the FP Design milestone, we ran the full tournament 100 times and counted champions. Results matched expectations: top-6 FIFA-ranked teams won about 65% of all tournaments combined, Mexico and USA each won around 5% despite being ranked #15-#16 (showing host advantage is working), and the lowest-ranked teams (Haiti, Curacao, New Zealand) never won. After the final-submission refactor, the parameter-sweep experiments in `experiments.py` provide a more rigorous version of this same kind of sanity check across many parameter values.
2. **Full tournament structure** — 48 teams reduce correctly to 32 → 16 → 8 → 4 → 2 → 1. No teams are duplicated or lost.
3. **Host advantage check** — Hosts Canada, Mexico, and USA advance from the group stage significantly more often than equivalent-ranked non-host teams. Confirmed quantitatively in Experiment 2.
4. **API integration** — Tested with a valid key (loads 48 teams successfully) and without a key (falls back to hard-coded data and runs identically).
5. **Upset realism** — Upsets happen but the stronger team wins more often than not. The exponent-sweep experiment shows this trend quantitatively (Experiment 1).
6. **Tiebreaker correctness** — Groups with tied teams are sorted by goal difference, then goals scored.
7. **Third-place selection** — Exactly 8 third-place teams advance, correctly ranked across all 12 groups.
8. **Parameter-sweep graphs** — All three experiments produce graphs at 100 runs per value. Two show clear monotonic trends (exponent and home advantage); one shows a null result that we honestly describe rather than hide.

## What's next

- Bias the injury probability so it preferentially affects top-ranked teams. This would likely show the effect that Experiment 3 failed to find.
- Use real match statistics from the API (recent results, head-to-head history) to compute offense/defense ratings more precisely instead of deriving them only from FIFA rank.
- Build a proper bracket structure for the knockout round (1A vs 2B, etc.) instead of randomized pairings.
- Run two-parameter sweeps (e.g., a heatmap of exponent × injury probability vs. champion strength).
- Build a visual bracket display instead of text output.
tput.

