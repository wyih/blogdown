---
title: Missing values
author: wyih
date: '2019-06-26'
slug: missing-values
categories:
  - Stata
tags:
  - Stata
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T16:48:42+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
Many advanced Stata estimation models can use multiple imputation for handling missing values.

[Auxiliary variables](https://www.iriseekhout.com/missing-data/auxiliary-variables/) are variables that can help to make estimates on incomplete data, while they are not part of the main analysis (Collins et al., 2001). 

1. Include all variables in the analysis model, including the dependent variable,
2. Include auxiliary variables that predict patterns of missingness, 
3. and Include additional variables that predict a person’s score on a variable that has missing values.

The imputation model is then used to generate a complete dataset.

Once you have included a reasonably large number of variables, adding additional variables may not be helpful because of multicollinearity.

Drop any participant who does not have complete information on every item used in the analysis. This approach goes by several names, including **full case analysis**, **casewise deletion**, or ***listwise deletion.***

* There will be a substantial loss of power because of the reduced sample size. 
* Listwise deletion can introduce substantial bias. (survival bias)

One alternative to listwise deletion involves substituting the mean on a variable for anybody who does not have a response. This has two serious limitations. People who are average on a variable are often more likely to give an answer than are people who have an extreme value.The second problem with mean substitution is that when you give several people the same score on a variable, these people have zero variance on the variable. This artificially reduced variance will seriously bias our parameter estimates. 

The key to understanding multiple imputation is that the imputed missing values will not contain any unique information once the variables in the model and the auxiliary variables are allowed to explain the patterns of missing values and predict the score of the missing values. The imputed values for variables with missing values are simply consistent with the observed data. This allows us to use all available information in our analysis.

## Multiple imputation

A powerful way of working with missing values involves multiple imputation. The command *mi* involves three straightforward steps:

* Create *m* complete datasets by imputing the missing values. Each dataset will have no missing values, but the values imputed for missing values will vary across the  datasets.
* Do your analysis in each of the *m*  complete datasets.
* Pool your *m*  solutions to get one solution.
    * The parameter estimates—for example, regression coefficients—will be the mean of their corresponding values in the  datasets.
    * The standard errors used for testing significance will combine the standard errors from the solutions plus the variance of the parameter estimates across the  solutions. If each solution is yielding a very different estimate, this uncertainty is added to the standard errors. Also the degrees of freedom is adjusted based on the number of imputations and proportion of data that have missing values.

The most widely used approach is using multivariate normal regression (MVN). `mi impute mvn` is designed for continuous variables. `mi impute chained` is another useful alternative.

A missing value will have a code of ., .a, .b, etc. Remember that a missing value is recorded in a Stata dataset as an extremely high value. Within mi, a missing-value code, . (dot), has a special meaning. It denotes the missing values eligible for imputation. If you have a set of missing values that should not be imputed, you should record them as extended missing values, that is, as .a, .b, etc.`recode agem (.a = .)`

```
misstable summarize ln_wagem gradem agem ttl_expm tenurem not_smsa south blackm
misstable patterns ln_wagem gradem agem ttl_expm tenurem not_smsa south blackm
quietly misstable summarize ln_wagem gradem agem ttl_expm tenurem not_smsa south blackm, gen(miss_)
```
then
```
logit miss_ln_wagem gradem agem ttl_expm tenurem not_smsa south blackm if ln_wagem <= .
logit miss_gradem ln_wagem agem ttl_expm tenurem not_smsa south blackm if gradem <= .
logit miss_agem ln_wagem gradem ttl_expm tenurem not_smsa south blackm if agem <= .
logit miss_ttl_expm ln_wagem gradem agem tenurem not_smsa south blackm if ttl_expm <= .
logit miss_tenurem ln_wagem gradem agem ttl_expm not_smsa south blackm if tenurem <= .
logit miss_blackm ln_wagem gradem agem ttl_expm tenurem not_smsa south if blackm <= .
```
Or use `pwcorr , obs sig` to find potential auxiliary variables. 

Any variable that is statistically significant in these logistic regressions should be included in the imputation step.

```
mi set flong
mi register imputed ln_wagem gradem agem ttl_expm tenurem blackm
mi register regular not_smsa south 
```
The `mi set flong` command tells Stata how to arrange our multiple datasets(flong (full and long), or mlong (marginal and long)). The `mi register imputed` command registers all the variables that have missing values and need to be imputed. The `mi register regular` command registers all the variables that have no missing values or for which we do not want to impute values.


```
mi impute mvn ln_wagem gradem agem ttl_expm tenurem blackm, add(20) rseed(2121)
```
生成m=20个数据集，`_mi_m` variable identifies datasets and ranges from 0 to 20.
```
mi impute mvn ln_wagem gradem agem ttl_expm tenurem blackm, add(20) rseed(2121)
```
To get pooled $R^2$ and standardized $\beta$s use `mibeta`
```
mibeta ln_wagem gradem agem ttl_expm tenurem not_smsa south blackm, fisherz miopts(vartable)
```
When **impossible** values are imputed(建议不调整): Binary variables, squares, and interactions（在原数据集先相乘，再impute）

