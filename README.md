# CS32-Final-SSJ-
2026 FIFA World Cup Simulator
CS 32 Final Project
What this project does - This program simulates the real 2026 FIFA World Cup, the tournament co-hosted by Canada, Mexico, and the United States in summer 2026.
It uses:
•	The real group-stage draw from the FIFA Final Draw on December 5, 2025
•	Real FIFA World Rankings as of April 1, 2026, so France is the top-ranked team, followed by Spain, Argentina, England, Portugal, and Brazil in the top 6
•	Real team data from the football-data.org API
The program then simulates the entire tournament, from group stage, Round of 32, Round of 16, Quarterfinals, Semifinals, and Final, to predict a champion.
Each match is simulated using a weighted probability algorithm that accounts for:
•	Offense and defense ratings derived from each team's FIFA ranking
•	Host-nation home advantage for Canada, Mexico, and the USA (8% rating boost)
•	Star player injuries that reduce a team's effective rating (8% penalty)
Higher-ranked teams are significantly favored to win, but upsets are possible, just like in real soccer.
The 2026 World Cup format
For the first time in history, the World Cup features 48 teams (expanded from 32):
•	12 groups of 4 teams play a round-robin group stage
•	Top 2 teams from each group advance (24 teams)
•	Plus, the 8 best third-place teams advance (32 teams total)
•	Knockout bracket: Round of 32 → Round of 16 → Quarterfinals → Semifinals → Final
The real 2026 groups
Group A: Mexico, South Korea, South Africa, Czechia
Group B: Canada, Switzerland, Qatar, Bosnia-Herzegovina
Group C: Brazil, Morocco, Scotland, Haiti
Group D: USA, Paraguay, Australia, Turkey
Group E: Germany, Ecuador, Ivory Coast, Curacao
Group F: Netherlands, Japan, Tunisia, Sweden
Group G: Belgium, Iran, Egypt, New Zealand
Group H: Spain, Uruguay, Saudi Arabia, Cape Verde
Group I: France, Senegal, Norway, Iraq
Group J: Argentina, Austria, Algeria, Jordan
Group K: Portugal, Colombia, Uzbekistan, DR Congo
Group L: England, Croatia, Panama, Ghana
How FIFA rankings translate to team ratings
The program uses a function ranking_to_rating() that converts each team's FIFA ranking into a numeric rating:
•	Rank #1 (France) → 95
•	Rank #10 (Germany) → 86
•	Rank #30 (Canada) → 73
This rating is then split into offense and defense with small random variation. In a match, we raise each team's rating to the 4th power before calculating win probability, which makes stronger teams clearly favored (a top 10 team beats a bottom-20 team 80% of the time) while still allowing realistic upsets.
How to run it
•	Make sure you have Python 3 installed.
•	Install the requests library: pip install requests
•	(Optional) Get a free API key at https://www.football-data.org/client/register and paste it into the API_KEY variable near the top of the file.
•	Run the program
The program runs fine without an API key, as the real 2026 draw and FIFA rankings are built into the code.

New concepts used (beyond what we covered in class)
•	The requests library - For making HTTP requests to the football-data.org API. Learned from https://requests.readthedocs.io/.
•	Parsing JSON API responses - Converting JSON to Python dictionaries.
•	Error handling with try/except - So the program runs even if the API is unavailable.
•	Weighted random choices (random.choices) - For realistic soccer scores.
•	Multi-key sorting with lambda functions - For ranking teams by points, then goal difference, then goals scored (real FIFA rules).
•	Mapping real-world rankings to numeric ratings - We wrote a piecewise function to convert FIFA ranks into ratings that produce realistic match outcomes.
Team members
•	Andreas Savva
•	Sophian Lovato
•	Jason Broome

Credits and external resources
AI tool usage
We used Claude (Anthropic) as a coding assistant during development:
•	Debugging help: Claude helped us fix indentation errors in our initial simulate_match function.
•	Design suggestions: After mentor feedback, Claude helped us restructure our code into clear functions, suggested the API fallback design, and suggested using weighted random choices for realistic scores.
•	README structure: Claude helped us outline this README.
We did NOT use AI for the core probability algorithm - that is our own original design from FP Design. We also wrote the tournament bracket and rating-conversion logic ourselves.

Data sources
•	Group-stage draw: Official FIFA Final Draw
Groups referenced via NBC Sports: https://www.nbcsports.com/soccer/news/2026-world-cup-groups-confirmed-full-draw-groups-details
•	FIFA rankings: Official FIFA World Ranking 
via ESPN and FIFA.com: https://www.espn.com/soccer/story/_/id/46664763/fifa-mens-top-50-world-rankings


Testing
We tested our code in these ways:
1.	100-tournament simulation - We ran the full tournament 100 times and counted champions. Results matched expectations:
o	Top 6 FIFA-ranked teams (Argentina, France, Portugal, Brazil, Spain, England) won about 65% of all tournaments combined.
o	Mexico and USA each won around 5% despite being ranked #15-#16, showing the host advantage is working.
o	The lowest-ranked teams (Haiti, Curacao, New Zealand) never won, which is realistic.
2.	Full tournament structure - 48 teams reduce correctly to 32 → 16 → 8 → 4 → 2 → 1. No teams duplicated or lost.
3.	Host advantage check - Hosts Canada, Mexico, and USA advance from the group stage significantly more often than equivalent-ranked non-host teams.
4.	API fallback - Tested with no API key and no internet. The program switches to hard-coded data and runs correctly.
5.	Upset realism - Upsets happen (Qatar beating Switzerland, USA beating Paraguay) but the stronger team wins more often than not.
6.	Tiebreaker correctness - Groups with tied teams are sorted by goal difference, then goals scored (same as real FIFA rules).
7.	Third-place selection - Exactly 8 third-place teams advance, correctly ranked across all 12 groups.

What's next
•	Use real match statistics to compute offense/defense ratings more precisely (e.g., weight recent World Cup qualifiers more heavily).
•	Pull real injury data from the API instead of simulating it randomly.
•	Run batches of tournaments and display each team's championship probability.
•	Build a visual bracket display instead of text output.

