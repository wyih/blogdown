---
title: Multilevel analysis
author: wyih
date: '2019-06-26'
slug: multilevel-analysis
markup: mmark
categories:
  - Stata
tags:
  - Stata
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T16:44:54+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
Multilevel analysis can address the lack of independence of the observations when you are analyzing grouped data. See *Stata Multilevel Mixed-Effects Reference Manual*.

* groups of individuals
* panel data

## Fixed-effects regression models

$$y_it = \beta_0 +\beta x_{it}+\mu_i+\eta_{it}$$

if $$\mu_i$$ correlates with $$x_{it}$$ -> Fixed-effects
if $$\mu_i$$ independent of $$x_{it}$$ -> Random-effects models give consistent estimates 

`xtreg`  see *Stata Longitudinal-Data/Panel-Data Reference Manual.*

## Random-effects regression models

$$y_it = \beta_0 +\beta x_{it}+\gamma z_i +\mu_i+\eta_{it}$$

assume $$\mu_i$$ is independent of $$x_{it}$$

fixed component, $$ \beta_0 +\beta x_{it}+\gamma z_i$$ , describes the overall relationship between our dependent variable and our independent variable. The random component, $$\mu_i$$ i represents the effects of the unobserved time-invariant variables.

score = fixed part + random effects + error

**Going back and forth between wide and long formats** : `reshape wide` and `reshape long`

```
reshape long drink, i(id) j(wave)
```
## Random-intercept model
### linear model

```
mixed drink c.wave || id:
estimates store linear
margins, at(wave=(0(2)10))
marginsplot
```
### quadratic term
```
mixed drink c.wave##c.wave || id:
estimates store quadratic
margins, at(wave=(0(2)10))
marginsplot
lrtest linear quadratic
```

A proportional reduction in error (PRE) measuring how much the residual (error) variance is reduced by adding the quadratic term may be useful. We will call the random-intercept linear model “Model 1” and the random-intercept quadratic model “Model 2”.

PRE = (var(Residual)Model1-var(Residual)Model2)/var(Residual)Model1

### Treating time as a categorical variable
```
mixed drink i.wave || id:
estimates store means
margins, at(wave=(0(2)10))
marginsplot
lrtest linear means
lrtest quadratic means
```
## Random-coefficients model
```
mixed drink c.wave || id: wave, cov(unstructured)
predict yhat_drink, fitted
```

## Including a time-invariant covariate
```
* Random coefficients model with time invariant covariate
* gender coded as male = 1, female = 0
mixed drink c.wave i.male || id: wave
margins male, at(wave=(0(2)8))
marginsplot

* Random coefficients, with wave interacting with the
* time invariant covariate--gender coded
mixed drink c.wave##i.male || id: wave
margins male, at(wave=(0(2)8))
marginsplot

mixed drink c.wave##c.wave##i.male || id: wave
margins male, at(wave=(0(2)8))
marginsplot

```