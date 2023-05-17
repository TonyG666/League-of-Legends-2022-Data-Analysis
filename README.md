# League of Legends 2022 Data Analysis

By Ricky Zhu(r2zhu@ucsd.edu) and Tony Guo(xig003@ucsd.edu)

# Introduction

## Background information ##

![Image not found](images/lol.png)

**League of Legends (LoL)** is a highly popular and competitive multiplayer online battle arena (**MOBA**) game developed and published by Riot Games. With a massive player base worldwide, LoL has become a staple in the eSports industry. Players assume the roles of powerful champions, forming teams and battling against each other in intense strategic matches. The game features a vast roster of unique champions, each with their own abilities and playstyles, providing endless possibilities for dynamic gameplay and strategic depth. 

Based on our gaming experiences with League of Legends, we are interested in the following question: 
* **Which role “carries” (does the best) in their team more often: Top (top) or Mid laners(mid)?**

To answer this question, we use the dataset containing 2022 League of Legends eSports match data from **OraclesElixir**, as it's representative for the League of Legends player population and meta for **2022**. Also it provides valuable insights into the performance and dynamics of professional teams in the game for the future. Analyzing this dataset can offer a deeper understanding of the strategies, strengths, and weaknesses of different roles within a team.

By examining the match data from 2022, we can provide evidence-based conclusions about the impact and effectiveness of **top and mid laners** in carrying their teams to victory.

*Maybe our conclusion about which position carries more can also activates potential players to try playing that position or the specific champion/hero more often and even be more responsible for making contributions to the whole team's progress and development.*


### 2022 League of Legends eSports match dataset breakdown(from OraclesElixir): ###


**Number of rows in original dataset**: 149400

**Meaning of each row**: a single player's eSport match statistics in a single game

**Relevant Columns:**

1. **datacompleteness**: Whether a single player's statistics in a single eSport match is **filled** in original dataset before we did data cleaning. 

2. **teamname**: The **name of the team** the player is in.

3. **position**: The player's **position** in a single eSport match. League of Legends only has five positions: Top laners(**top**), Jungle(**jng**), Mid laners(**mid**), Bottom laners(**bot**), and Support(**sup**). 
* *Note that in a League of Legends map, there are three lanes total and jungle areas. Top laner covers top lane, Mid laner covers mid lane, Jungle covers jungle areas, Support and Bottom laner cover the bottom lane.*

4. **kills**: The **number of kills** a player have in a single eSport match.

5. **assists**: The **number of assists** a player have in a single eSport match.

6. **death**: The **number of deaths** a player have in a single eSport match.

7. **earned gpm**: earned **Gold Per Minute** by player in a single game. 
* *Gold in League of Legends is used to buy items in order to make your character stronger.*

8. **cspm**: **Creep Score Per Minute**, indicating the number of **minions** killed by a player per minute.


# Cleaning and Exploratory Data Analysis


## Data Cleaning Process

1. We create a **copy** of the original dataset in order to keep it unchanged.

2. Each team has **five players** and a match contains **two teams** and so ten players. After examining the file, we find **every 11th row and 12th row** to be two teams' overall statistics and the **ten rows before them** to be these two teams' individual players' statistics of this match. As our goal is to examine individual performances of top laners and mid laners, we drop all rows of whole teams.

3. The 'datacompleteness' column contains three kinds of values: 'complete', 'ignore', and 'partial'.
We replace 'ignore' and 'partial' with **False** and 'complete' with **True** because the boolean values make it easier to do analysis.

4. After examination of original file, we see that the 'teamname' column initially have NaN values and also a kind of values called "unknown team". We replace them with **NaN** to clean the data.

5. We add a column called **kda_standardized**: **Kill Death Assist Ratio** by a player in a single game, calculated by 
**(Kills + Assists) / Deaths**, and then standardized. Unstandardized Kills, Assists, Deaths are three columns we kept before(Refer to Relevant Columns section above).

6. We add a column called **gpm_standardized**: get Earned gold per minute and then standardize. Unstandardized GPM is provided in original file directly.

7. We add a column called **cspm_standardized**: get Creep score per minute and then standardize. Unstandardized CSPM is provided in original file directly.

8. We add a column called **carry_score**: For our analysis, we calculate a score for every player called 'carry_score'. It is our **self-designed** measurement of **how well a player performs in a game**, calculated by **(kda_standardized + gpm_standardized + cspm_standardized) / 3**. The reason why we use these three standardized statistics is that these three factors impact the development of a League character the most. The higher the three statistics are, the better a player's possible performance would be in various aspects such as the amount of damage dealt or the quality of the player's items. Also, these three statistics are the most crucial factors in determining which player is the **Most Valuable Player**(i.e. **MVP**) in a single match.

9. We then **drop** the columns that are not in standardized units, specifically **kills**, **assists**, **deaths**, **earned gpm**, **cspm**.

### The head of DataFrame(After Cleaning) ###

```
print(league_head.to_markdown(index=False))
```

| datacompleteness   | teamname                 | position   |   standardized_kda |   standardized_gpm |   standardized_cspm |   carry_score |
|:-------------------|:-------------------------|:-----------|-------------------:|-------------------:|--------------------:|--------------:|
| True               | Fredit BRION Challengers | top        |          -0.696223 |           0.242621 |            0.563221 |     0.0365397 |
| True               | Fredit BRION Challengers | jng        |          -0.633408 |          -0.47272  |           -0.37376  |    -0.493296  |
| True               | Fredit BRION Challengers | mid        |          -0.507778 |          -0.242903 |            0.134244 |    -0.205479  |
| True               | Fredit BRION Challengers | bot        |          -0.759038 |           0.111582 |            0.506755 |    -0.0469004 |
| True               | Fredit BRION Challengers | sup        |          -0.675285 |          -1.45253  |           -1.57038  |    -1.23273   |

## Distribution of the standardized KDA among all players ##

In the below univariate histogram, the x axis shows standardized KDA in standardized units, ranging from -1 to 8, with a bin size of 0.5 standardized units. The y axis shows the amount of players with their specific standardized KDA.
The graph is skewed to the right, inferring that most players' standardized KDA is in the range of -1~0 standardized unit, which further means that a lot of players' KDA is relatively lower compared to the average value within the dataset. 

<iframe src="assets/univariate_kda.html" width=800 height=600 frameBorder=0></iframe>

## Information about carry score among all player positions ##

The below bivariate boxplot shows various information about carry score such as median, interquatile range, and outliers. The x axis displays carry score in standardized units, while the y axis labels each of the five positions. Despite that top laners and mid laners have similar carry score distribution, mid laners' scores are a little higher overall.
*Interestingly, carry score tend to have more outliers when carry score is high.*

<iframe src="assets/boxplot.html" width=800 height=600 frameBorder=0></iframe>


## Grouped table of respective mean standardized KDA, GPM, CSPM, and carry score for the five positions##

```
print(league_position.to_markdown(index=True))
```

| position   |   standardized_kda |   standardized_gpm |   standardized_cspm |   carry_score |
|:-----------|-------------------:|-------------------:|--------------------:|--------------:|
| bot        |         0.122206   |           0.798652 |            0.793597 |      0.571485 |
| jng        |         0.00599775 |          -0.196162 |           -0.22893  |     -0.139698 |
| mid        |         0.0684821  |           0.430029 |            0.625648 |      0.37472  |
| sup        |        -0.0218033  |          -1.35438  |           -1.67982  |     -1.01867  |
| top        |        -0.174883   |           0.321859 |            0.489504 |      0.21216  |

The table demonstrates the mean statistics for each position in League of Legends and can be used for comparison among different positions. Also, it provides a more specific and detailed numerical illustration of the five positions. For example, mid and top are the two positions with the smallest difference in mean standardized carry score.


# Assessment of Missingness #

## NMAR Analysis ##
**Note that NMAR means that the missingness of some data is dependent on the missing values themselves**

We don't believe that there is column in our dataset that is NMAR. We would like to obtain additional information about **possible correlation** between **datacompleteness** column and missingness in **teamname** column as well as **position** column and missingness in **teamname** column in order to see the type of missingness of the **teamname** column.

## Missingness Dependency ##

### MAR(Missing at Random) ###
**Note that MAR means that a column's missing values depend on other columns and not on missing values themselves**

* We perform permutation tests to check if **teamname** column depends on **datacompleteness** column or not. 

* We get the p-value by computing the proportion of **simulated total variation distances** that are **bigger than** the **observed total variation distance**.

* After obtaining our p-value for this permutation testing: 0.004, which is smaller than the significance level, we **reject the null hypothesis** that in year 2022, distribution of **datacompleteness** when **teamname** is missing is the same as when **teamname** is not missing. 

* The result of this permutation testing is that the missingness of **teamname** column depends on **datacompleteness** column, which means the missingness of **teamname** is MAR when it is analyzed with and only with **datacompleteness** column.

<iframe src="assets/missingness_tmnm.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/datacompleteness_perm.html" width=800 height=600 frameBorder=0></iframe>

### MCAR(Missing Completely at Random) ###
**Note that MCAR means that a column's missing values depend neither on other columns nor on missing values themselves**

* We perform permutation tests to check if **teamname** column depends on **position** column or not. 

* We get the p-value also by computing the proportion of simulated total variation distances from the permutation testing that are bigger than the observed total variation distance.

* After obtaining the our p-value for this permutation testing: 1.0, which is bigger than the significance level, we **fail to reject the null hypothesis** that In year 2022, distribution of **position** when **teamname** is missing is the same as when **teamname** is not missing. 

* The result of this permutation testing is that the missingness of **teamname** does not depend on **position** column, which means the missingness of **teamname** is MCAR when it is analyzed with and only with **position** column.

<iframe src="assets/missingness_pos.html" width=800 height=600 frameBorder=0></iframe>

<iframe src="assets/position_perm.html" width=800 height=600 frameBorder=0></iframe>


# Hypothesis testing #

Recall that we are interested in the following question: Which role “carries” (does the best) in their team more often: Top laners (top) or Mid laners(mid)?

## Null and ALternative hypothesis ##

**Null hypothesis**: Top laners carries(does the best) in their team **as likely as** the Mid laners(mid). Any observed difference in their respective likelihood of carrying the team is due to random chance.

**Alternative hypothesis**: Mid laners carries (does the best) in their team **more often than** Top laners.
* We set such an alternative hypothesis because from the boxplot shown above, we tend to favor a little more about the assumption that Mid laners might carry more than the Top laners because they have a higher overall carry score. Our alternative hypothesis also **avoids two-sided tests**.

## Choice of test statistics ##

We choose **difference in group means** between Mid laners and Top laners as our test statistics because the distribution of carry scores between Top laners and Mid laners are **roughly shifted versions of the same shape**.

<iframe src="assets/hypothesis_stat.html" width=800 height=600 frameBorder=0></iframe>

## Significance level ##

We choose the **0.05** significance level because it is widely used in many hypothesis testings.

## P-value ##

The p-value we get after running permutation testings: **0.00**

<iframe src="assets/hypo_test.html" width=800 height=600 frameBorder=0></iframe>

We calculate p-value by computing the proportion of simulated difference in group means from the permutation testing that are **smaller than** the observed difference in group means.

## Conclusion ##

We **reject the null hypothesis**, which suggests that Mid laners might possibly carry (do the best) in their team more often than Top laners. This rejection is based on our statistical analysis, specifically the calculation of the p-value, which is a measure of the likelihood of obtaining a result as extreme as, or more extreme than, the one observed if the null hypothesis is true. In our case, the obtained p-value is less than the commonly used significance level of 0.05 (0.0 < 0.05).

However, it is important to consider the **limitations** of our study, such as the specific context, population size, and potential confounding variables, when interpreting these results. Further research and analysis may be warranted to gain a deeper understanding of Mid laners' and Top laners' impact on team performance and about which position "carries" more.
