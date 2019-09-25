---
title: Logistic Regression
author: wyih
date: '2019-06-26'
slug: logistic-regression
markup: mmark
categories:
  - Stata
tags:
  - Stata
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T17:03:59+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
## Odds ratios
An [odds ratio](https://en.wikipedia.org/wiki/Odds_ratio) of 1.0 is equivalent to a beta weight of 0.0.

| Group       | Diseased | Healthy |
| ----------- | -------: | :-----: |
| Exposed     |    $D_E$ |  $H_E$  |
| Not exposed |    $D_N$ |  $H_N$  |


$OR={\frac {D_{E}/H_{E}}{D_{N}/H_{N}}}$

The distribution of the odds ratio is far from normal. Take the natural logarithm of the odds ratio to get normal.

$logit = ln(OR)$

When the mean is around 0.50, the OLS regression and logistic regression produce consistent results, but when the probability is close to 0 or 1, the logistic regression is especially important.

## Logistic regression
The `logit` command gives the regression coefficients to estimate the logit score. The `logistic` command gives us the odds ratios we need to interpret the effect size of the predictors.

Both commands give the same results, except that `logit` gives the coefficients for estimating the **logit score** and `logistic` gives the **odds ratios**.

The McFadden pseudo-$R^2$ represents how much larger log likelihood is for the final solution.
, meaning the log likelihood for the fitted model is 2% larger than for the log likelihood for the intercept-only model. 
This is not explained variance. The pseudo-$R^2$  is often a small value, and many researchers do not report it. The biggest mistake is to report it and interpret it as explained variance.

If you are interested in specific effects of individual variables, it is better to rely on odds ratios for interpreting results of logistic regression. ~~This shows that mothers who smoke have 2.02 times greater odds of having a low-birthweight child.~~

**Odds ratios** tell us what happens to the odds of an outcome, whereas **risk ratios** tell us what happens to their probability.

For binary predictor variables, you can interpret the odds ratios and percentages directly. For variables that are not binary, you need to have some other standard. One solution is to compare specific examples, such as having no dinners with the family versus having seven dinners with them each week. Another solution is to evaluate the effect of a 1-standard-deviation change for variables that are not binary.`listcoef`,get from package `spost13`. After logit/logitstic regression, run `listcoef, help`or  `listcoef, help percent` 

|     Group    | Experimental (E)   |  Control (C)  |
| --------   | -----:  | :----:  |
| Events (E)     | EE |   CE     |
| Non-events (N)       |   EN   |   CN   |


$ RR={\frac {EE/(EE+EN)}{CE/(CE+CN)}}={\frac {EE(CE+CN)}{CE(EE+EN)}}. $
相对风险是指在暴露在某条件下，一个事件的发生风险 
`oddsrisk`
$OR={\frac {EE/CE}{EN/CN}}={\frac {EE\cdot CN}{EN\cdot CE}}$
一个事件发生比是该事件发生和不发生的比率 
Risk ratio is different from the odds ratio, although it asymptotically approaches it for small probabilities of outcomes. If EE is substantially smaller than EN, then EE/(EE + EN) $ \scriptstyle \approx $ EE/EN. Similarly, if CE is much smaller than CN, then CE/(CN + CE) $ \scriptstyle \approx $ CE/CN. 
$ RR={\frac {EE(CE+CN)}{CE(EE+EN)}}\approx {\frac {EE\cdot CN}{EN\cdot CE}}=OR. $

The difference is small with a rare outcome.The relative risk is appealing, but it should not be used in a study that controls the number of people in each category.




## Hypothesis testing

chi-squared test that has  k degrees of freedom, tells us only that the overall model has at least one significant predictor.
### Testing individual coefficients
The z test in the Stata output is actually the square root of the Wald chi-squared test.

The likelihood-ratio chi-squared test for each parameter estimate is based on comparing two logistic models, one with the individual variable we want to test included and one without it. The likelihood-ratio test is the difference in the likelihood-ratio chi-squared values for these two models (this appears as LR chi2(1) near the upper right corner of the output). The difference between the two likelihood-ratio chi-squared values is 1 degree of freedom.

```
use nlsy97_chapter11, clear
logistic drank30 male dinner97 pdrink97
estimates store a
logistic drank30 age97 male dinner97 pdrink97
#subtracts the chi-squared values and estimates the probability of the chi-squared difference;
lrtest a
```
or just use `lrdrop1`

### Testing sets of coefficients
```
test pdrink97 dinner97
#it is the same as:
logistic drank30 age97 male if !mi(dinner97) &!mi(pdrink97)
estimates store a
logistic drank30 age97 male pdrink97 dinner97 
lrtest a
lrdrop1
```

this overall test only tells us that at least one of them is significant.

## Margins
```
logit drank30 age97 i.black pdrink97 dinner97
margins, dydx(black) atmeans
margins black, atmeans
margins, at(pdrink97=(1 2 3 4 5)) atmeans
marginsplot
```
We can run the logistic regression using the i. label for this categorical variable, i.black. This produces the same results for the logistic regression as if we had simply used black, but the results will work properly if we follow this command with other postestimation commands.

## Nested logistic regressions

The `nestreg` command is extremely general, applicable across a variety of regression models, including logistic, negative binomial, Poisson, probit, ordered logistic, tobit, and others. It also works with the complex sample designs for many regression models.

## Power analysis
```
powerlog, p1(.70) p2(.75) alpha(.05)
powerlog, p1(.70) p2(.75) alpha(.05) rsq(.30) help
```