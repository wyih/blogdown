---
title: Measurement, reliability, and validity
author: wyih
date: '2019-06-26'
slug: measurement-reliability-and-validity
markup: mmark
categories:
  - Stata
tags:
  - Stata
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T16:51:24+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
## Constructing a Scale
```
recode empathy2 empathy4 empathy5 (1=5 "Does not describe very well") ///
  (2=4) (3=3) (4=2) (5=1 "Describes very well"), pre(rev) label(empathy)
egen empathy = rowmean(empathy1 revempathy2 empathy3 revempathy4 ///
  revempathy5 empathy6 empathy7)
egen miss = rowmiss(empathy1 revempathy2 empathy3 revempathy4 ///
   revempathy5 empathy6 empathy7) 
egen empathya = rowmean(empathy1 revempathy2 empathy3 revempathy4 ///
   revempathy5 empathy6 empathy7) if miss < 3
```
One drawback to using the rowmean() function is that it simply adds the score on the items a person answers and divides by the number of items answered.
## Reliability

* **Stability** means that if you measure a variable today using a particular scale and then measure it again tomorrow using the same scale, your results will be consistent.(correlation r,`pwcorr`, intraclass correlation $\rho_I$) 
* **Equivalence** means that you have two measures of the same variable and they produce consistent results. (correlation $r_{xx}$)* (A low correlation means either that the measure is not reliable or that the measures are not truly equivalent.)
* A reliable test would be **internally consistent** if the score for the first half of the items was highly correlated with the score for the second half of the items.(correlation $$r_{x_Ax_B}$$), alpha,$$\alpha$$) In general, an $\alpha>0.8$ is considered good reliability, and many researchers feel an $\alpha>0.7$ is adequate reliability. ($$\alpha=\sigma^2_{True}/(\sigma^2_{True}+\sigma^2_{error})$$)However, for this interpretation to be used, we need to assume that the scale is valid.
* 
```
alpha empathy1 revempathy2 empathy3 revempathy4 revempathy5 /// 
  empathy6 empathy7, asis item min(5)
```
The asis (as is) option means that we do not want Stata to change the signs of any of our variables.
The bottom row of the output table, *Test scale*, reports the $\alpha$ for the scale (0.7462). Above this value is the $\alpha$ we would obtain if we dropped each item, one at a time. The *item-test correlation* column reports the correlation of each item with the total score of the seven items. *item-rest correlation*. This is the correlation of each item with the total of the other items.
The equivalent of alpha for items that are dichotomous is the Kuder–Richardson measure of reliability.`alpha` 
* **Rater consistency** is important when you have observers rating a video, observed behavior, essay, or something else where two or more people are rating the same information. Here reliability means that a pair of raters gives consistent results.(kappa,$\kappa$ `kap coder1 coder2`)$\kappa$ only gives us credit for the extent the agreement exceeds what we would have expected to get by chance alone. kappa tends to be lower than alpha.
## Validity
A valid measure is one that measures what it is supposed to be measuring. 


* **表面效度(face validity)**：把設計的問卷，拿給親朋好友填，並問他們問卷好不好。指測量工具在外顯形式上的有效程度 
* **內容效度(content validity)**：找一群有相關經驗的人來看題目，問他們設計的好不好，有沒有哪裡要修改。Content validity ratio (CVR): Judges rate each item as *essential, useful, or not necessary.*  $CVR=(Ne - N/2)/(N/2)$ , in which the $Ne$ is the number of panelists indicating "essential" and $N$ is the total number of panelists. You can keep the items that have a relatively high CVR and drop those that do not. 
* **效標效度(criterion validity)**：把測量工具和其他可測量的工具，算他們之間的相關n以測驗分數和特定效標（criterion）之間的相關係數，表示測量工具有效性之高低。 
    * （1）同時效度(current validity)：把設計好的題目，和標準工具（同樣的觀念，相同的變項），去算之間的相關。如：測疼痛忍受度，有四題一分鐘可測完的題目，和另一份標準工具的題目，45題1小時可做完的題目去測，如果R＝0.92（高相關），表示原題目有同時效度。
    * （2）預測效度(predictive validity)：一個調查，可以預測未來的事件、行為、態度、結果。如：手術後，病人對止痛藥的需求，看24個病人的分數，分數越高，手術忍受度越高。把24的分數算出，和拿止痛藥量求相關，R＝－0.82，表示高忍痛程度，低止痛藥量。SAT（可以預測大學第一學期的平均成績）成績，和大學第一學期的平均成績求相關，R＝0.42，表示沒有預測效度。但是R如果逐年增加，則表示有預測效度。
* 構念（建構）效度(construct validity)：
    * We can assess the **convergent** and **divergent** validity of our measure, hope, by seeing whether it is positively correlated with variables with which we believe it converges and negatively correlated with variables with which we believe it diverges.`ttest, esize, pwcorr`
## Factor analysis

* exploratory factor analysis, which Stata calls **principal factor analysis**: the variance is partitioned into the shared variance and unique or error variance. The shared variance is how much of the variance in any one item can be explained by the rest of the items. PF
* **principal-component factor analysis** PCF

`putdocx` stata 15可以create word documents!

### Terminology

* Extraction(萃取)
* Eigenvalues: In the case of PCF analysis, If there are 10 items, the sum of the eigenvalues will be 10.The factors will be ordered from the most important, which has the largest eigenvalue, to the least important, which has the smallest eigenvalue.In PF analysis, the sum of the eigenvalues will be less than the number of items, and the eigenvalues’ interpretation is complex.
* Communality and uniqueness: PF analysis tries to explain the shared variance. PCF analysis tries to explain all the variance, which is why it is ideal for the uniqueness to approach zero.
* Loadings: how clusters of items are most related to one or another of the factors. If an item has a loading over 0.4 on a factor, it is considered a good indicator of that factor.
* Simple structure: This is a pattern of loadings where each item loads strongly on just one factor and a subset of items load strongly on each factor. When an item loads strongly on more than one factor, it is factorially confounded.
* Scree plot: This is a graph showing the eigenvalue for each factor. When doing a PCF analysis, we usually drop factors that have eigenvalues in the neighborhood of 1.0 or smaller.
* Rotation: 轉軸的方式有很多種，但基本就是兩大類：正交 (orthogonal) 與斜交 (oblique rotation)。轉軸的目的是讓因素更有意義，並同時看看因素之間的關係。更詳細一點來說，如果是正交轉軸的話，那就是假設因素之間沒有關連；相對地，斜交假設因素之間有一定的關連。
* Factor score: weights each item based on how related it is to the factor. Also the factor score is scaled to have a mean of 0.0 and a variance of 1.0.

Use PCF when you have a set of items that you believe all measure one concept. In this situation, you would be interested in the first principal factor. You would want to see if it explained a substantial part of the total variance for the entire set of items, and you would want most of the items to have a **loading of 0.4 or above** on this factor. Because PCF analysis is trying to explain all the variance in the items, the **uniqueness** for each item should approach zero. Generally, we should consider any factor that has an eigenvalue of more than 1.A visual way to examine the eigenvalues is with a scree plot.
```
factor rnatspac rnatenvir rnatheal rnatcity rnatcrime rnatdrug ///
	rnateduc rnatrace rnatarms rnatfare rnatroad rnatsoc rnatchld rnatsci, pcf
screeplot
```
If, on the other hand, you want to identify two or more latent variables that represent interpretable dimensions of some concept, then PF analysis is probably best.

### Rotation

* Orthogonal:`rotate`With a varimax rotation, we can think of the loadings as being the estimated correlation between each item and each factor.
* oblique:`rotate, promax`

`estat common` to get correlation matrix of promax rotated common factors

## Get one factor score

However, this distinction rarely makes a lot of practical difference. The factor score may make a difference if there are some items with very large loadings, say, 0.9, and others with very small loadings, say, 0.2. But we would probably drop the weakest items. When the loadings do not vary a great deal, computing a factor score or a mean/total score will produce comparable results.
```
factor rnatenvir rnatheal rnatcity rnatcrime rnatdrug rnateduc rnatrace ///
	rnatfare rnatsoc rnatchld, pcf
predict libfscore, norotate
egen libmean = rowmean(rnatenvir rnatheal rnatcity rnatcrime rnatdrug ///
	rnateduc rnatrace rnatfare rnatsoc rnatchld)
```
correlation higher than 0.9...