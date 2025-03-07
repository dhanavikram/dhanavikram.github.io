---
layout: page
title: Graduate Admission Chances
description: Analysis and Prediction of Admission chances for Graduate education in the US.
img: https://www.linkforcounselors.com/wp-content/uploads/2015/10/zkozzi-17395759-high_school_graduates_students-832x624.jpg
importance: 6
category: 
---

Find the full work [here](https://github.com/dhanavikram/Graduate-Admission-Chances).

According to the annual [Opendoors](https://opendoorsdata.org/annual-release/international-students/#fast-facts) report, the total number of international students moving to the US for their education increased by 12 percent in the year 2022-23. To successfully complete the application process for a university, an international student must have their exam scores (GRE and TOEFL), a good CGPA standing, a Statement of Purpose and a few Letters of Recommendation. Other than this, each person’s probability of getting into a university depends on the university’s ranking as well. As a graduate international student, I have always wondered if there is a way to find out my chances of getting into specific universities given my profile. This research delves into prediction and analysis of one's chances for getting into a university using the data by Jamboree education in Kaggle at the same time answers some interesting questions that graduate students might have.

## Analyses, Methods, and Results

### Data Source and Collection

The data is collected by [Ranit Sarkar](https://www.kaggle.com/ranitsarkar01) and is published in the Kaggle platform as a non-commercial open source dataset in CSV format - [Link](https://www.kaggle.com/datasets/ranitsarkar01/jamboree-linear-regression-dataset/data).

### Previous Work

A lot of people tried different linear regression models and regularization techniques using this data in the dataset's code page. But only one analysis by Sneha Bajaj involved statistically testing for the assumptions of linear regression before applying it. Even then the owner of the work has ignored some of the key points in her work and made two mistakes in testing the assumptions of linear regression. This work aims to correct those mistakes and proposes another generalized linear model approach.

### Exploratory Data Analysis

The dataset has 7 features and one response variable.

- GRE Score
- TOEFL Score
- CGPA
- SOP rating
- LOR rating
- University rating
- Research
- Chance of Admit (Response variable)
  
On analyzing the distribution of each of the features using boxplots and histograms, it was discernible that the continuous columns (GRE, TOEFL, CGPA, and Chance of Admit) are almost normally distributed. The ordinal columns are also normally distributed with very few samples having low and high ranking/scores (SOP, LOR, Research, and University Rating). 

The results of Bivariate analysis using the `pairs` function reveal the presence of a linear relationship between the continuous features and the response variable. While the categorical/ordinal features also show a linear upward trend, their variance is too high to consider that relationship as strictly linear. Hence a correlation plot was used. Almost all of the features seem to have a positive correlation with the chance of admission. The linear relationship between the features and the response shows that a linear regression model might be a better fit for this problem.

### Linear Regression and Model Diagnostics

Since the outcome is probability, it is plausible to use logistic regression to find out the outcomes in terms of probabilities. However, as all of the R's inbuilt methods require binary classes in the response variable, this initial approach attempts to use linear regression to regress the probabilities and uses a ReLU-like function(that returns min(1, probability)) to cap the regression output at 1.

Once the model is fit, the residuals of the model are used to check for the assumptions of linear regression. The major assumptions of linear regression are:

- Linearity of the response 
- Independence of features (no multicollinearity)
- Normality of residuals
- Constant Variance of residuals (Homoscedasticity)
- Independence of residuals.

Visual plots are used to diagnose each of the assumptions in the *Diagnostics of Linear Model and Residuals* section. 

1. **Linearity** is checked using the Fitted vs Residual plot. The presence of a linear line in the fitted vs residuals plot indicates the presence of a linear relationship between the predictors and the response.

2. To check for **Constant Variance**, The Fitted vs Residual plot was used in conjunction with the Breusch-Pagan test. The latter involves estimating the squared residuals from the original regression against the predictors.
    
    **Null Hypothesis**: Variance of errors is constant across observations.\
    **Alternate Hypothesis**: Variance of errors is not constant across observations.
    
    This test resulted in a p-value less than 0.05 (significance level). Hence the variance is not constant throughout the fitted values.

3. Similarly to check for **Normality of Residuals**, the QQ plot and Shapiro-Wilk test are used. The QQ plot suggests a significant deviation from the normal distribution. In the Shapiro-Wilk test,
    
    **Null Hypothesis**: Residuals are normally distributed.\
    **Alternate Hypothesis**: Residuals are not normally distributed.

    The test resulted in a p-value significantly smaller than 0.05(significance level). Hence we reject the null hypothesis.

4. Successive residual plot and Durbin-Watson test have been used to check for **Independence of Residuals**. The plot shows that the residuals are uncorrelated. For the Durbin-Watson test,

    **Null Hypothesis**: Errors are uncorrelated.\
    **Alternate Hypothesis**: Errors are correlated.

    Although the p-value is higher than the significance level (0.05), if we look at the autocorrelation value, it is closer to 0. Hence, we can say that the residuals are uncorrelated with each other.

5. Multicollinearity between features has been checked using the Variance Inflation Factor(VIF) and correlation matrix. On computing the VIF, it was found that all the features have a VIF score of less than 5. However, from the correlation matrix, it is discernible that there is a high correlation between some of these features (scores especially) and the CGPA feature.

Since two of the five assumptions of Linear Regression have failed, it can be concluded that the Linear Regression model is not the right fit for this data.

### Generalized Linear Models - Logistic Regression

Since the assumptions of the linear model have failed, a binomial regression model has been used to better fit the data. 

### Data Preparation

The probability of each person’s admit probability has been converted into binary classes such that the applicant whose probabilities are higher than 0.5 will be in class 1 and the other applicants will be in class 0.

### Diagnostics of Logistic Regression

Similar to linear regression, logistic regression has its own set of assumptions that need to be satisfied. They are:

- Appropriate outcome types
- Linearity of independent variables and log odds
- No strongly influential outliers
- Absence of multicollinearity
- Independence of observations
- Sufficiently large sample size

Unlike linear regression, logistic regression does not assume the normality of residuals and homoscedasticity, which makes it the perfect model for this problem.

On assessing each of these assumptions individually, it was found that the data follows all the required assumptions. Hence a binomial model was fit.

### Feature selection for logistic regression

First, a logistic regression model was built on the entire set of features. Then R’s inbuilt stepwise backward selection method was used to reduce the number of features and select the ones that yield the best AIC. The resultant model had 4 features instead of the initial 7.

### Goodness of fit

For assessing the goodness of fit of the reduced model and the full model, the Likelihood ratio test was used in which,

**Null hypothesis**: Reduced model is sufficient\
**Alternate hypothesis**: Reduced model is not sufficient.

The results of this test show that the reduced model is sufficient.

### Interpreting the weights

Out of the selected four features. CGPA has the highest weightage followed by the Letter Of Recommendation rating. The model suggests that features like SOP and Research experience do not have significant weights in predicting one’s probability of getting into a university. But is this correct? To find out if features like Research experience have significance in predicting one’s chance of getting into a university ANOVA technique has been used in the next section. Another notable finding was that the GRE score does not have a significant weightage or barely any weight in predicting one's chance of getting into a university.

### Prediction accuracy

Both the full model and the reduced model chosen via a stepwise backward selection of features are used to predict the estimated admit for the test dataset (20 percent of the entire data). The result shows that both models predict with the same accuracy, precision, and recall. This allows us to confirm that the reduced model is sufficient even for predictions (not just for explainability).


### ANOVA (One way F-test)

One of the research questions that has always been on top of several applicant’s heads is “Does research experience improve the probability of getting admitted into a university?”. To answer this question and the question from the previous section with statistical proof, the ANOVA technique has been used. The data is divided into two groups, one with research experience and another with no research experience. Their distributions are compared using the ANOVA F-Test method in R. The p-value of less than 0.05 (significance level) suggests that the average probability of the two groups is significantly different from each other. To find how distant they are from each other, Tukey’s Honestly Significant Difference Test has been used. The result of the test shows that the group with research experience has a higher probability of getting into a university with an average difference of 0.149 and a 95 percent confidence interval of (0.12,  0.17). 

### Hypothesis Testing (t-test)

Another interesting question that can be answered using the data is regarding the relationship between TOEFL scores and SOP ratings. "Do applicants who score high in TOEFL score write good SOPs?". To answer this question, the applicants are categorized based on their TOEFL score. People who score above 100 are chosen as high scorers and others are categorized as low scorers. The score 100 is chosen as the cutoff because a lot of high-ranked universities have their required minimum TOEFL scores in the range of 100 to 110, while most of the US universities have their minimum requirement as 90. 

After categorizing, the hypothesis is formulated as follows,

**Null Hypothesis**: There is no significant difference in SOP ratings between students with high TOEFL scores and students with low TOEFL scores.\
**Alternate Hypothesis**: There is a significant difference in SOP ratings between students with high TOEFL scores and students with low TOEFL scores.

Then, a standard t-test was conducted to compare the two groups using their average SOP ratings. At first, the assumptions of t-test are verfied. It showed that out of the two groups, one group is non-normal. However it is known from [Reference 6](https://thestatsgeek.com/2013/09/28/the-t-test-and-robustness-to-non-normality/) that if the sample size is greater than 30, it is safe to conduct t-test on a non-normal data. And for the next assumption, levene's test is conducted to check for equal variances in the two groups. The results showed that the groups have equal variance. Hence a t-test was conducted. To double verify on the results, Kruskal-Wallis test was conducted on the group and the results was similar. The answer was as expected. The results of the t-test and Kruskal-Wallis test show that the groups are significantly different with a p-value less than 0.05 (significance level). The data confirms that people who score high in TOEFL have high SOP ratings. The applicants with high TOEFL scores have an average SOP rating of approximately 3.5 while those with low TOEFL scores have an average rating of 2.36.

## Conclusion

Based on the weights of the binomial model, it was evident that CGPA plays a major role in one's admission process, while GRE score does not play that much of a role in the admission process. Thus every applicant whose aim is to get admitted into a US university should focus more on getting a good CGPA that would allow them to become a competitive applicant in the present landscape.

In the future, more independent features such as years of relevant work experience, majors, etc. could be added to the model. And since nowadays GRE scores are not required by a lot of universities, features such as the GRE score can be dropped.

## References

1. Class notes, Slides and Notebooks.
2. [Prediction of Chances of Graduate Admission (Linear Regression) by Sneha Bajaj](https://jovian.com/snehabajaj108/jamboree-linear-regression)
3. [STAT - 462, Lesson 12 - Logistic Regression](https://online.stat.psu.edu/stat462/node/207/)
4. Stoltzfus JC. Logistic regression: a brief primer. Acad Emerg Med. 2011;18(10):1099-1104. doi:10.1111/j.1553-2712.2011.01185.x
5. [Assumptions of Logistic Regression, Clearly Explained](https://towardsdatascience.com/assumptions-of-logistic-regression-clearly-explained-44d85a22b290)
6. [The t-test and robustness to non-normality](https://thestatsgeek.com/2013/09/28/the-t-test-and-robustness-to-non-normality/)
