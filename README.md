 
 
 
 
 
 
 
 
 
## NHL Goalie Analysis
Ryan Kelley

STATS 401: Applied Statistical Methods II

December 11, 2025
 
Word Count: 1486 words
 
 
 

 
## Background
The goal of this analysis is to create a model to predict the effectiveness of goalies in the NHL. Goalies have the most difficult and important job in hockey, so having a good one is very crucial to a team’s success. The data set used for this analysis is from naturalstattrick.com and includes data from the 2022-23 regular season through the 2024-25 regular season with a sample size of 196. The observational units of the data sets are given in rates per 60 minutes instead of total counts, because each observation has differing amounts of time and games played.
The variables in the data set are split up into 6 groups: basic information, standard saves stats, high danger saves stats, medium danger saves stats, low danger saves stats, and shot information. The basic information includes player name, team, games played, total time on ice (in minutes), and time on ice per game played (in minutes). The standard saves category include shots on goal against per 60 minutes, saves per 60 minutes, save % (shown as SV. in R), goals against per 60 minutes, goals saved above average per 60 minutes, which is the amount of saves made subtracted by the saves made by a goalie with the league average save %, and expected goals against per 60 minutes, which is an advanced metric computed by Natural Stat Trick predicting how many goals should have been allowed on a 60 minute average. The high danger, medium danger, and low danger categories include all the same stats as the standard saves, minus xG.Against.60, but are specific to high danger, medium danger, and low danger chances respectively. The shot information includes odd man rush attempts against per 60 minutes, which is the hockey equivalent to a fast break in basketball, rebound attempts against per 60 minutes, average shot distance (in feet), and average goal distance (in feet).
## Building the Model
**Initial Model**

The initial model included a response variable, xG.Against.60, three quantitative predictor variables, Shots.Against.60, Saves.60, and HD.Shots.Against.60, and one categorical variable, Team. Figure 1 shows a histogram of the response, as well as the six-number summary and the standard deviation. The histogram appears approximately normal barring a few potential outliers, so a log transformation is not necessary.
Figure 2 shows a scatterplot matrix with the response variable and three quantitative predictors. Shots.Against.60 appears to have the strongest positive relationship with the response, slightly edging out Saves.60. HD.Shots.Against.60 has the most moderate positive correlation with a bit more of an oval distribution than the other two. Shots.Against.60 and Saves.60 have a very strong positive correlation, so much so that there could be concern for overparameterization. HD.Shots.Against.60 also has a moderate positive correlation with both of the other predictors.
The model output is shown in Figure 3, along with the residual plot and Q-Q plot. The model has an RMSE = 0.2779 and an R-squared = 0.7949. The residual plot has significantly more points on the right side, meaning it is not reasonable to assume linearity, but the variance appears to be roughly equal above and below 0 on the residual plot, so it is reasonable to assume constant variance. The Q-Q plot has a strong deviation from the identity line on the left tail, but the center and right tail are both roughly along the identity line. At best, you could moderately assume normality, but that may be a stretch due to the deviation in the left tail.
Figure 4 contains the variance inflation factors of the three quantitative predictors, and there is overparameterization between Shots.Against.60 and Saves.60.

**Model Adjustments**

In the Project Checkpoint, an issue with the Team variable arose where goalies that played for multiple teams in the 3 season span would include all of the teams, separated by commas. This was an issue because it added additional “teams” that did not actually exist, but comprised multiple teams. To solve this problem, the CSV file was altered to split each observation into only their time with a single team, so the problematic goalies show up once per team they played for in the 3 year span. Another problem was goalies that hadn’t played enough time to register stats or have reasonable data points, hence the potential outliers in the initial model. To combat this, a subset of the data set was made called goalies_adjusted, including only goalies that had 200 minutes or more of total time on ice. The head of this new data set is displayed in Figure 5.
Figure 6 displays histograms of the three quantitative predictors to see if a log transformation appears necessary for any of the variables. All three histograms are approximately normal, so no transformations are needed. The response was already tested for a transformation earlier. Figure 7 shows the updated model and diagnostic plots, where the RMSE has already decreased to 0.13 and the R-squared has increased to 0.8762. On top of that, the assumptions of linearity and normality are much more reasonable based on the new residual plot and Q-Q plot respectively. As shown by the updated variance inflation factor in Figure 8, the problem of overparameterization still exists. To decide which variable should be removed, an adjusted R-squared comparison was done by modeling both Shots.Against.60 and Saves.60 against the response in Figure 9. The model including Shots.Against.60 proved to be the better model with an adjusted R-squared = 0.8417 compared to the Saves.60 model with an adjusted R-squared = 0.8351, so Saves.60 was removed from the model.
The next step was to decide if an interaction between Team and a quantitative variable would improve the model. Figure 10 displays a scatterplot of xG.Against.60 vs Shots.Against.60 by Team as well as a model output with Shots.Against.60 interacted with Team. It is difficult to decipher anything of importance from the scatterplot because there are 33 different teams in the data set, but the nested F-test in Figure 11 has a p-value = 0.2273, which is not enough evidence to support the interacted model given the simplicity of the original model. It also seemed unreasonable to include Team at all because each factor variable does not have at least 10 observations, causing the model to be overfit. For this reason, Team was removed from the model.

**Final Model Creation**

To create the best possible model given the data, each variable deserved a fair chance of inclusion. A correlation matrix was created in Figure 12 with all of the quantitative variables in the data set. To avoid overparameterization, any time two variables had a correlation greater than 0.8, the variable with a lower correlation to the response was removed. This process removed GP, SV., GSAA.60, HD.Saves.60, HDGSAA.60, MD.Saves.60, MDSV., MDGSAA.60, LD.Saves.60, LDSV., and LDGSAA.60. The final step was to use backward elimination maximizing adjusted R-squared at each step in Figure 13. This gave the 9 predictors that would make up the final model.

## Model Results
**Final Model**

The final model includes the following predictors: TOI.GP, GAA, HD.Shots.Against.60, HDSV., MD.Shots.Against.60, MDGAA, LD.Shots.Against.60, LDGAA, Rebound.Attempts.Against.60. With a sample size of 157 and 9 predictors, there is no overfitting. Figure 14 displays the scatterplot matrix of the response and predictors. The summary output in Figure 15 shows an RMSE = 0.096 and an R-squared = 0.918.

**Assumptions**

Also shown in Figure 15 are the diagnostic plots. The residual plot has a moderate random scatter and relatively constant width, reasonably meeting both the linearity and constant variance assumptions. The Q-Q plot has minimal deviation from the identity line throughout, making the normality assumption reasonable.

**Variance Inflation Factor**

The biggest flaw with the model is that it has some overparameterization. Even after eliminating similar predictors, GAA, HD.Shots.Against.60, HDSV., MDGAA, and LDGAA fail to meet the VIF threshold. However, The overall model has a p-value < 0.01, so there is very strong evidence that the model is statistically significant.

## Conclusion
The purpose of this project was to develop a model to forecast NHL goalies' efficacy, and it is reasonable to assume that goal has been achieved. The final model had a low RMSE, high R-squared, and very strong p-value. It also met all of the necessary assumptions and did not have any overfitting. It did however include some overparameterization, but the strength of the overall model remained strong. Ultimately, this model provides a reliable and analytically sound framework for forecasting NHL goalie performance, offering meaningful insight for both predictive evaluation and future decision-making.
 
 
 

