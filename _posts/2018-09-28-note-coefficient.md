---
title: 'Can we use coefficient of determinant for nonlinear regression?'
date: 2018-09-27
excerpt: 'A conjecture that justify the use of R^2.'
permalink: /posts/2018/09/blog-post-note-coefficient-determinant-nonlinear-regression/
tags:
  - regression
  - tsa_class_notes
---

## Abstract

This is a note of my thoughts on R^2 after taking Time Series Analysis class by Prof. Byon. I will make the following assumptions.

1. scalar target: $y \in \mathbb{R}^1$ 
2. data is sampled i.i.d.


## Introduction

Coefficient of determinant arose from the observation in **linear regression** that 

$$
\begin{equation}
SST = SSR + SSE,
\end{equation}
$$

where 

$$
\begin{equation}
\textrm{sum of squared total variance: }SST = \sum_{i} (y_i - \overline{y})^2, \\
\textrm{sum of squared error: }SSE = \sum_{i} (y_i - \hat{y}_i)^2, \\
\textrm{sum of squared regression: }SSR = \sum_{i} (\hat{y}_i - \overline{y})^2.
\end{equation}
$$

The proof is quite ubiquitous in any textbook or [online materials](https://stats.stackexchange.com/questions/207841/why-is-sst-sse-ssr-one-variable-linear-regression). With this equality, one can find a nondimensionalized version of it as, **coefficient of determinant, $R^2$** as 

$$
\begin{equation}
R^2 = \frac{SSR}{SST} = 1 - \frac{SSE}{SST}.
\end{equation}
$$

Note that in the above equation there is two =, which of them is the definition for $R^2$ is not really certain. [Wiki](https://en.wikipedia.org/wiki/Coefficient_of_determination) says it is the second one, while [Jim](http://statisticsbyjim.com/regression/r-squared-invalid-nonlinear-regression/) claim the first one is more natural. Both of them is well defined and equal in the context of *linear regression*. In general, we believe the R^2 is _a statistic that measures how much proportions of variance of the target is explained by predictor variable, excluding the constant_. In such sense, the first one is more natural. 

## Appearance in nonlinear regression

From my viewpoint, there are mainly two aspects of $R^2$  in the context of linear regression, that makes it popular.

1. the **nondimensional property** i.e., we don't have to worry about getting different performance measure over different datasets. In general, $R^2$ over 0.9 is a good indicator of well performed models. Note that if nondimensional property is not favored, for example, we simply interested in one datasets and we don't have issue of measuring performance of models across different datasets with different scales, then simply one can choose to use $RMSE$ as commonly seen in machine learning and fluid dynamic community, or the so-called [standard error of regression](http://statisticsbyjim.com/regression/standard-error-regression-vs-r-squared/). 
2. **variance explanation** property. Note that in the context of linear regression, another equivalent phase for _explaining variance_ is correlation coefficient. It can be shown that the square of Person correlation coefficient between $y$ and $\hat{y}$ is essentially $R^2$.. There are other alternative correlation coefficient out there but not really satisfies me anyway. I will write another post about a _potentially dumb/workable_ nonlinear coefficient. 

However, as it has been mentioned a lot of times that in the context of _nonlinear modeling_, 

$$
\begin{equation}
SST \neq SSR + SSE.
\end{equation}
$$

Therefore, one need to _make a choice for_ the definition for $R^2$. Most of the time, people like to use the one with SSE since minimizing SSE is what we do and the smaller the higher for $R^2$. Note that _it is implemented in_ in [Scikit-learn](https://github.com/scikit-learn/scikit-learn/blob/bac89c2/sklearn/metrics/regression.py#L448) as the SSE is well-defined for both _linear_ and _nonlinear regression_. However, the variance explanation property might not be hold. Because of this issue, there are some negative viewpoint on the usage of $R^2$. 

## The difference might be small for well-trained nonlinear models

The key to make the equality lies in the following condition

$$
\begin{equation}
\sum_{i}(y_i - \hat{y}_i) (\hat{y}_i - \overline{y}) = 0, \\
\rightarrow \sum_{i}\epsilon_i (\hat{y}_i - \overline{y}) = 0.
\end{equation}
$$

Note that  the following two are sufficient conditions for the above, 


$$
\begin{equation}
\sum_{i}\epsilon_i \hat{y}_i = 0, \\
\sum_{i}\epsilon_i \overline{y} = 0.
\end{equation}
$$

The second one is easy, as long as model _takes constant as linear features_

$$
\begin{equation}
\hat{y} = \alpha + g_{\beta}(x).
\end{equation}
$$

One can show that the OLS solution corresponds to the extrema in $\alpha$ would lead to 

$$
\begin{equation}
\sum_{i} \epsilon_i = 0.
\end{equation}
$$

For the first one, we notice the following 

$$
\begin{equation}
0  = \sum_i \epsilon_i \hat{y}_i = \sum_i \epsilon_i(\hat{y}_i - \frac{1}{N}\sum_j \hat{y}_j) \sim \mathbb{Cor}(\epsilon, \hat{y}).
\end{equation}
$$

Note $\epsilon$ is the residual and clearly it is zero mean, if the data is sampled i.i.d, so the _explicitly unweighted_ sum in the above is proportional to the _correlation between residual and prediction_. 

For well-trained, models, [Billings et. al.,](https://www.sheffield.ac.uk/acse/staff/sab) derived several criterions on determining whether the neural network model is well-trained or not. In the _equation 21_ in [their paper](https://www.sheffield.ac.uk/acse/staff/sab),  it shows the above _uncorrelation in the linear sense_ is a condition required, which would certainly leads to 
$$
\begin{equation}
SST \approx SSR + SSE.
\end{equation}
$$
Under the above sense, we should expect that one is able to use $R^2$ for nonlinear regression with the variance explanation property and nondimensionalized property.

