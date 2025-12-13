# League of Legends Early Game Statistics Analysis

by Dylan Louie 

---

## Introduction

League of Legends (LOL) is large online multiplayer game, developed by Riot Games, amassing over 100 million monthly player worldwide. In this project, I analyze a dataset of professional League of Legends matches sourced from [Oracle's Elixir](https://oracleselixir.com/tools/downloads). The dataset includes key gameplay statistics from over 9900 matches from the 2025 season. 

In League of Legends, your performance in the "early game" or the early stages of the game, can often have profound effects to the outcome of the rest of the game. These small advantages gained in the early game can lead to what is called a "snowball effect", causing you gain more and more of an advantage, ultimately leading you to your victory. However, this is not always the case. 

The central question of this project is **How much do the first 15 minutes of the game dictate the final result of the match? Do early game advantages truly result in more wins?**
In order to explore this question we must refer back to our dataset. 

The original dataset contains a total of 118932 rows and 164 columns. Despite the large amounts of data we can explore, we will be focusing on some few key columns:

- `gameid`: A unique string identifier for every match. This is used to distinguish individual games in the dataset.
- `teamname`: The name of the professional team (e.g., "T1 Esports", "Weibo Gaming").
- `league`: The region or tournament the game was played in (e.g., "LCK", "LPL", "LFL2"). 
- `side`: Indicates which side of the map the team played on ("Blue" or "Red").
- `result`: The binary variable indicating the outcome of a match (1 indicates a Win, and 0 indicates a Loss).
- `gamelength`: The total duration of the match in seconds.
- `golddiffat15`: The difference in total gold between the player/team and their opponent at the 15-minute mark.
- `xpdiffat15`: The difference in total experience points (XP) between the player/team and their opponent at 15 minutes.
- `csdiffat15`: The difference in "Creep Score" (minions killed) at 15 minutes between the player/team.
- `killsat15`: The total number of enemy champions killed by the player/team in the first 15 minutes.
- `deathsat15`: The total number of times the player/team's own champions died in the first 15 minutes.

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Our first step is make sure the data is cleaned so we can properly analyze it. The first thing I decided to do was to only keep the rows that contained information about the entire team's data as a whole. This means for each match there are two rows, one for each team. I was able to do this by including only rows where the `position` column contained the keyword `team`. I then decided to keep only our key columns.

| gameid           | teamname                |   result | side   | league   |   gamelength |   golddiffat15 |   xpdiffat15 |   csdiffat15 |   killsat15 |   deathsat15 |
|:-----------------|:------------------------|---------:|:-------|:---------|-------------:|---------------:|-------------:|-------------:|------------:|-------------:|
| LOLTMNT03_179647 | IziDream                |        0 | Blue   | LFL2     |         1592 |          -3837 |         -469 |          -16 |           0 |            3 |
| LOLTMNT03_179647 | Team Valiant            |        1 | Red    | LFL2     |         1592 |           3837 |          469 |           16 |           3 |            0 |
| LOLTMNT06_96134  | Esprit Shōnen           |        1 | Blue   | LFL2     |         1922 |           5069 |         2014 |           64 |          10 |            6 |
| LOLTMNT06_96134  | Skillcamp               |        0 | Red    | LFL2     |         1922 |          -5069 |        -2014 |          -64 |           5 |           10 |
| LOLTMNT06_95160  | Karmine Corp Blue Stars |        0 | Blue   | LFL2     |         1782 |            118 |         1990 |          -43 |          10 |            6 |

* This dataframe may be adjusted (add a new column temporarily) when dealing with hypothesis testing or creating a model

### Univariate Analysis

