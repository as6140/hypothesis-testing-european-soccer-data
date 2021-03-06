# Hypothesis Testing European Soccer Data: Home Field Advantage, Optimal Formations, and Inter-League Core Attributes

by Alex Shropshire, Connor Anderson, Kevin Velasco

Non-Technical Business Presentation: Slideshow PDF attached in this repository

Slightly More Technical Blog Link: https://towardsdatascience.com/hypothesis-testing-european-soccer-data-home-field-advantage-ideal-formations-and-inter-league-8af2f6302995


![Map_European_Clubs](map_clubs.png)

Using the European Soccer Dataset from Kaggle (https://www.kaggle.com/hugomathien/soccer), an organization like ours could help improve club management, players development, player selection, on-field formations & game tactics, team-wide and league-wide core competencies, and transfer market decisions. 

Before exploring the dataset, we first formulated some initial questions to ask of the data. Does the traditional belief in the existence of an underlying advantage for European soccer teams playing at home have statistical significance? What formation has a better overall rate of victory, the 4–4–2 or the 4–3–3? When comparing the attributes of teams in English Premier League and the French Ligue 1, which league, on average, has the higher defensive aggressiveness? Which league, on average, has the greater ability to get players into a shooting position? Will our hypotheses tests yield valuable, statistically significant conclusions, or simply leave us with more unanswered questions?

### THE DATA ### 

The data is sourced from an SQL database. We created an entity relationship diagram of the data structure and found that the dataset had two subsets:  tables that contained real-life match data from leagues across the European continent alongside fictional statistics of players and teams from the soccer video game FIFA by Electronic Arts. 

Video game player attributes and player info was disconnected from real-world tables as these datasets did not share a primary key or a combination of matching features that could be used in place of a primary key. Rather than use SQL to join the tables together, we split our hypothesis tests into separate jupyter notebooks and maintained the original tables as pandas dataframes. Then, in each test, we performed the appropriate joins in pandas between tables of interest to stitch together the necessary data required for analysis. 

### METHODOLOGY ###
For the following tests, we first state our our null & alternative hypothesis, and then perform the necessary data exploration, data cleaning, feature engineering, and data organization. Then, we calculate the effect size (quantifying the difference between two groups) to help us determine the appropriate sample size. The powers of each hypothesis varies, but we maintain an alpha of 0.05 throughout each test. This alpha, or the significance level (α) is the probability of making the wrong decision when the null hypothesis is true. We wanted to keep this consistent while performing out various t-tests and the p-values generated from them. 

When testing for the differences between two groups, we can imagine two separate situations. In one, there is no real difference between the two groups. In the other situation, the mean difference between the two groups is not zero. For our purposes, we chose to run t-tests rather than z-tests. This is because we decided to interpret the entire dataset as a sample of the population of all soccer leagues. Without knowing all the top-flight league information, the population standard deviation is unknown, we can only use the t-test in this situation. 


### HYPOTHESIS TEST #1 Home Team vs. Away Team Win Rates ###

Is there a statistical difference in the odds of winning a game when a team is playing in front of their home crowd? The match table itself was the sole table of interest, so no joins were required here.  
- H0 (Null Hypothesis): there is no statistically significant difference in the odds of winning a game when a team is at playing at home vs. when a team is playing away.
- HA (Alternative Hypothesis): There is a statistically significant difference in the odds of winning a game when a team is playing at home vs. when a team is playing away. 
  
To aid with our analysis, we engineered new features creating booleans for when the home team won or the away team won, with 1’s being indicative of wins. Calculating the Cohen’s d between the means of the home win rate and the away win rate yielded ~0.359, which we then use to find the ideal sample size. With a power of .95 and an alpha of .05, we determined that we needed a minimum sample size of 202. In sum, these decisions help to achieve a balance in the tradeoff between a test's risk of returning a Type I error (rejecting a true H0 - a false positive) or a Type II error (failure to reject a false H0 - a false negative).

We then use the boostrap method to generate arrays of 1000 means of randomly selected samples between the home wins and the away wins. Bootstrapping allows assigning measures of accuracy (defined in terms of bias, variance, confidence intervals, prediction error or other such measure) to sample estimates. This technique allows estimation of the sampling distribution of almost any statistic using random sampling methods. This ensures our data for the 2-sample T-test is randomly collected, independent, and approximately normally distributed. Calculating the t-statistic, with a value of ~113.63, we were first alarmed that it might be incorrect. But, after calculating the difference between the home win rate (~0.459) and the away win rate (~0.287) to be ~0.171, it seemed to fit. With a final p-value of 0.0, we have the evidence within our sample to confidently reject the null hypothesis, and accept the alternative that a difference between the two means exists. 


### HYPOTHESIS TEST #2 4–4–2 vs. 4–3–3 Win Rates ###

For the second test, we wanted to continue utilizing real-life data provided before diving into the video game data, and explored the question of whether formation selection had a difference in a team winning a match.
- H0 (Null Hypothesis): There is no difference in win rate between the 442 and 433 formation
- HA (Alternative Hypothesis):There is a difference between the two win rates. X,Y starting positions of the 11 players were given in the matches table, along with unique team IDs for the home and away teams. 

The teams table had all the teams present in the dataset, and by joining them, along with some feature engineering, we were able to create a column that listed the winning team. To engineer the formation data, we had to first isolate the Y position of the starting squads, for it indicated how each player was positioned up and down the pitch. By utlizing an ordered counter to organize the Y values, we sucessfully created a column that listed formations. To give you an example, a '451' formation denotes four defenders, five midfielders, and one striker. 

Although there were many listed, we ultimately chose to run an analysis on two iconic and popular formations: the 4-4-2 and the 4-3-3. Traditionally speaking, Barcelona, Real Madrid, and Chelsea are a few of the many teams that run the more offensive oriented 4-3-3, while teams such as Manchester United and Aston Villa implement the more traditional structured 4-4-2. After identifying which mathches featured the two formations, we created two separate dataframes to split the two. Like in our first hypothesis, we engineered again a column again to indicate if a formation won or not. We use this to feed into a calculation of our minimum sample size needed to achieve a desired alpha level (0.05) and a desired power level (typically around 0.8). With a small effect size after calculating Cohen's d, to our surprise, due to a rather slim difference between sample means, the calculations indicate that we would actually want far more samples to utilize in our analysis than we have available. Because of this, the power of our test is significantly reduced in hypothesis tests 2, 3, and 4, increasing the risk of a Type II error. We moved forward in running the tests despite this fact, but a more ideal scenario would allow us to achieve a larger statistical power before we could conclude the tests with confidence. Bootstrapping the two samples, our 2-sample t-test gave us a t-statistic of ~152.46 with a p-value of ~0.00, and again we are able to reject the null hypothesis and accept the null hypothesis that there is a statistical difference between the two formations win rates.


### HYPOTHESIS TEST #3 Defensive Aggression Rating (English Premier League vs. French Ligue 1) ###

Is there a statistical difference in the defensive aggressiveness of teams in the English and French leagues. In order to create a dataframe for this hypothesis, we had to make several merge and joins in order to compare the leagues. We merged the match data with the league_ids and team attributes so that we were able to compare the attributes of the teams in both leagues. 
- H0(Null Hypothesis): there is no significant difference in the defensive aggression between the English and French leagues. 
- HA:(Alternate Hypothesis): there is a statistical difference in the defensive aggression between the English and French Leagues. 

In order to extract the data from the correct columns, we had to engineer booleans which would only take rows that were subject to each league. This allowed for easier exploration and helped facilitate the functions created. Calculating the Cohen's d between the means of our English and French leagues as 0.0698, this allowed us to choose our minimum sample size of 578 for England and 643 for France.

We used bootstrapping in this example as well, generating arrays of 50 means of randomly selected samples between the English defense and the French Defense. Bootstrapping allows assigning measures of accuracy (defined in terms of bias, variance, confidence intervals, prediction error or some other such measure) to sample estimates. This technique allows estimation of the sampling distribution of almost any statistic using random sampling methods. This ensures our data for the 2-sample T-test is randomly collected, independent, and approximately normally distributed. The test resulted in a calculated T-Value of 11.38, and a final p-value of 1.239375501089002e-19. According to our p-value, we will reject the null hypothesis, accept the alternative hypothesis and conclude that there is a statistical difference in the defensive aggression of teams from the English and French leagues. The caveat with our test is that we can only claim our conclusion with a power of 23%, this was in part because the data set we were given was too small and within the time constraints of our project we were unable to collect more. 
