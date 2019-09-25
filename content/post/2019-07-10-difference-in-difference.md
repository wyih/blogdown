---
title: Difference in Difference
author: wyih
date: '2019-07-10'
slug: difference-in-difference
markup: mmark
categories:
  - R
tags:
  - econometrics-with-r
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T15:55:22+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---
## 效應評估模型

“提高最低工資是否會減少就業？”

“最低工資提高是否餐廳的全職員工數會減少？”

假設 $MinWage$為「最低工資有提高」的虛擬變數， $FEmp$為餐廳全職員工數。

$$
FEmp_i=FEmp_{0,i}+\beta^*MinWage_i
$$

$$
FEmp_i=\beta_0+\beta_1 MinWage_i+\epsilon_i
$$

「沒有受到最低工資提高影響下的員工數」$FEmp_{0,i}$與「有無受到最低工資提高影響」无關时OLS是一致估计。

令 $s$表示餐廳所屬的州，則原本的效應模型可以寫成：
$$
\begin{eqnarray}
FEmp_{is}=FEmp_{0,is}+\beta^*MinWage_{s}
\tag{7.1}
\end{eqnarray}
$$

|           | Pre  | Post           |
| --------- | ---- | -------------- |
| Control   |      | $MinWage=1$:PA |
| Treatment |      | $MinWage=1$:NJ |

## 複迴歸模型

餐廳的型態（大型連鎖、咖啡店、小吃店等等）會影響員工僱用量。
$$
\begin{eqnarray}
FEmp_{is} =FEmp_{0,-type,is}+\beta^*MinWage_s+\gamma'type_{is}
\tag{7.2}
\end{eqnarray}
$$
其中
$$
FEmp_{0,-type,is}=FEmp_{0,is}-\mathbb{E}(FEmp_{0,is}|type_{is})
$$

> 在思考怱略變數偏誤(omitted variable bias)時，可能的confounder都必需放在（依實驗組/控制組分的）加總層級來思考。

## 固定效果

### 組固定效果


$$
FEmp_{is}=FEmp_{0,is}+\beta^*MinWage_{s}
$$

多數時候實驗組/控制組在政策還沒施行前，他們就存在組間的特質差異，也就是
$$
FEmp_{0,is}=FEmp_{0,-\alpha_s,is}+\alpha_s
$$
其中$\alpha_s$ 代表因組而異的confounder效果。

若沒有其他confounder，我們可以估計以下迴歸模型：
$$
FEmp_{ist}=\alpha_s+\beta^* MinWage_{st}+\epsilon_{ist}
$$

### 時間固定效果

$$
FEmp_{ist}=FEmp_{0,-(\alpha_s,\delta_t),ist}+\alpha_s+\delta_t+\beta^*MinWage_{st}
$$

所對應的迴歸模型為：
$$
FEmp_{ist}=\alpha_s+\delta_t+\beta^* MinWage_{st}+\epsilon_{ist}
$$

### 資料追踪/不追踪

雖然$FEmp_{ist}$ 有到個別餐廳（即有下標 $i$），然而固定效果只到組層級（即下標 $s$)，因此在估計上我們並不需要追踪同一家餐廳——各期抽樣的餐廳可以不同。

## DiD 估计法

$$
\begin{eqnarray}
FEmp_{ist}=\alpha_s+\delta_t+\beta^*MinWage_{st}+\epsilon_{ist}
\tag{7.3}
\end{eqnarray}
$$

$$
FEmp_{ist}=\beta_0+\alpha_1D1_s+\delta_1B1_t+\beta_1MinWage_{st}+\epsilon_{ist}
$$

* 令$D1=1$代表來自第1個州（NJ）的虛擬變數。
* 令$B1 = 1$代表政策施行「後」的虛擬變數。
* $MinWage_{st}=D1_s\times B1_t$

| State | t=0       | T=1       |
| ----- | --------- | --------- |
| NJ    | D1=1,B1=0 | D1=1,B1=1 |
| PA    | D1=0,B1=0 | D1=0,B1=1 |

## cluster standard error

我們有G1-G4共四群誤差項的變異數及跨群間的共變異數需要去留意，當誤差項有聚類（clustering）可能時，必需要適當的調整估計式標準誤。