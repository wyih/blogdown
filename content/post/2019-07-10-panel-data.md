---
title: Panel Data
author: wyih
date: '2019-07-10'
slug: panel-data
markup: mmark
categories:
  - R
tags:
  - econometrics-with-r
subtitle: ''
summary: ''
authors: []
lastmod: '2019-09-25T16:01:36+08:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

## 效應評估模型

$$
mrall=mrall_{-BeerTax}+\beta^*BeerTax
$$

提高啤酒稅（BeerTax）是否有助減低車禍死亡率（mrall）？

## 固定效應模型

令 $W$代表「州愛喝酒程度」。

* $W$與 $mrall_{-BeerTax}+$有關
* $W$與 $BeerTax$有關

$$
mrall=(mrall_{-BT}-\mathbb{E}(mrall_{-BT}|W))+\mathbb{E}(mrall_{-BT}|W) + \beta^*BeerTax
$$

$$
mrall_{-BT,-W}\equiv mrall_{-BT}-\mathbb{E}(mrall_{-BT}|W)
$$

$$
mrall=mrall_{-BT,-W}+\mathbb{E}(mrall_{-BT}|W)+\beta^*BeerTax
$$

$mrall_{-BT,-W}$為「去除」 $W$影響的「非啤酒稅造成的車禍死亡因素」：

* 它與 $W$無關。
* 若兩筆obs有相同飲酒文化，即$W$相同，他們的 $\mathbb{E}(mrall_{-BT}|W)$
  會相同。

> 「假設」一個地方的飲酒文化「不隨時間改變」，即同一州在不同時點的$W$相同。

令$$\mathbb{E}(mrall_{-BT,it}|W_i)=\alpha_i$$， 故我們的效應模型可以寫成：
$$
mrall_{it}=mrall_{-BT,-W,it}+\alpha_i+\beta^*BeerTax_{it}
$$
其中$\alpha_i$為第 $i$ 個州的固定效果：

* $BearTax$與$mrall_{-BT,-W}$無關
* $BearTax$與$\alpha$有關

## 組內差異最小平方法

差分OLS解决$\alpha_i$不可得的阻碍

$$
mrall_{i1}-mrall_{i0}=\beta^* (BeerTax_{i1}-BearTax_{i0})+(mrall_{-BT,-W,i1}-mrall_{-BT,-W,i0})
$$

如果$t$超過兩期，考慮用組內平均為差分比較的點。

即$$x_1-\bar{x},x_2-\bar{x},...,x_n-\bar{x}, \bar{x}=\sum_{i=1}^n x_i/n$$
$$
\bar{mrall}_i=\sum_{t=1}^T mrall_{it}/T \\
\bar{BeerTax}_i=\sum_{t=1}^T BeerTax_{it}/T\\
\bar{mrall}_{-BT,-W,i}=\sum_{t=1}^T mrall_{-BT,-W,it}/T
$$

$$
mrall_{it}-\bar{mrall}_i=\beta^*\left( BeerTax_{it}-\bar{BeerTax}_i\right)+(mrall_{-BT,-W,it}-\bar{mrall}_{-BT,-W,i})
$$

固定效果模型下，我們可以以最小平方法估計下面的迴歸式：
$$
mrall_{it}-\bar{mrall}_i=\beta_0+\beta_1\left( BeerTax_{it}-\bar{BeerTax}_i\right)+\epsilon_{it}
$$
其中$\hat{\beta}_1$即為$\beta^*$的一致性估計

## 常見的固定效果模型

* Identity fixed effect:$\alpha_i$
* Time fixed effect:  $\delta_i$

$$
mrall_{-BT,it}=mrall_{-BT,-W_i,-Z_t}+\alpha_i+\delta_t
$$

* $W_i$為造成效應係數估計偏誤的變數，它在$i$面向固定不變。
* $Z_t$為造成效應係數估計偏誤的變數，它在$t$面向固定不變。

> 如$Z_t$為全美國的景氣狀況。

對應的迴歸模型：
$$
mrall_{it}=\alpha_i+\delta_t+\beta_1 BeerTax_{it}+\epsilon_{it}
$$

## 廣義的固定效果模型

$$
mrall=mrall_{-BeerTax}+\beta^*BeerTax
$$

但
$$
\begin{equation}
  mrall_{-BT,it}\not\perp BeerTax_{it}
  \tag{5.1}
\end{equation}
$$

### 複迴歸控制

先思考造成(5.1)的變數有哪些——統計上稱這些變數為混淆變數(confounder)。Confounder中有資料的（令為$Z$）可進一步用來擴充模型成為：
$$
mrall_{it}=mrall_{-BT,-Z,it}+\beta^*BeerTax_{it}+\gamma'Z_{it}
$$
其中： 
$$
mrall_{-BT,-Z}=mrall_{-BT}-\mathbb{E}(mrall_{-BT}|Z)
$$

### 固定效果模型

Confounder中沒有資料但在某些面向固定的，假設分成以下兩類：

* $W_i$：在同個identity下固定。
* $V_t$：在同個time下固定。

$$
\begin{eqnarray}
mrall_{it}=mrall_{-BT,-(Z,W,V),it}+\beta^*BeerTax_{it}+\\
\alpha_i+\delta_t+\gamma'Z_{it}
\tag{5.2}
\end{eqnarray}
$$

(5.2)是相當廣義的固定效果效應模型——有兩個面向的固定效果及控制變數。

## 隨機效果模型

$$
mrall_{it}=mrall_{-BT,-Z,it}+\beta^*BeerTax_{it}+\gamma'Z_{it}
$$

隨機效果模型(Random Effect model)的設定：

* 使用迴歸模型：

$$
\begin{eqnarray}
  mrall_{it}=\beta_0+\beta_{1}BeerTax_{it}+\gamma'Z_{it}+\nu_{it}
  \tag{5.3}
\end{eqnarray}
$$

* 假設$\nu_{it}$ 具有某種結構。

其中假设：

* $\nu_{it}\perp BeerTax_{it}$
* $$var(\alpha_i|X)=\sigma_{\alpha}^2$$
* $var(\epsilon_{it}|X)=\sigma^2$
* $cov(\epsilon_{it},\epsilon_{is}|X)=0$

> 隨機效果模型帶有高度誤差項假設，故不建議使用。

## Hausman檢定

### 固定效果模型(FE)

表示使用組內差異最小平法方去估算以下迴歸模型中的$$\beta_1$$:
$$
mrall_{it}=\beta_0+\beta_{1}BeerTax_{it}+\gamma'Z_{it}+\alpha_i+\epsilon_{it}
$$

### 隨機效果模型(RE)

表示使用GLS去估算以下迴歸模型中的$$\beta_1$$:
$$
mrall_{it}=\beta_0+\beta_{1}BeerTax_{it}+\gamma'Z_{it}+\nu_{it}
$$

* $$\nu_{it}=\alpha_i+\epsilon_{it}$$

假設

* RE下「關於variance、covariance的假設」都成立。
* $$\epsilon_{it} \perp BeerTax_{it} | \alpha_i,Z_{it}$$

**H0:** $$\alpha_i \perp BeerTax_{it} |Z_{it}$$

H0为RE，拒绝则为FE