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
- `golddiffat15`: The difference in total gold between the player/team and their opponent at 15 minutes.
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

I conducted some univariate analysis to examine the distribution of single variables. One the variable I analyzed was the distribution of kills at 15 minutes as shown below. We can notice that the shape of the histogram is skewed right, meaning the majority of games have low number of kills at 15 minutes, but there are a few games that have significantly higher, extreme kill counts.

<iframe
  src="assets/killsat15_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

I also conducted some bivariate analysis to examine the relationship between two variables. One relationship I analyzed was between the gold difference at 15 minutes and the match result. We can see from the box plots, that on average, when a team wins, they tend to have a higher gold difference at 15 minutes than when they lose.

<iframe
  src="assets/golddiff_boxes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates

I was also able to find some interesting aggregates within the data.

One interesting aggregate I found involved first groupby by the match result then finding the mean for some numerical columns. 
As shown below, the columns which dealt with a difference at 15 minutes were symmetrical in their differences, for example take `golddiffat15`, when a team won they had an average gold difference of positive 1346.97 while when a team lost they had an average gold difference of exactly the opposite, -1346.97. This makes sense as a difference means as a team goes up 100, another team goes exactly down for the same. However, we can notice that whenever a team won, they had a postive difference in that statistic.  

'|   result |   golddiffat15 |   xpdiffat15 |   csdiffat15 |   killsat15 |   deathsat15 |\n|---------:|---------------:|-------------:|-------------:|------------:|-------------:|\n|        0 |       -1346.97 |     -953.119 |     -17.0443 |     3.65241 |      5.28458 |\n|        1 |        1346.97 |      953.119 |      17.0443 |     5.26688 |      3.67385 |'

---

## Assessment of Missingness

### NMAR Analysis

When looking through the original dataset, I was able to identify `ban1`, `ban2`, `ban3`, `ban4`, `ban5` as NMAR. Sometimes one of the ban columns would be NaN but the rest of the ban columns would correctly have data in the same row, so we can tell it is not randomly missing. When playing League of Legends, players have choose not to ban anyone and in this case that would would result in a NaN. One way to turn the ban columns into MAR, is for every player row have a column that we can call `used_ban`, which would be a boolean determining if they used their ban.

### Missingness Dependency

To test missingness dependency, I will test the missingness of `golddiffat15` column when it depends on the `league` column and the `result` column.

First, I ran a permutation test to test the missingness of `golddiffat15` on `league`.

Null Hypothesis: The distribution of the 'league' column is the same when `golddiffat15` is missing and when `golddiffat15` is not missing.

Alternative Hypothesis: The distribution of the 'league' column is not the same when `golddiffat15` is missing and when `golddiffat15` is not missing

Test Statistic: Total variation distance (TVD)



