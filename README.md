# USL Player Analysis
## 1. Executive Summary
### Problem
Oakland Roots needs help recruiting talented soccer players to their team. In particular, they are looking to find players with skillsets lacking in the Roots team and at an affordable price. 
### Solution
With data sourced and scraped from soccer statistics websites, we have provided a comprehensive analysis/plan of attack for Oakland Roots. We delve into player statistics, team dynamics, field layouts, and feature/skillset importance to make better-informed recruitment decisions.
### Highlights 
Using various analytic techniques, we uncovered resources & statistics that provide the Oakland Roots team strategies to recruit talent. These include 3 main categories 
- Talent-based recruitments (based on specific features of desired players)
- Similarity-based recruitment (comparing to other players)
- Team-based recommendations & blindspots (based on what team dynamics lead to winning championships).
- More specifically, this breaks down into these loosely-grouped analytic categories:
    - Undervalued Players (Market Value Model)
    - Similar Players (Cosine Similarity)
    - mportant Features of Valuable Players (Random Forest)
    - Important Features Per Position
    - Team-by-Team Comparisons
    - Statistics by Features
    - Player Country of Origin (Choropleth)
    - Radar Plots Comparing Most Similar Players
## 2. Data Retrieval
### Databases 
#### Wyscout (2020 & 2021) 
The Wyscout dataset is retrieved from the Wyscout data platform as provided by the client. Wyscout is the largest soccer data database on the internet. It compares players in the USL League One — ranking players by shots, crosses, & successful tackles in the USL Championship Games. 
#### American Soccer Analysis Dataset
The American Soccer Analysis (ASA) dataset is downloaded to visualize the data. The dataset contains three separate datasets of xGoals, xPass, and Goals Added (g+). The metric xGoals (xG) assesses the probability (%) of any shot scoring (a goal). Specifically, it quantifies the difficulty of a scoring shot using various predictive metrics — players' goals, key passes, & assists. Likewise, the metric xPass (xP) assesses the probability (%) of a pass being successful (making it to a teammate).
### Webscraping
#### TransferMarkt
Transfermarkt is a website where anyone can look up a soccer team with player statistics, game stats, & a player's market value ($). 
#### Sofa Score
The SofaScore ratings for 2020 and 2021 were scraped from the site. This dataset was merged with Wyscout to construct feature selection, ratings, and a salary model.
## 2. Data Processing
### Position Categorization
Owing to the complexity of the datasets in this project, biases afflict the descriptions of players' positions from different sources. Moreover, stats on positions will also differ due to the opportunities different positions are presented with. Offensive players, for instance, will have more goal-scoring opportunities whilst defensive players rank higher in deep passes. So — categorizing by position is inherently valuable. Nonetheless, it makes sense to aggregate players in similar positions so that the categories are more meaningfully predictive when we conduct machine learning.
### Data Consolidation
In order to make the data better serve the subsequent progress, we decided to merge the Rating column of the data from the Wyscout source with the data from the SofaScore source via the Player column. In order to make the data better serve the subsequent progress, we decided to merge the Rating column of the data from the Wyscout source with the data from the SofaScore source via the Player column. However, since the player names of the SofaScore source data are fully spelled, they do not match the Wyscout data. We match by constructing a regular expression that keeps the player's last name and replaces the player's first name with an acronym. Player names with middle names or special characters that are not alphabetic are matched using manual substitution.
## 3. EDA
Using Python package ProfileReport to generate EDA report.
## 4. Data visualization Overview
![image](https://github.com/jdenggao/USL-Player-Analysis/assets/112433825/aaf5ff9c-b553-4f94-b7b1-40aea3d5f0a5)

![image](https://github.com/jdenggao/USL-Player-Analysis/assets/112433825/1d24098a-1089-4fb3-93ba-0cfbb13fb642)

![image](https://github.com/jdenggao/USL-Player-Analysis/assets/112433825/ee3f3330-99b3-4708-9b6c-7ad4ee70f4a8)

## 5. Features selection
In our models, we took the counsel of our advisor as to what features mattered most by position. We compiled it into the table below. Given these groupings, we used each feature’s perceived importance as weighted values for predictive models of skill set. 
<br>
To be able to further select the important features from the features of each position above, we decided to build a random forest model with the features of each position above as the independent variable and 'Rating' as the dependent variable.
```
  forest = RandomForestClassifier(oob_score=True, n_estimators=100, random_state=100, n_jobs=-1)
  forest.fit(x_train, y_train)
  
  # select features which threshold larger then 0.025
  selector = SelectFromModel(forest, threshold=0.025)
  features_important = selector.fit_transform(x_train, y_train)
  model = forest.fit(features_important, y_train)
  selected_features = model.feature_importances_
```
Following two plots are for the feature selection on the Fullback position. Fullbacks are responsible for defending both sides of the field. Their task is to prevent the opponent from passing the ball from the sideline into the penalty area, and they also help make passes for attacks. In the scatter plot, the important features of a FB are key passes per 90, duels per 90, and shots blocked per 90. These are all essential features for FB to prevent opponents from passing the ball, creating a chance for goals. There are also box and violin plots of the features to show the min, median, max, 0.25, 0.75, upper max, and outliers for each of the features.

![image](https://github.com/jdenggao/USL-Player-Analysis/assets/112433825/1336dc8e-ad65-48a3-9d74-99a12b59c88c)

![image](https://github.com/jdenggao/USL-Player-Analysis/assets/112433825/c141fbd4-1b3a-4386-8ccb-fd38a1ea38f5)

## 6. Cosine distance
The client provided us with four ideal players (Reggie Cannon, James Sands, Ronaldo Damus, and Cole Bassett). When planning a player acquisition, these players have the statistics the client thought would best match his preference for these four positions (LB/RB, CB, FWD, and AM). These four positions were categorized into broader categories in previous steps (CB, CF, and FB). To locate other players similar to these five players, we calculated the cosine distance between the ideal players and similar players.
<br>
By extracting the model player's statistics, and the essential features from the positions data generated by the RF model. The dataset was normalized and separated the data into x, all the players' features, and y, the player's name. Next, we calculated the cosine similarity between the players. The angle between the players will give the percent similarity between the ideal player and comparable players. We then use cosine distance (1-cosine similarity) to determine the distance between the two players. If the players' cosine distance is closer to 0, the two players are alike.
<br>
Players with more significant cosine similarity and the least cosine distance are the top similar players.
<br>
To enable our clients to compare all players, we have implemented this feature in Dash. The client can select the position of the player he wants to search for and then select the two players he wants to compare to get an idea of the player. This Dashboard can be viewed in a Jupyter notebook.

![image](https://github.com/jdenggao/USL-Player-Analysis/assets/112433825/1df039a7-6303-405f-8c4a-23022d3ad3cd)

## Detail Report 
[Capstone Project Final Report Root Team.pdf](https://github.com/jdenggao/USL-Player-Analysis/files/12600559/Capstone.Project.Final.Report.Root.Team.pdf)
