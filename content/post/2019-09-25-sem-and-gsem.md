---
title: SEM and GSEM
author: wyih
date: '2019-09-25'
slug: sem-and-gsem
categories:
  - Stata
tags:
  - Stata
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T17:02:04+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
## SEM
```
sem bmi <- age children incomeln educ quickfood
```
This would give us the unstandardized solution. This command uses **maximum likelihood estimation** ather than the ordinary least-squares (OLS) estimation used by the `regress` command. Add `,standardized` just like add `,beta` to `regress`

option `method(mlmv)` (maximum likelihood with missing values):
Estimation is less robust to the assumption of multivariate normality when using the method(mlmv) option than when using maximum likelihood estimation with listwise deletion of observations with missing values. Because some of the five variables in our model are not normally distributed, the method(mlmv) option needs to be used with caution. The estimation performed when we use the method(mlmv) option also assumes that the missing values are MAR[^f1] . By contrast, when listwise deletion is used we are assuming that missing values are MCAR[^f1], and this is a much more restrictive assumption.
[^f1]: Missing at Random (MAR)This is where the unfortunate names come in.Missing at Random means  the propensity for a data point to be missing is not related to the missing data, but it is related to some of the observed data.

[^f1]:Missing Completely at Random is pretty straightforward.  What it means is what is says:  the propensity for a data point to be missing is completely random.


```
sem bmi <- age children incomeln educ quickfood, method(mlmv) standardized

estat eqgof
```

The OLS regression solution and the SEM solution without MLMV, which uses listwise deletion, are producing the same standardized parameter estimates and $R^2$s. As noted, the z values are slightly larger than the t-values, and the p-values are slightly smaller. The z tests for the SEM solution are directly testing the standardized solution. The regress solution’s  t tests are testing the significance of the unstandardized B coefficients and do not directly test the significance of the Betas. The regress command does not provide such a direct test for the significance of Betas.

Notice that the $R^2$ using sem with method(mlmv) is actually slightly smaller. Using all the available information in the SEM solution with MLMV is not cheating if the assumptions are met. The **MAR** assumption for the SEM solution is more realistic than the **MCAR** assumption required for listwise deletion to be unbiased.

There are three rules to follow when using the maximum likelihood with missing values estimation.

* Generate an indicator variable for each variable in your model to reflect whether an observation has a missing value.
* Correlate potential auxiliary variables to see whether they predict missing value indicator variables.
* Include additional auxiliary variables that are substantially correlated with a person’s score on a variable that has missing values.

Getting auxiliary variables into your SEM command？？？没懂

## GSEM

```
logit obese age children incomeln educ quickfood
listcoef
glm obese age children incomeln educ quickfood, family(binomial) link(logit)
glm, eform
```
The logit command is a special application of the generalized linear model. We can obtain the same results by using the glm command. The glm command requires us to specify the family of our model, family(binomial), and the link function, link(logit). To obtain the odds ratio, we can replay these results by using glm, eform.

后面没看懂，以后再说吧。