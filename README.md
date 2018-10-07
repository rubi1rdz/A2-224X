# A2-224X
Linear regression model

Q: List your chosen drivers with a brief description of why you selected each. You are asked to give them in order of your guess of most correlated (Driver 1) to least correlated (Driver 5) with TOTALBTU. Please commit to responses to this question before moving on; you may end up changing your chosen drivers, but the point of this question is to test your intuition.

Driver 1:NUMBERAC (This is the number of AC units, I think it is important since HAVC systems account for the greatest percentage of energy consumption in buildings, followed by lighting and plugloads)
Driver 2: WASHLOAD (This is the frequency for using the washing machine per week. I consider the washing machine to be one of the appliances in a home that uses the most of energy. Plus, it is within the plugloads category that I consider important)
Driver 3:AIRCOND (This is whether a home has AC or not. I consider this to be an important driver of energy consumption given my explanation for driver 1, however I considered 1 to be more important given the level of granularity of the data. Drivers 3 & 4 only have 2 levels)
Driver 4:HEATHOME (Same as Driver 3)
Driver 5:LGTINLED (This is the percentage of the lights that are LED. Given the contribution of lighting to the total energy consumption in buildings )
Driver 6: (additional driver) (Cooling Degree days at 65F. I just wanted to try it, given HDD65 is a key variable used to model energy consumption in buildings)

All the measures above where chosen taking into consideration the feasibililty of retrofits in existing buildings as well as the possibility of implementing a new policy to regulate the variables that are found to contribute the most to energy consumption in our model

Q: Interpret the results of your correlation analysis. Identify and discuss the 2-3 strongest and weakest correlated pairs that include the outcome-of-interest, TOTALBTU, specifically any surprises.

Strong 1: TOTALBTU & HEATHOME (0.41) (this potentially means that the most amount of energy comes from heating)
Strong 2: TOTALBTU & NHSLDMEM (0.38) (it makes sense that the more people in a home, the higher the energy consumption)
Strong 3: TOTALBTU & WASHLOAD (0.34) (this seems somehow correlated with the number of people in the house, meaning the use of washing machine is more frequent)

Weak 1: TOTALBTU & CDD65 (-0.24) (Negative correlation, which not that intuitive, statistically it means that as cooling increases, total btus decreases)
Weak 2:TOTALBTU & NUMBERAC (0.07)( This is close to 0, which means no correlation, which means that number of AC units has almost no effect on totalBTUs
Weak 3: TOTALBTU & LGTINLED (0.11) (this means that LEDs are not as impactful to energy consumption )

Then identify and discuss the 2-3 strongest correlated pairs that don't include the outcome-of-interest (like MONEYPY vs. HDD65), and how these correlations may affect our regression analysis.

Strong 1: CDD65 & HDD65 (-0.67) (a strong negative correlation which makes sense given that the more the heating days the less the cooling ones)
Strong 2:WASHLOAD & NHSLDMEM (0.44) (This is as expected, the more people that live in a house, the more the washing machine gets used during the week)
Strong 3:HEATHOME & HDD65 (0.42) (this means that the more heating days on a day, the more people will be likely to have a heating system installed at their home)

Q: Discuss the results of your scatter plot and the goodness of fit of the trendline. Note if there are any outliers of interest.


The plot tells us that for the homes that do not have a heating system, the energy consumption ranges from 7,381.82 BTUs to 40,401.88 BTU , while for those with heating installed the energy consumption goes from 19,006.8 BTUS to 153,823.2 BTUS. 

The trendline has an equation of TotalBTUs = 23,940 + 40,275 * HEATHOME, this only applies for the mean of both categories. if HEATHOME is 0, the equation gives us the mean of BTUS the cluster of HEATHOME = 0 ("minbtu") whereas if HEATHOME is 1, we get the mean of "highbtu", which is the cluster of HEATHOME = 1.

As for outliers, we do a boxplot and can see that there are 5 outliers. We calculate get the outliers by filtering the values in the upper cluster that are greater than 2.689 standard deviations (Q3 + 1.5IQR). We fit a linear model for the dataframe of the outliers. We can see that based on the Pr (>|t|)the main drivers are NHSLDMEM, HDD65, MONEYPY and NUMFRIG. For instance, a quick look at the quartiles of MONEYPY, tells us that the values for the outliers are in the 4th quartile which means high level of income. 

Q: Discuss the difference between your single-predictor and multiple-predictor regression models. In particular, describe how the coefficient of your first predictor (from the single-predictor model) changed once you “controlled” for other variables. Finally, discuss any other results you thought were insightful.

Definetelly, a single predictor misses all the correlations between multiple variables. We can see how the single predictor model gave a high weight to HEATHOME (Pr (>|t|) = 1.11e-05) while for the multivariable predictor HEATHOME was in 5th place in terms of significance to the model (Pr (>|t|) = 0.06265), more significant parameters were NHSLDMEM, followed by HDD65, NUMFRIG and MONEYPY. In addition to that, we can see that the R2 of the single variable model (0.1608) is less than that of the multivariable model (0.3326), which tells us that the latter is a more complete model for our study.

Insightfull enought is that after cleaning the data (removing the "-2") my datapoints were reduced from 882 observations to 107, which tells the importance of gathering complete data. In addition to that, I somehow thought that number of AC units would have a greater impact on energy consumption that anything related to heating, which I can say I am biased since where I live in an area where we dont need much heating but more AC. For this same reason, I didnt choose many parameters related to heating, and now I can see how my biases can affect my input data which could hinder the selection of better parameters to fit a more accurate model. 

Single Variable LR
lm(formula = TOTALBTU ~ HEATHOME, data = recsV7)

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)    23940       8222   2.912  0.00439 ** 
HEATHOME       40275       8726   4.616 1.11e-05 ***

Multiple R-squared:  0.1687,	Adjusted R-squared:  0.1608 


Multivariable LR
lm(formula = TOTALBTU ~ NHSLDMEM + MONEYPY + HDD65 + NUMFRIG + 
    NUMBERAC + WASHLOAD + LGTINLED + HEATHOME + AIRCOND + CDD65, 
    data = recsV7)

Coefficients: (1 not defined because of singularities)
              Estimate Std. Error t value Pr(>|t|)   
(Intercept) -1.446e+04  1.520e+04  -0.951  0.34385   
NHSLDMEM     5.236e+03  1.708e+03   3.065  0.00282 **
MONEYPY      1.226e-01  6.132e-02   1.999  0.04844 * 
HDD65        5.650e+00  2.405e+00   2.350  0.02081 * 
NUMFRIG      8.321e+03  3.856e+03   2.158  0.03341 * 
NUMBERAC    -3.324e+03  3.699e+03  -0.899  0.37105   
WASHLOAD     1.180e+03  1.098e+03   1.075  0.28496   
LGTINLED     3.168e+02  1.757e+02   1.803  0.07442 . 
HEATHOME     1.949e+04  1.045e+04   1.865  0.06525 . 
AIRCOND             NA         NA      NA       NA   
CDD65        1.814e+00  3.668e+00   0.495  0.62201 

Multiple R-squared:  0.3893,	Adjusted R-squared:  0.3326 
