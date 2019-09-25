---
title: Linear Regression
author: wyih
date: '2019-07-04'
slug: linear-regression
markup: mmark
categories:
  - R
tags:
  - econometrics-with-r
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T16:25:23+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
## OLS estimator
The method to compute (or *estimate*) $b_0$ and $b_1$ we illustrated above is called *Ordinary Least Squares*, or OLS. $b_0$ and $b_1$ are therefore also often called the *OLS coefficients*. By solving problem 

$$
\begin{align}
e_i & = y_i - \hat{y}_i = y_i - \underbrace{\left(b_0 + b_1 x_i\right)}_\text{prediction}\\
e_1^2 + \dots + e_N^2 &= \sum_{i=1}^N e_i^2 \equiv \text{SSR}(b_0,b_1) \\
(b_0,b_1) &= \arg \min_{\text{int},\text{slope}} \sum_{i=1}^N \left[y_i - \left(\text{int} + \text{slope } x_i\right)\right]^2 
\end{align}
$$

one can derive an explicit formula for them:

$$
\begin{equation}
b_1 = \frac{cov(x,y)}{var(x)}
\end{equation}
$$
i.e. the estimate of the slope coefficient is the covariance between $x$ and $y$ divided by the variance of $x$, both computed from our sample of data. With $b_1$ in hand, we can get the estimate for the intercept as

$$\begin{equation}
b_0 = \bar{y} - b_1 \bar{x}
\end{equation}$$


where $\bar{z}$ denotes the sample mean of variable $z$. The interpretation of the OLS slope coefficient $b_1$ is as follows. Given a line as in $y = b_0 + b_1 x$,

* $b_1 = \frac{d y}{d x}$ measures the change in $y$ resulting from a one unit change in $x$
* For example, if $y$ is wage and $x$ is years of education, $b_1$ would measure the effect of an additional year of education on wages.

There is an alternative representation for the OLS slope coefficient which relates to the *correlation coefficient* $r$. Remember that $r = \frac{cov(x,y)}{s_x s_y}$, where $s_z$ is the standard deviation of variable $z$. With this in hand, we can derive the OLS slope coefficient as

$$
\begin{align}
b_1 &= \frac{cov(x,y)}{var(x)}\\
    &= \frac{cov(x,y)}{s_x s_x} \\
    &= r\frac{s_y}{s_x} \end{align}
$$


In other words, the slope coefficient is equal to the correlation coefficient $r$ times the ratio of standard deviations of $y$ and $x$.

### Linear Regression without Regressor


$$
\begin{equation}
y = b_0
\end{equation}
$$


This means that our minimization problem becomes very simple: We only have to choose $b_0$! We have 

$$
b_0 = \arg\min_{\text{int}} \sum_{i=1}^N \left[y_i - \text{int}\right]^2,
$$
which is a quadratic equation with a unique optimum such that 
$$
b_0 = \frac{1}{N} \sum_{i=1}^N y_i = \overline{y}.
$$

> Least Squares **without regressor** $x$ estimates the sample mean of the outcome variable $y$, i.e. it produces $\overline{y}$.

### Regression without an Intercept

We follow the same logic here, just that we miss another bit from our initial equation and the minimisation problem now becomes:
$$
\begin{align}
b_1 &= \arg\min_{\text{slope}} \sum_{i=1}^N \left[y_i - \text{slope } x_i \right]^2\\
\mapsto b_1 &= \frac{\frac{1}{N}\sum_{i=1}^N x_i y_i}{\frac{1}{N}\sum_{i=1}^N x_i^2} = \frac{\bar{x} \bar{y}}{\overline{x^2}} 
\end{align}
$$

> Least Squares **without intercept** (i.e. with $b_0=0$) is a line that passes through the origin. 

In this case we only get to choose the slope $b_1$ of this anchored line.[^fn1]

[^fn1]: This slope is related to the angle between vectors $\mathbf{a} =(\overline{x},\overline{y})$, and $\mathbf{b} = (\overline{x},0)$. Hence, it's related to the [scalar projection](https://en.wikipedia.org/wiki/Scalar_projection) of $\mathbf{a}$ on $\mathbf{b}$]

### Centering A Regression

By *centering* or *demeaning* a regression, we mean to substract from both $y$ and $x$ their respective averages to obtain $\tilde{y}_i = y_i - \bar{y}$ and $\tilde{x}_i = x_i - \bar{x}$. We then run a regression *without intercept* as above. That is, we use $\tilde{x}_i,\tilde{y}_i$ instead of $x_i,y_i$ in 

$$
\begin{align}
b_1 &= \arg\min_{\text{slope}} \sum_{i=1}^N \left[y_i - \text{slope } x_i \right]^2\\
\mapsto b_1 &= \frac{\frac{1}{N}\sum_{i=1}^N x_i y_i}{\frac{1}{N}\sum_{i=1}^N x_i^2} = \frac{\bar{x} \bar{y}}{\overline{x^2}} 
\end{align}
$$

 to obtain our slope estimate $$b_1$$:

$$
\begin{align}
b_1 &= \frac{\frac{1}{N}\sum_{i=1}^N \tilde{x}_i \tilde{y}_i}{\frac{1}{N}\sum_{i=1}^N \tilde{x}_i^2}\\
    &= \frac{\frac{1}{N}\sum_{i=1}^N (x_i - \bar{x}) (y_i - \bar{y})}{\frac{1}{N}\sum_{i=1}^N (x_i - \bar{x})^2} \\
    &= \frac{cov(x,y)}{var(x)}
\end{align}
$$


This last expression is *identical* to the one in OLS estimate! It's the standard OLS estimate for the slope coefficient. We note the following: 

>Adding a constant to a regression produces the same result as centering all variables and estimating without intercept. So, unless all variables are centered, **always** include an intercept in the regression.

### Standardizing A Regression {#reg-standard}

*Standardizing* a variable $z$ means to demean as above, but in addition to divide the demeaned value by its own standard deviation. Similarly to what we did above for *centering*, we define transformed variables $\breve{y}_i = \frac{y_i-\bar{y}}{\sigma_y}$ and $\breve{x}_i = \frac{x_i-\bar{x}}{\sigma_x}$ where $\sigma_z$ is the standard deviation of variable $z$. From here on, you should by now be used to what comes next! As above, we use $\breve{x}_i,\breve{y}_i$ instead of $x_i,y_i$:

$$
\begin{align}
b_1 &= \frac{\frac{1}{N}\sum_{i=1}^N \breve{x}_i \breve{y}_i}{\frac{1}{N}\sum_{i=1}^N \breve{x}_i^2}\\
    &= \frac{\frac{1}{N}\sum_{i=1}^N \frac{x_i - \bar{x}}{\sigma_x} \frac{y_i - \bar{y}}{\sigma_y}}{\frac{1}{N}\sum_{i=1}^N \left(\frac{x_i - \bar{x}}{\sigma_x}\right)^2} \\
    &= \frac{Cov(x,y)}{\sigma_x \sigma_y} \\
    &= Corr(x,y)  
\end{align}
$$

> After we standardize both $y$ and $x$, the slope coefficient $b_1$ in the regression without intercept is equal to the **correlation coefficient**.

## Predictions and Residuals {#pred-resids}

Now we want to ask how our residuals $e_i$ relate to the prediction $\hat{y_i}$. Let us first think about the average of all predictions $$\hat{y_i}$$, i.e. the number $$\frac{1}{N} \sum_{i=1}^N \hat{y_i}$$. Let's just take 

$$
\begin{equation}
\hat{y}_i = b_0 + b_1 x_i 
\end{equation}
$$

and plug this into this average, so that we get

$$
\begin{align}
\frac{1}{N} \sum_{i=1}^N \hat{y_i} &= \frac{1}{N} \sum_{i=1}^N b_0 + b_1 x_i \\
&= b_0 + b_1  \frac{1}{N} \sum_{i=1}^N x_i \\
&= b_0 + b_1  \bar{x} \\
\end{align}
$$


But that last line is just equal to the formula for the OLS intercept  $b_0 = \bar{y} - b_1 \bar{x}$! That means of course that

$$
\frac{1}{N} \sum_{i=1}^N \hat{y_i}  = b_0 + b_1  \bar{x} = \bar{y}
$$
in other words:

>The average of our predictions $\hat{y_i}$ is identically equal to the mean of the outcome $y$. This implies that the average of the residuals is equal to zero.

Related to this result, we can show that the prediction $\hat{y}$ and the residuals are *uncorrelated*, something that is often called **orthogonality** between $\hat{y}_i$ and $e_i$. We would write this as

$$
\begin{align}
Cov(\hat{y},e) &=\frac{1}{N} \sum_{i=1}^N (\hat{y}_i-\bar{y})(e_i-\bar{e}) =   \frac{1}{N} \sum_{i=1}^N (\hat{y}_i-\bar{y})e_i \\
&=  \frac{1}{N} \sum_{i=1}^N \hat{y}_i e_i-\bar{y} \frac{1}{N} \sum_{i=1}^N e_i = 0
\end{align}
$$

## Correlation, Covariance and Linearity
It is important to keep in mind that Correlation and Covariance relate to a *linear* relationship between `x` and `y`. Given how the regression line is estimated by OLS (see just above), you can see that the regression line inherits this property from the Covariance.

> Always **visually inspect** your data, and don't rely exclusively on summary statistics like *mean, variance, correlation and regression line*. All of those assume a **linear** relationship between the variables in your data.

## Analysing $Var(y)$
Analysis of Variance (ANOVA) refers to a method to decompose variation in one variable as a function of several others. We can use this idea on our outcome $y$. Suppose we wanted to know the variance of $y$, keeping in mind that, by definition, $y_i = \hat{y}_i + e_i$. We would write

$$
\begin{align}Var(y) &= Var(\hat{y} + e)\\ &= Var(\hat{y}) + Var(e) + 2 Cov(\hat{y},e)\\ &= Var(\hat{y}) + Var(e) \end{align}
$$

We have seen that the covariance between prediction $\hat{y}$ and error $e$ is zero, that's why we have $Cov(\hat{y},e)=0$. What this tells us in words is that we can decompose the variance in the observed outcome $y$ into a part that relates to variance as *explained by the model* and a part that comes from unexplained variation. Finally, we know the definition of *variance*, and can thus write down the respective formulae for each part:

* $$Var(y) = \frac{1}{N}\sum_{i=1}^N (y_i - \bar{y})^2$$

* $$Var(\hat{y}) = \frac{1}{N}\sum_{i=1}^N (\hat{y_i} - \bar{y})^2$$, because the mean of $\hat{y}$ is $\bar{y}$ as we know. 

* Finally, $$Var(e) = \frac{1}{N}\sum_{i=1}^N e_i^2$$, because the mean of $e$ is zero.
  We can thus formulate how the total variation in outcome $y$ is apportioned between model and unexplained variation:

  > The total variation in outcome $y$ (often called SST, or *total sum of squares*) is equal to the sum of explained squares (SSE) plus the sum of residuals (SSR). We have thus **SST = SSE + SSR**.


## Assessing the *Goodness of Fit*
In our setup, there exists a convenient measure for how good a particular statistical model fits the data. It is called $R^2$ (*R squared*), also called the *coefficient of determination*. We make use of the just introduced decomposition of variance, and write the formula as

$$
\begin{equation}R^2 = \frac{\text{variance explained}}{\text{total variance}} = \frac{SSE}{SST} = 1 - \frac{SSR}{SST}\in[0,1]  \end{equation}
$$

It is easy to see that a *good fit* is one where the sum of *explained* squares (SSE) is large relative to the total variation (SST). In such a case, we observe an $R^2$ close to one. In the opposite case, we will see an $R^2$ close to zero. Notice that a small $R^2$ does not imply that the model is useless, just that it explains a small fraction of the observed variation.

