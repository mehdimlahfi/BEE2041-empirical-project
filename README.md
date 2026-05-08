# Live-Service vs Traditional Games: Steam Player Trends

This project investigates whether live-service games dominate the most-played games on Steam, and whether they show stronger player activity and retention than more traditional games.

The project uses data scraped from SteamCharts and combines it with game-level metadata. The final output is a Quarto website/blog post with a retro arcade-inspired visual design.

The main research question is:

> Are live-service games more successful at maintaining large player bases than traditional games?

The project focuses on the top Steam games by player activity and compares live-service and traditional games using summary statistics, visualisations, and simple regression analysis.

---

## Repository Structure

```text
bee2041-empirical-project/
├── README.md
├── data/
│   ├── top_steam_games.csv
│   ├── requirements.txt
│   ├── steamcharts_top_games_history.csv
│   ├── steamcharts_failed.csv
│   └── all_data.csv
├── output/
│   ├── figures/
│   │   ├── top_games_current_players.png
│   │   ├── live_service_share.png
│   │   └── retention_boxplot.png
│   └── tables/
│       ├── summary_by_model.csv
│       └── regression_results.txt
├── notebooks/
│   ├── presentation.ipynb
│   └── steam_webscraping.ipynb
└── report/
│   ├── appendix/
│       └── regression_results.txt
│   ├── data
│       └── summary_by_model.csv
│   ├── figures
│       ├── live_service_share.png
│       ├── retention_boxplot.png
│       └── top_games_players.png
│   ├── _quarto.yml
│   ├── blog.html
│   ├── blog.qmd
│   └── styles.css
  
```
---
## Workflow Summary

The project should be run in the following order:

1. webscraping-traning.ipynb
2. presentation.ipynb
3. blog.qmd

---

## Python packages

The main Python libraries used are:
```text
pandas
numpy
requests
beautifulsoup4
matplotlib
statsmodels
```
Install the required Python packages with the requirements.txt file:

```text
pip install -r requirements.txt
```

### Step 1: Web Scraping

Run the notebook:

```text
jupyter notebook webscraping-traning.ipynb
```

This notebook collects data from SteamCharts.

The scraping process uses:

- requests to download HTML from SteamCharts
- BeautifulSoup to parse the HTML
- re to extract Steam app IDs from URLs
- pandas.read_html() to extract monthly player tables
- time.sleep() to avoid sending requests too quickly

The notebook first scrapes the top Steam games from:
```text
https://steamcharts.com/top
```

It then visits each individual game page, for example:
```text
https://steamcharts.com/app/{app_id}
```
For each game, the notebook extracts monthly SteamCharts data including:

- month
- average players
- monthly gain
- percentage gain
- peak players
- Steam app ID
- game name

The outputs from this stage are saved in data/.

Expected outputs:
```text
data/raw/top_steam_games.csv
data/raw/steamcharts_top_games_history.csv
data/raw/steamcharts_failed.csv
```
steamcharts_failed.csv records any games that could not be scraped successfully.

## Step 2: Data Analysis and Figure Generation

Run the notebook:
``text
jupyter notebook presentation.ipynb
```
This notebook loads the raw scraped data and additional metadata files:
```text
data/raw/top_steam_games.csv
data/raw/steamcharts_top_games_history.csv
data/raw/all_data.csv
data/raw/steam_games_2026.csv
```
The notebook then prepares the analysis dataset by:

- Cleaning column names
- Converting the month variable to datetime format
- Selecting the latest SteamCharts observation for each game
- Renaming average players as current players
- Calculating player retention
- Merging SteamCharts data with game metadata
- Manually classifying games as live-service or traditional
- Creating logged current-player values for regression analysis

The retention variable is calculated as:
```text
retention = current_players / peak_players
```
This gives a simple measure of how much of a game's peak player base is still active in the latest observation.

**Live-Service Classification**

Games are manually classified as live-service if they are designed around continuous updates, online multiplayer, recurring content, long-term engagement, or ongoing monetisation.

The live-service app IDs used in the analysis include:
```text
live_service_app_ids = [
    730,      # Counter-Strike 2
    570,      # Dota 2
    578080,   # PUBG
    1172470,  # Apex Legends
    1085660,  # Destiny 2
    2676230,  # FiveM
    440,      # Team Fortress 2
    252490,   # Rust
    359550,   # Rainbow Six Siege
    230410,   # Warframe
    2507950   # Delta Force
]
```
Each game is assigned:
```text
live_service = 1
```
if it is classified as live-service, and:
```text
live_service = 0
```
if it is classified as traditional.

**Outputs Generated**

The analysis notebook creates both tables and figures.
```text
Summary table
output/tables/summary_by_model.csv
```
This table compares live-service and traditional games.

The summary results show that the sample contains 12 traditional games and 8 live-service games. Live-service games have higher mean average players and higher mean peak players in this sample.
```text
Regression results
output/tables/regression_results.txt
```
Two simple OLS regressions are estimated:
```text
log_current_players on live_service
retention on live_service
```
The first model finds a positive relationship between live-service status and logged current players. The coefficient on live_service is approximately 1.00 and is statistically significant at the 1% level.

The second model estimates the relationship between live-service status and retention. The coefficient is positive but not statistically significant.

These results suggest that live-service games in this sample tend to have larger active player bases, but the evidence for stronger retention is weaker.

**Figures**

The following figures are generated:
```text
output/figures/top_games_current_players.png
output/figures/live_service_share.png
output/figures/retention_boxplot.png
top_games_current_players.png
```
This horizontal bar chart shows the most-played Steam games in the sample by current player count.
```text
live_service_share.png
```
This bar chart compares the number of traditional and live-service games in the sample.
```text
retention_boxplot.png
```
This boxplot compares player retention between traditional and live-service games.

## Step 3: Render the Quarto Blog

The final blog post is written in:
```text
blog.qmd
```
The website configuration is stored in:
```text
_quarto.yml
```
The project is configured as a Quarto website with the title:
```text
Live-Service vs Traditional Games
```
The website uses:
```text
styles.css
```
for the visual theme.

To render the website, run:
```text
quarto render
```
The rendered website will be created in:
```text
report/
```
**Website Design**

The website uses a retro gaming / arcade terminal style.

The CSS file gives the website:
```text
black background
neon green headings and links
subtle dotted/pixel background pattern
white Arial-style body text
glowing borders around figures
dark table styling
arcade-inspired visual presentation
```
The main styling file is:
```text
styles.css
```
The CSS is connected through _quarto.yml using:
```text
format:
  html:
    theme: none
    css: styles.css
    toc: true
    page-layout: full
```
The visual design is intended to match the project topic by making the blog feel like a retro video game dashboard.

## Main Findings

The project finds three main results:

1. Live-service games are a large part of the top Steam games, but they do not make up the whole sample. In this version of the sample, 8 of the top 20 games are classified as live-service, while 12 are classified as traditional.
2. Live-service games have higher average player counts in this sample. The summary statistics show that live-service games have substantially higher mean average players and mean peak players than traditional games.
3. Retention differences are less clear. Live-service games have slightly higher mean retention, but the regression results do not show a statistically significant relationship between live-service status and retention.

## Reproducibility Instructions

To reproduce the full project from the beginning:

1. Clone the repository

```text
git clone <https://github.com/mehdimlahfi/BEE2041-empirical-project>
```

2. Install dependencies
```text
pip install -r requirements.txt
```
3. Run the scraping notebook
```text
jupyter notebook steam_webscraping.ipynb
```
This creates the steamcharts_top_games_history.csv and displays the top 20 most-played Steam games.

4. Run the analysis notebook
```text
jupyter notebook presentation.ipynb
```
This creates the figures and tables.

5. Render the Quarto website
```text
quarto render
```
6. Open the final website

Open:
```text
report/blog.html
```

## Limitations

This project has several limitations:

- The sample focuses on top Steam games only, so the results do not represent all games on Steam.
- Live-service classification is manual and therefore involves judgement.
- SteamCharts captures Steam player activity only and does not include players on other platforms.
- The regression analysis is descriptive and should not be interpreted as causal.
- Current-player and peak-player values can change over time.
Project Files
File	Purpose
webscraping-traning.ipynb	Scrapes SteamCharts data
presentation.ipynb	Cleans data, creates variables, runs analysis, exports figures and tables
blog.qmd	Final written blog post
_quarto.yml	Quarto website configuration
styles.css	Retro arcade website styling
summary_by_model.csv	Summary statistics by game type
regression_results.txt	OLS regression output
top_games_current_players.png	Bar chart of current players
live_service_share.png	Live-service vs traditional count chart
retention_boxplot.png	Retention comparison boxplot
Author

Mehdi Mlahfi

BEE2041 – Data Science in Economics
University of Exeter
