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

|   result |   golddiffat15 |   xpdiffat15 |   csdiffat15 |   killsat15 |   deathsat15 |
|---------:|---------------:|-------------:|-------------:|------------:|-------------:|
|        0 |       -1346.97 |     -953.119 |     -17.0443 |     3.65241 |      5.28458 |
|        1 |        1346.97 |      953.119 |      17.0443 |     5.26688 |      3.67385 |

---

## Assessment of Missingness

### NMAR Analysis

When looking through the original dataset, I was able to identify `ban1`, `ban2`, `ban3`, `ban4`, `ban5` as NMAR. Sometimes one of the ban columns would be NaN but the rest of the ban columns would correctly have data in the same row, so we can tell it is not randomly missing. When playing League of Legends, players have choose not to ban anyone and in this case that would would result in a NaN. One way to turn the ban columns into MAR, is for every player row have a column that we can call `used_ban`, which would be a boolean determining if they used their ban.

### Missingness Dependency

To test missingness dependency, I will test the missingness of `golddiffat15` column when it depends on the `league` column and the `result` column.

First, I ran a permutation test to test the missingness of `golddiffat15` on `league`.

Null Hypothesis: The distribution of the `league` column is the same when `golddiffat15` is missing and when `golddiffat15` is not missing.

Alternative Hypothesis: The distribution of the `league` column is not the same when `golddiffat15` is missing and when `golddiffat15` is not missing

Test Statistic: Total variation distance (TVD)

Below is the empirical distribution of the TVD's. I found an observed TVD of 0.991, which gave me a p-value of 0.00, which is less than a significance level of 0.5, meaning I reject the null hypothesis. This indicates that missingness of `golddiffat15` (MAR) depends on `league`.

<iframe
  src="assets/league_miss_tvd.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Next, I ran a permutation test to test the missingness of `golddiffat15` on `result`.

Null Hypothesis: The distribution of the `result` column is the same when `golddiffat15` is missing and when `golddiffat15` is not missing.

Alternative Hypothesis: The distribution of the `result` column is not the same when `golddiffat15` is missing and when `golddiffat15` is not missing

Test Statistic: Abosulute Mean Difference (absolute difference between the average win rate when `golddiffat15` is missing and when it is not missing)

Below is the empirical distribution of the absolute mean differences. I found an observed absolute mean difference of 0, which gave me a p-value of 1.00, which is greater than a significance level of 0.5, meaning I fail reject the null hypothesis. This indicates that missingness of `golddiffat15` does not depend on `league`.

<iframe
  src="assets/league_miss_tvd.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---

## Hypothesis Testing

I will be running a hypothesis test to test if there is significant difference between the distribution of results when teams do and do not have a gold lead at 15 minutes . Before running the test, I added a column `hasgoldleadat15` which contains a boolean value depending if a team has a gold difference of greater than 0 at 15 minutes (True if they do, False otherwise). 

Null Hypothesis: The distribution of a team's result (whether they win/lose) is the same for teams that have or don't have a gold lead at 15 minutes.

Alternative Hypothesis: The distribution of a team's result (whether they win/lose) is not the same for teams that have or don't have a gold lead at 15 minutes.

Test Statistic: Difference of mean result (win rate) between teams with a gold lead at 15 minutes and teams that do not have a gold lead at 15 minutes.

Significance Level: 5%

I recieved an observed difference of mean of 0.424, which gave me a p-value of 0.00, which allows me to reject the null hypothesis. This gives as reason to believe that the distrubition of a team's result result (whether they win/lose) is not the same for teams that have or don't have a gold lead at 15 minutes. This relates back to the central question, suggesting that possibly early game statistics, specifically gold, may play some role in determing the result.

---

## Framing a Prediction Problem

From the Hypothesis Test, we have evidence to believe that having a gold lead at 15 minutes has some sort of influence on the result of the game. Now, it brings up the question, if we would actually be able to predict the result of game, using early game statisics such as `golddiffat15` and others. To address this prediction problem, we must acknowledge that this a classification problem, specifically a binary classification, since it is either 0 for a lost and 1 for a win. Because data is repeated twice for matches for difference statistics like `golddiffat15` (if you have +100 diff, then there would a team with -100) we will only consider Blue side teams, as both sides are effectively the same. The metric we will use is accuracy, since the classes are well balanced, since there is approximately 50% teams winning and 50% teams winning losing, if we consider Blue side only. At the time of the test, I will only have access to only 75% of the data since I will be splitting the data into 75% training and 25% test. In addition, at the time of test I will consider all features except `gametime`, because every statistic is something we would be gathered in game, while gametime is after the game is done.

---

### Baseline Model

For my baseline model, I will use a Random Forest Classifier with 3 features. I will start with the three difference at 15 minutes statistics: `golddiffat15`, `xpdiffat15`, `csdiffat15`. These features are all numerical and do not require transformations, however I still applied StandardScaler transformation in case I ever switch models. I also had to impute some values for each of the numerical features, using each of their medians as the Random Forest Classifier cannot handle missing values. Aftering fitting, the accurracy of the model is a suprising 96.2% on training data and 68.9% on the test data. This is case of extreme overfitting that my future model must handel.

---

### Final Model

For my final model I added two new features. I one hot encoded the `league` column as certain leagues may better take advantage or fall short when they are ahead. I also added `killdiffat15` column, which is the result subtracting the `deathsat15` from `killsat15`. I decided to stick with a Random Forest Classifier as they function better than Decision Trees, however, I used GridSearchCV to find the best hyperparameters for the Random Forest Classifier which the hyperparameters I included were max depth and the needed minimumn number of samples in order to split. From a result of all of this, the model scored 76.3% accuracy on the training data and 71.1% accuracy on the test data, meaning around 2.2% improvement.

---

### Fairness Analysis

I also analyzed the fairness of my model, specifically if it performs better or worse for teams that have a gold lead at 15 minutes compared to teams that do not have a gold lead at 15 minutes. 

Group X: Teams that have a gold lead at 15 minutes
Group Y: Teams that do not have a gold lead at 15 minutes
Null Hypothesis: Our model is fair. Its accuracy for teams who have gold lead at 15 minutes is same as the accuracy for players who do not have gold lead at 15 minutes.
Alternative hypothesis: Our model is unfair. Its accuracy for teams who have gold lead at 15 minutes is not the same as the accuracy for players who do not have gold lead at 15 minutes.
Test statistics: Absolute Difference in accuracy between teams who have gold lead at 15 minutes and teams who do not have a gold lead.
I got a observed absolute accuracy difference of 0.0153 resulting in a p-value of 0.4148, failing to reject the null hypothesis, meaning our model appears to be fair.

