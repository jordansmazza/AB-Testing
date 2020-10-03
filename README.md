# A/B Testing
### Table of Contents
- [Introduction](https://github.com/jordansmazza/AB-Testing/blob/master/README.md#introduction)
- [Probability](#probability)
- [A/B Test](#ab_test)
- [ Regression](#regression)
## Introduction

> This repository consists of a series of probability and A/B tests. The main dataset used in this project shows the results of an experiment run by an e-commerce company on their website. The company wants to implement a new web page to see if it leads to more users paying for the companies products (`converted`). Users were split into two groups (`control` and `treatment`). User's in the control group saw the current webpage (`old_page`) and users in the treatment saw a new web page (`new_page`). The company wants to know if they should implement the new page or keep the old page based on the conversion rate each page gets.
>**Disclaimer**: This data was taken from a school project, the data used is not from a real e-commerce company.
---
#### Probability
The first part in the analysis is running some probability functions to help get familiar with the data and see if there are any trends we can identifity just through these tests. Basic functions such as `.head()`, `.shape`, and `.unique()` were used to examine the dataset. After this, some data cleaning was done by checking if there were any iregularities such as if a user was said to be in the treatment group but was given the old page. These rows, as well as any duplicated user_ids were discarded. Some functions were run to find the probability of conversion under each page (see below for example):
```df2.query('group == "control"')['converted'].mean()```
```df2.query('group == "treatment"')['converted'].mean()```
The output for these queries were incredibly similar, sugesting there is no significant different between each page.

---
### Part II - A/B Test
#### Hypotheses:
**Null: The new page is equally effective as the old page at causing conversions.**
$$N_{o}$$: $$p_{new}$$ = $$p_{old}$$

**Alternative: The new page is not equally as effective as the old page at causing conversions.**
$$N_{1}$$: $$p_{new}$$ != $$p_{old}$$

To test these hypothesis, two randomly simulated draws (one for each group) using `np.random.choice()` were run using the conversion rate and group size. This random simulation was then repeated 10,000 times suing a for loop. The results were then displayed via histogram using `plt.hist()`.

A p-value of 0.907 was then computed by using the average conversion rates from the simulations and the actual conversion rate. The p-value is the probability of observing the statistic or something more extreme in favor of the alternative hypothesis given the null hypothesis is true. The high p-value shows it is likely this statistic falls under the null hypothesis, meaning we will most likely fail to reject the null hypothesis. This is consistent with the previous probability findings showing the probabilities of a user converting under each page were incredibly similar.

A similar test was run using `statsmodels.api` to cooborate the previous reuslts as well as find the z_score for this data. The p-value found was 0.905 while the z_score was -1.31. The p-value found using this method is near identical to the p-value found earlier. The z-score is is negative, which shows the statistic lies below the sample mean. The z_score also is around 1 standard deviation from the sample mean. This z-score combined with the high p-value leads further shows we will fail to reject the null hypothesis.

---
### Part III - A Regression Approach
**Since there is not a linear, or continuous, outcome (the outcome is one of two options), we will be using Logistic Regression for this analysis.**

An intercrept and dummie variable was added to the dataset to prepare for the logistic regression model. In this analysis, the location `old_page` was used as the baseline. The p-value for this regression model is 0.189, which does not match with the p-value found earlier in this project. This is because in this regression model, the hypotheses are slightly different. The null hypothesis earlier states the new and old page are equally likely to lead to conversions and the alternative hypothesis states the new and old page are not equally likely to lead to conversions. 

In this regression model, the null hypothesis being tested states the page a user receives (ab_page) does _not_ influence conversions and the alternative hypothesis states the page a user receives does influence conversions. While these are similar hypotheses, they are not the exact same and therefore, it makes sense the p-value calculated here is different than the p-valaue from earlier.

At this stage in the anlysis, the e-commerce company would also like to see if location plays any role in the results of this experiment. It can be advantageous to add more variables to a regression model because it will show if there are any confoudning variable in the data that are influencing the results. This, however, is a double-edged sword because adding more variables can also decrease the visibility of significant effects because the variables added to an analysis, the less our degrees of freedom, or values that have the ability to vary or change.

In order to test if location has a significant effect on conversions, a dataset with users locations have been added to the project. Once again, a dummie variable was used to prep for the regression model. In this analysis, the location `CA` was used as the baseline. Both location and landing page were added to this model.

From the summary of the model, the difference in conversion  based on the user’s country is incredibly minimal. If a user location is `UK`, the conversion rate increases by 0.0507 each unit in comparison to our baseline. for users in `US`, the conversion rate increases by 0.0408. The p-values for these results also suggest there is no statistical impact on conversion rate based on the user's country because they are both greater than 0.05.

Finally, a third regression model is used to test for an interaction between the landing pade and a user's location. The results of this final test shows that there is no significant interaction between the user's location and the page the user received. Both the `ab_page_us` and `ab_page_uk` variables vary minimally from the baseline. This is further confirmed by looking at the p-values for both variables as they are both greater than 0.05.