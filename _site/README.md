# EECS 398 Project

## Introduction

Our dataset features 12,549 League of Legends games from tier one professional leagues in 2022. It provides a variety of statistics at both the team and individual level for each of these games. The dataset has 150,588 entries with 161 features which includes some match metadata.

There are many different ways to win a match of League of Legends–but, how do the pros do it? As players ourselves, we are interested to see what aspects of a match matter the most. Is it the gold and experience, the towers and inhibitors, or something else?

Using our intuition and experience as players, we selected the following columns to aid us in our analysis:

| Name of Column     | Description |
|--------------------|-------------|
| `golddiffatXX`     | The difference of gold between opposing teams or players at XX minutes (10, 15, and 20) |
| `xpdiffatXX`       | The difference of experience between opposing teams or players at XX minutes (10, 15, and 20) |
| `towers`           | The number of towers destroyed by a team |
| `inhibitors`       | The number of inhibitors destroyed by a team |
| `gamelength`       | The length of a game measured in seconds |
| `side`             | Which side a person/team belongs to |
| `gameid`           | Unique identifier for each game in the dataset |
| `result`           | Whether the team/player won the game or not |
| `earnedgold`       | The amount of gold earned for a team/player |
| `champion`         | The champion that a player played |
| `position`         | The role that a player took on |
| `datacompleteness` | Whether or not data collection for a game is complete |

---

## Data Cleaning and Exploratory Data Analysis

We took 5 distinct steps in our data cleaning process:

### 1. Filtering for Complete Data
We filtered the dataframe on the `datacompleteness` column to remove remakes or corrupted matches. This ensures only fully recorded games are used in our analysis.

### 2. Dropping Unnecessary Metadata Columns
We removed irrelevant metadata such as `participantid`, `playerid`, and `url`, which don’t help us analyze outcomes or performance.

### 3. Separating Team and Player Statistics
We split team and player data using the `position` column. This allowed us to analyze team and individual performance separately.

### 4. Dropping Columns with All NaN Values
Columns that are completely `NaN` were removed, as they are either exclusive to team or player views and not helpful in the current frame.

### 5. Creating Derived Features for Team Advantage
We engineered difference columns (e.g., `tower_diff = towers - opp_towers`) to directly capture who had the advantage in key objectives.

---

## Univariate Analysis

![Gold Diff Distribution Plot](path/to/your/golddiff_plot.png)

The plot above displays the distribution of the gold difference between teams, for teams that won their respective matches, at 20 minutes. We observe a peak in the distribution at 2000–4000 gold, which means that the majority of teams that won their games had at least 2000 more gold than the other team at the 20-minute mark.

---

## Bivariate Analysis

![Game Length vs XP Diff Plot](path/to/your/xpdiff_plot.png)

The plot above displays the relationship between game length and average XP difference by role in winning games. Irrespective of what position is played, the majority of winning teams hold a positive XP lead for the entire game. In the context of our analysis, this could indicate that gaining and maintaining an XP advantage, no matter how small, is critical to securing a win.

---

## Interesting Aggregates

![Pivot Table Champion Win Rates](path/to/your/pivot_table.png)

The pivot table above displays the win rate for each champion in each respective role, based on the games in the dataset available. The values displayed in the table are the proportion of games won to total games played on that champion for that specific role. NA values represent that the champion has not been played in a specific role.

---

## Imputation

Taking into account the context of our dataset and our target question, we have opted **not to impute any data**. Matches in League of Legends can swing on a dime — what this means is that when we impute data we are almost always going to introduce noise and assumptions that could distort the actual story of the match.

---

## Framing the Decision Problem

**Can we accurately predict the outcome of a League of Legends game using only data available up to the 20-minute mark?**

Our problem is a **binary classification task** that aims to predict the `result` column of the dataset. We chose this approach to explore whether in-game statistics available by the 20-minute mark provide enough information to make accurate win/loss predictions.

We use the **F1-score** as our evaluation metric because it offers a single value that balances both precision and recall. Unlike accuracy, which can be misleading when the classes are imbalanced, the F1-score gives us a more complete picture of model performance. This makes it especially useful now and in future iterations where we may experiment with imbalanced datasets to improve and evaluate performance.

---

## Baseline Model

The baseline model utilizes 6 variables:

- `golddiffat20`
- `xpdiffat20`
- `csdiffat20`
- `killsat20`
- `assistsat20`
- `deathsat20`

These features are all **quantitative**, so no special encoding or transformations were required. There are **no ordinal or nominal features** in this version of the model.

Our baseline model achieved an **F1-score of 0.79** for both classes (win/loss), which indicates that the model is performing significantly better than random guessing. However, we believe there is room for improvement by incorporating more nuanced features and potentially engineered features.

---