# League of Legends 2022 Data Analysis

By Ricky Zhu(r2zhu@ucsd.edu) and Tony Guo(xig003@ucsd.edu)


# Introduction
League of Legends (LoL) is a highly popular and competitive multiplayer online battle arena (MOBA) game developed and published by Riot Games. With a massive player base worldwide, LoL has become a staple in the eSports industry. Players assume the roles of powerful champions, forming teams and battling against each other in intense strategic matches. The game features a vast roster of unique champions, each with their own abilities and playstyles, providing endless possibilities for dynamic gameplay and strategic depth. 

Throughout our gaming experience League of Legends, we are interested on the question: Which role “carries” (does the best) in their team more often: Top (top) or Mid laners(mid)? 

To answer the question, we used the dataset contains contains 2022 League of Legends eSports match data from OraclesElixir, as it's representative for the League of Legends player population and meta for 2022. Also it provides valuable insights into the performance and dynamics of professional teams in the game for the future. Analyzing this dataset can offer a deeper understanding of the strategies, strengths, and weaknesses of different roles within a team.

By examining the match data from 2022, we can provide evidence-based conclusions about the impact and effectiveness of top and mid laners in carrying their teams to victory.
Maybe our conclusion about which position carries more can activate potential players to try playing that position or the specific champion/hero more often and even train themselves to be team leaders.


### 2022 League of Legends eSports match dataset breakdown(from OraclesElixir): ###


Number of rows: 149400

Meaning of each row: a single player's eSport match statistics in a single game


**Relevant Columns:**

1. datacompleteness: Whether a single player's statistics in a single eSport match is filled in original dataset before we did data cleaning. 

2. teamname: The name of the team the player is in.

3. position: The player's position in a single eSport match. League of Legends only has five positions: Top laners(top), Jungle(jng), Mid laners(mid), Bottom laners(bot), Support(sup). Note that in A League of Legends map, there are three lanes total and jungle areas. Top laner covers top lane, Mid laner covers mid lane, Jungle covers jungle areas, Support and Bottom laner cover the bottom lane.

4. kills: The number of kills a player have in a single eSport match.

5. assists: The number of assists a player have in a single eSport match.

6. death: The number of deaths a player have in a single eSport match.

7. earned gpm: Earned gold per minute by player in a single game. Gold in League of Legends is used to buy items in order to make your character stronger.

8. cspm: Creep score per minute, indicating the number of minions killed by a player per minute.



# Cleaning and Exploratory Data Analysis


## Data Cleaning

1. We create a copy of the original dataframe in order to keep original dataframe unchanged.

2. Each team has five players and a match contains two teams and so ten players. After examining the file, we find every 11th row and 12th row to be two teams' overall statistics and the ten rows before them to be these two teams' individual players' statistics of this match. As our goal is to examine individual performances of top laners and mid laners, we drop all rows of whole teams.

3. The 'datacompleteness' column contains three kinds of values: 'complete', 'ignore', and 'partial'.
We replace 'ignore' and 'partial' with False and 'complete' with True.
The boolean values make it easier to do analysis.

4. After examination of original file, we see tha the 'teamname' column initially have NaN values and 
also a kind of values called "unknown team". We replace them with NaN to clean the data.

5. kda_standardized: Kill Death Assist Ratio by a player in a single game, calculated by 
**(Kills + Assists) / Deaths**, and then standardized. Unstandardized **Kills**, **Assists**, **Deaths** are three columns we kept before. Refer to **Relevant Columns** section above.

6. gpm_standardized: Earned gold per minute by player in a single game, and then standardized. Unstandardized GPM is provided in original file directly.

7. cspm_standardized: Creep score per minute, indicating the number of minion killed by a player per minute, and then standardized. Unstandardized CSPM is provided in original file directly.

8. **carry_score**: For our analysis, we calculate a score for every player called 'carry_score'. It is our self-designed measurement of how well a player performs in a game, calculated by **(kda_standardized + gpm_standardized + cspm_standardized) / 3**. The reason why we use these three statistics is that these three factors impact the development of a League character the most. The higher the three statistics are, the better a player's possible performance would be in various aspects such as the amount of damage dealt or the quality of the player's items. Also, these three statistics are the most crucial factors in determining which player is the Most Valuable Player(i.e. MVP) in a single match.

9. We then drop the columns not in standardized units, specifically **kills**, **assists**, **deaths**, **earned gpm**, **cspm**


| datacompleteness   | teamname                 | position   |   standardized_kda |   standardized_gpm |   standardized_cspm |   carry_score |
|:-------------------|:-------------------------|:-----------|-------------------:|-------------------:|--------------------:|--------------:|
| True               | Fredit BRION Challengers | top        |          -0.696223 |           0.242621 |            0.563221 |     0.0365397 |
| True               | Fredit BRION Challengers | jng        |          -0.633408 |          -0.47272  |           -0.37376  |    -0.493296  |
| True               | Fredit BRION Challengers | mid        |          -0.507778 |          -0.242903 |            0.134244 |    -0.205479  |
| True               | Fredit BRION Challengers | bot        |          -0.759038 |           0.111582 |            0.506755 |    -0.0469004 |
| True               | Fredit BRION Challengers | sup        |          -0.675285 |          -1.45253  |           -1.57038  |    -1.23273   |

### Univariate histogram showing the distribution of the standardized KDA among all players. ###

The x axis shows standardized KDA in standardized units, ranging from -1 to 8, with a bin size of 0.5 standardized units. The y axis shows the amount of players with their specific standardized KDA.
The graph is skewed to the right, inferring that most players' standardized KDA is in the range of -1~0 standardized unit, which further means that a lot of players' KDA is relatively lower compared to the average value within the dataset. 

<iframe src="assets/univariate_kda.html" width=800 height=600 frameBorder=0></iframe>

### Bivariate boxplot showing information about carry score among all player positions ###

The boxplot shows various information of carry score such as median, interquatile range, and outliers. The x axis shows carry score in standardized units, while the y axis labels each of the five positions.

<iframe src="assets/boxplot.html" width=800 height=600 frameBorder=0></iframe>