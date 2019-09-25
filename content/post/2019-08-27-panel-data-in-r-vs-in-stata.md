---
title: Panel data in R vs in Stata
author: wyih
date: '2019-08-27'
slug: panel-data-in-r-vs-in-stata
categories:
  - R
  - Stata
tags:
  - R
  - Stata
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T15:20:28+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

## Panel data with one way fixed effect

```R
mm1 <- invforward ~ TOBINQ + inv + top3 + size + lev + cash + loss + lnage + cfo + sd + ic + factor(year)
zzz <- plm(mm1,data=sample,model="within",index=c("stkcd"))
```
same as xtreg i.year fe , without robust vcetype
用这种方法算出来$R^2$和Stata报告$R^2$ within的一致 
```R
m1 <- invforward ~ TOBINQ + inv + top3 + size + lev + cash + loss + lnage + cfo + sd + ic
zz <- plm(m1,data=sample,model="within",index=c("stkcd", "year"),effect = "twoways")
summary(zz)
```
same sa xtreg i.year, fe , without robust vcetype，但$R^2$较Stata报告$R^2$ within小

##  vcetype robust
```R
zz_r <- coeftest(zz, vcov.=function(x) vcovHC(x, type="sss")) # same as stata xtreg i.year, fe r
# OR
zzz_r <- coeftest(zzz, vcov.=function(x) vcovHC(x, type="sss"))
```

## 组间系数比较
OLS可用
```R
sur_diff <-  MVBV ~ (Dm + Dh + EBV + DmEBV +DhEBV)*g_layer
h2t <- h2 %>%
  filter(g_layer != 2)%>%
  mutate(g_layer = ifelse(g_layer == 1, 0, 1))
mm <- lm(sur_diff,data=h2t)
ttt <-  coeftest(mm, vcov.=function(x) vcovHC(x, cluster="group", type="HC1"))

stargazer(fpm,models_growth_layer,type = "text", column.labels = table4_label)
stargazer(fpm_r,robusts_growth_layer,type = "text", column.labels = table4_label,
          add.lines=c("DhEBV(4)-(2)", str_c(round(ttt[12,1],3),"**(p=",round(ttt[12,4],3),")")))
```
Panel Data不行！One way, two way fixed effect都不行！
建议直接加interaction