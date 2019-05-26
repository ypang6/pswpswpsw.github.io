---
title: 'Sensitivity of warm-start in computing MultitaskElasticNet path by coordinate descent in Sklearn'
date: 2019-01-26
excerpt: 'We found warm-start strategy can be critical for computing MultitaskElasticNet path using the coordinate descent implemented in Sklearn.'
permalink: /posts/2018/09/blog-post-sensitivity-multi-task-elastic-net-warm-starts/
tags:
  - regularized linear model
  - Sklearn
---

## Abstract

This post describes a phenomena that we encounter in computing MultitaskElasticNet path, i.e., computing the coefficients of MultitaskElasticNet model with sweeping sparsity regularization parameter $\alpha$. We solve this problem by manually setting up the warm starts and everything works as expected.

## What kind of problem does MultiTaskElasticNet solve?

### Linear regression

Most useful scientific problem in daily life can be cast into linear regression problem if the features are well designed. 
So let's begin with a standard linear regression problem, with $N$ as number of data points, $M$ as the dimension of the data,  $P$ as the number of features used. To find the $W$ with least square of the residuals, we simply solve the following problem,   
$$
\min \lVert Y - XW \rVert^2_{F},
$$
where $X \in \mathbb{R}^{N \times P}$ are features, $Y \in \mathbb{R}^{N \times M}$ are  targets, $W \in \mathbb{R}^{P \times M}$ are model coefficients.

### Unique and sparse solution is preferred in modeling scientifc problem  

In general, the above problem can be viewed as a linear inversion problem especially if the problem is ill-condition either due to lack of observations or the heavily correlated features. Thus simply solving the above least square problem won't give us unique solution. However, most of the ground truth behind a inversion problem in scientic community is unique. So what can we do? The standard procedure is to consider regularization: let model to prefer a certain type of solution, for example, sparsity, which is ideal in most of our cases. This naturally leads to the development of **[LASSO](https://en.wikipedia.org/wiki/Lasso_(statistics))**. This would be helpful in noisy data since the model will not only consider MSE but also the sparsity of the solution $W$. 

Further, for cases where features are correlated, even though the truth is a unique solution. Certainly there is no unique solution to the optimization problem, even LASSO the typical $L1$ sparsity is considered. To at least uniquely determine the solution, **[ElasticNet](https://en.wikipedia.org/wiki/Elastic_net_regularization)** is proposed based on **LASSO** simply adding a $L2$ regularization. One needs to carefully tune the **L2** regularization though.

### MultiTask learning: dominant features are shared across different tasks

Most of the time, if there is a multi-output (multi-task) linear regression problem, besides sparsity and uniqueness of the solution, another desired property often overlooked is: dominant features are **shared** across different tasks. Similar as before, one can come up with a loss function that considers preference for this desired property. For example, consider the following penalty on $W$
$$
\lVert W \rVert_{21} = \sum_{i} \sqrt{\sum_{j} W_{ij}^2 }.
$$

This $L_{2,1}$ is first proposed by [Argyriou et al.](https://ttic.uchicago.edu/~argyriou/papers/mtl_feat.pdf) in 2008. It can be thought as first compute the 2-norm for each row, and then compute 1-norm on the resulting norm vector. Following the similar spirit in LASSO, the second step encourages the **the number of zero rows** in the solution $W$, which is encouraging **a small subset of features**, i.e., common features across all tasks. Now. let's upgrade the previous problem of **ElasticNet** into the following **MultiTaskElasticNet**, we have the new objective function, 
$$
\begin{equation}
\frac{1}{N}  \lVert Y - XW \rVert_{F}^2
+ \alpha  c   \lVert W  \rVert_{21}
+ 0.5 \alpha (1 - c)  \lVert W \rVert_{F}^2
\end{equation}.
$$

## Does it work? A toy example show the sensetivity of warm start.

Download the case:
```
git clone https://github.com/pswpswpsw/example_sensetivity_initial_guess_MultiTaskElasticNet.git
```
where I have prepared a case with 1600 data points, for a two tasks regression with 14 features.

Our goal is to draw the path of the coefficient, i.e., find the optimal $W$ with each time varying the regularization coefficients $\alpha$ while keep $c$ fixed as 0.5. To optimize the aforementioned MultiTaskElasticNet loss function, we simply take the Sklearn implementation of [MultiTaskElasticNet](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.MultiTaskElasticNet.html). Alternatively, one can also call the [enet_path](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.enet_path.html). 

Then
```
python test.py
```

It is surprise to see that the results are different between **MultiTaskElasticNet** and **enet_path** even with tuned optimization hyperparameters, say increasing the number of iterations. While the later reaches a better local optimum than the former one. Note that **MultiTaskElasticNet** calls the **enet_path**. So there must be something weird! 

Recall that the algorithm in Sklearn is just a simple coordinate descent algorithm but usually sufficient and fast for linear models, the issue turns out to be whether or not reuse previous solution as initial condition. In default, **MultiTaskElasticNet** calls **enet_path** every single time but explicitly disable resuing the coefficient in default. However, directly using **enet_path**  will enable the reusing ofthe coefficient. 


# Acknowledgement

I thank [Alex Sun](https://www.linkedin.com/in/weitao-sun/) for debugging to figure out the issue of initial condition and [Alexandre Gramfort](http://alexandre.gramfort.net/) for noticing the warm_start could be an issue. 