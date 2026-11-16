# NBA-Prediction-Project

[README.md](https://github.com/user-attachments/files/34890389/README.md)
# Identifying NBA Player Archetypes and Predicting All-NBA Selections

A data mining project that groups NBA players into modern statistical archetypes with PCA and K-Means, then uses those same statistical profiles to predict All-NBA selections.

## Problem Statement

NBA players are traditionally classified into five positions, but modern players often fill roles that do not fit those categories. This project investigates whether player statistics alone can identify modern player archetypes, which statistics are most associated with All-NBA selection, and whether certain archetypes are more likely to produce All-NBA players.

The project combines unsupervised learning (PCA and clustering) and supervised learning (classification) around one connected question, rather than treating them as two separate analyses.

## Research Questions

1. Can NBA players be grouped into distinct modern archetypes using statistical performance data?
2. Which statistics best distinguish those archetypes?
3. Which statistical factors are most associated with All-NBA selection?
4. Can player statistics accurately classify whether a player is selected to an All-NBA team?
5. Are certain archetypes more likely to produce All-NBA players than others?

Question 5 ties the two halves together: the clusters found in the first half become an input to, and a lens on, the second.

## Pipeline

```
NBA player-season data
        ↓
Cleaning and wrangling
        ↓
Standardize player statistics
        ↓
PCA
        ↓
K-Means clustering
        ↓
Interpret and name archetypes
        ↓
Add All-NBA labels
        ↓
Classification model
        ↓
Feature importance
        ↓
Compare All-NBA rates across archetypes
```

## Data

Each observation is one player-season.

| | |
|---|---|
| Seasons | 2018-19 through 2024-25 (7 seasons) |
| Observations | 1,373 qualified player-seasons |
| Source | Stathead season exports, about 200 players per season |
| Target | `All_NBA` (1 = selected, 0 = not), 105 positive cases (15 per season) |
| Also available | `All_NBA_Team` (1, 2, 3, or 0) and `AS` (All-Star flag, 183 positive cases) |

**Qualification rule:** a player-season is kept only if the player appeared in at least 20 games and averaged at least 15 minutes per game. This keeps players who barely played from forming meaningless clusters.

**Class imbalance:** only about 7.6% of player-seasons are All-NBA, so accuracy alone is a misleading metric. Precision, recall, F1, and ROC-AUC matter more here.

### Features

Per-game rates and efficiency measures are used instead of season totals, so that minutes played does not dominate the clustering.

| Category | Variables |
|---|---|
| Scoring | `PPG`, `FGAPG`, `2PAPG`, `3PAPG`, `FTAPG` |
| Shooting | `FG%`, `2P%`, `3P%`, `FT%`, `eFG%` |
| Playmaking | `APG`, `TOVPG` |
| Rebounding | `RPG`, `ORPG`, `DRPG` |
| Defense | `SPG`, `BPG` |
| Efficiency | `TS%`, `WS` |
| Playing time | `MPG` |

The final feature set should be narrowed to roughly 10 to 15 variables, since several of these are strongly correlated. Usage rate and PER are not in the current exports and would need to be added separately.

## Methods

### Part 1: Player archetypes (unsupervised)

1. Standardize the selected features.
2. Apply PCA to reduce dimensionality.
3. Run K-Means over a range of k (roughly 4 to 8) and choose k using elbow and silhouette analysis.
4. Examine each cluster's statistical profile and assign a basketball interpretation, such as 3&D Wing, Stretch Big, Primary Creator, Scoring Guard, or Interior Big.

K-Means does not name clusters. The archetype labels are our interpretation of each cluster's profile, and k is chosen from the data rather than fixed in advance to match archetypes we expect to find.

### Part 2: All-NBA classification (supervised)

1. Train Logistic Regression, Decision Tree, and Random Forest classifiers on player statistics.
2. Evaluate with accuracy, precision, recall, F1, and ROC-AUC on a held-out test set.
3. Examine feature importance to see which statistics drive selection.
4. Test whether adding the cluster assignment improves prediction, and compare All-NBA rates across archetypes.

## Repository Structure

```
NBA_Player_Archetypes/
├── data/
│   ├── raw/                          # Stathead exports per season, plus all_nba_selections.csv
│   └── processed/
│       ├── nba_2024_25_clean.csv     # Single-season cleaned file
│       └── nba_player_seasons_clean.csv   # Master dataset, all seasons with labels
└── notebooks/
    └── data_collection_cleaning.ipynb
```

## Status

- [x] Data collection and cleaning
- [x] Per-game feature engineering
- [x] All-NBA labels merged (names normalized to handle accents, such as Jokić and Dončić)
- [ ] Exploratory data analysis
- [ ] PCA and K-Means clustering
- [ ] Archetype interpretation
- [ ] Classification models and evaluation
- [ ] Archetype vs. All-NBA comparison
- [ ] Final notebook and presentation

## Team Roles

| Role | Responsibilities |
|---|---|
| Data and feature engineering | Acquire player data, clean it, combine seasons, create selection labels, choose and transform statistics |
| PCA and player archetypes | Standardize variables, run PCA, choose k, run K-Means, visualize clusters, interpret and name archetypes |
| All-NBA classification | Build classifiers, train and test, evaluate metrics, investigate feature importance |

EDA, interpretation, the final notebook, and the presentation are shared across the team.

## Getting Started

Requires Python 3 with `pandas`, `numpy`, and `scikit-learn`, plus Jupyter.

```bash
jupyter notebook NBA_Player_Archetypes/notebooks/data_collection_cleaning.ipynb
```

The notebook reads from `data/raw/` and writes the cleaned master dataset to `data/processed/nba_player_seasons_clean.csv`.
