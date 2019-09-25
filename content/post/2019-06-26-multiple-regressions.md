---
title: Multiple Regressions
author: wyih
date: '2019-06-26'
slug: multiple-regressions
categories:
  - Stata
tags:
  - Stata
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T16:54:10+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
{{% toc %}}

Note: toc is not compatible with `markup: mmark`
## Basic
* F: There is a highly significant relationship between outcomes and the set of predictors.
* R2: How much of the outcome variance is explained by the regression model
* Adj-R2: remove the chance effects
* Coef.: *unstandardized regression coefficients*
* t: coef/standard error
* Std. Err.: represents the average distance that the observed values fall from the regression line. Conveniently, it tells you how wrong the regression model is on average using the units of the response variable.
* ,beta gives **beta weights**: based on standardizing all variables to have a mean of 0 and a standard deviation of 1. These beta weights are interpreted similarly to how you interpret correlations in that beta<0.2 is considered a weak effect,  between 0.2 and 0.5 is considered a moderate effect, and  is considered a strong effect.(range of -1 to +1, if out of range, ->multicollinearity problem):a 1-standard-deviation change in the independent variable produces a - beta standard-deviation change in the dependent variable.
* increment in R2:*part-correlation square* because it measures the part that is uniquely explained by the variable. or *semipartial R2* (Semipartial Corr.^2 in `pcorr` )estimates only the **unique** effect of each predictor. Another way to compare is partial correlation; 
* distribution of the dependent variable: `histogram env_con, frequency normal kdensity` (for [kernel density estimation](https://lotabout.me/2018/kernel-density-estimation/))**Skewness**(0:Normal; <0: negative or left skew, >0: positive or skew to the right)**kurtosis**(3: normal; <3: tails are too thick, flat or negative kurtosis; >3: tails are too thin, peaky or positive kurtosis)`sktest`
* distribution of the residuals: for large sample, normality is not a critical issue. `rvfplot, yline(0)`residual-versus-fitted plot:
To solve the non-normal distribution of residual, we can use `reg y xs, vce(robust)` or use bootstrap`reg y xs, vce(bootstrap, rep(1000))` , it will change std err and hence t-value.  However, 
Andrew J. Leone, Miguel Minutti-Meza, and Charles E. Wasley (2019) Influential Observations and Inference in Accounting Research. The Accounting Review In-Press.
they talk about robust regression using **robreg, what's the difference?**
ALso, check [Correcting for Cross-Sectional and Time-Series Dependence in Accounting Research](https://www.kellogg.northwestern.edu/faculty/petersen/htm/papers/se/se_programming.htm)

 
```
regress env_con educat inc com3 hlthprob epht3, beta
predict envhat
preserve
set seed 515
sample 100, count
twoway (scatter env_con envhat) (lfit env_con envhat)
restore
```




## Diagnostic statistics

* [Rstandard:](http://www.r-tutor.com/elementary-statistics/simple-linear-regression/standardized-residual)

The standardized residual is the residual divided by its standard deviation.

```
regress env_con educat inc com3 hlthprob epht3, beta
predict yhat
predict residual, residual
predict rstandard, rstandard
list respnum env_con yhat residual rstandard if abs(rstandard) > 2.58 & rstandard < .
dfbeta
list respnum rstandard _dfbeta_1 if abs(_dfbeta_1) > 2/sqrt(3769) & _dfbeta_1 < .
estat vif

```

* Influential observations: DFbeta: You could think of this as redoing the regression model, omitting just one observation at a time and seeing how much difference omitting each observation makes. ****A value of **DFbeta  >2/sqrt(N) ** indicates that an observation has a large influence**** More specific than rstandard



```
. dfbeta
(739 missing values generated)
                       _dfbeta_1: dfbeta(educat)
(739 missing values generated)
                       _dfbeta_2: dfbeta(inc)
(739 missing values generated)
                       _dfbeta_3: dfbeta(com3)
(739 missing values generated)
                       _dfbeta_4: dfbeta(hlthprob)
(739 missing values generated)
                       _dfbeta_5: dfbeta(epht3)
```

* multicollinearity: The more correlated the predictors, the more they overlap and, hence, the more difficult it is to identify their independent effects. In such situations, you can have multicollinearity in which one or more of the predictors are virtually redundant.
variance inflation factor `estat vif` after regression, if >10, for any variable, a multicollinearity problem may exist. If the average VIF is substantially greater than 1.00, there still could be a problem.(Dropping a variable, create a scale that combines them into one variable.)
1/VIF = 1-R2(of regress X1 on other Xs) It tells how much of the variance in the independent variable is available to predict the outcome variable independently.
## Weighted data
```
regress env_con educat inc com3 hlthprob epht3 [pweight=finalwt], beta
```
When you do a weighted regression this way, Stata automatically uses the robust regression—whether you ask for it or not—because weighted data require robust standard errors.
## Categorical predictors and hierarchical regression
```
regress smday97 age97 male psmoke97 aa hispanic other if !missing(smday97, ///
	age97, male, psmoke97, aa, hispanic, other), beta
test aa hispanic other
```
nested regressions
```
nestreg: regress smday97 (age97 male) (psmoke97) (aa hispanic other), beta
```
If you put i. as a stub in front of a categorical variable, Stata will make the first category the reference category and then generate a dummy variable for each of the remaining categories.
```
regress smday97 age97 male psmoke97 i.race
#change reference category or what Stata refers to as the baselevel
regress smday97 age97 male psmoke97 ib3.race
regress smday97 age97 male psmoke97 ib(last).race
```
## interaction
```
g ed_male = educ*male
reg inc educ male ed_male,beta
nestreg: regress inc (educ male) (ed_male), beta
regress inc i.male##c.educ, beta
```
some researchers choose to center quantitative independent variables, such as education, before computing the interaction terms.
Centering is important for independent variables where a value of zero may not be meaningful.
```
summarize educ
generate educ_c = educ - r(mean)
```
margins help us to interpret the interaction term
```
margins male, at(educ=(8 10 12 14 16 18))
marginsplot
```

## nonlinear
```
regress ln_wage c.ttl_exp##c.ttl_exp, beta
margins, at(ttl_exp = (0(2)28))
marginsplot
```

[Power analysis](https://stats.idre.ucla.edu/stata/dae/multiple-regression-power-analysis/)
