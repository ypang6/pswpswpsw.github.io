---
title: 'Adjusted $R^2$ is a weaker penalty than AIC'
date: 2018-09-27
permalink: /posts/2018/09/blog-post-adjust-r2-weaker-than-aic/
tags:
  - regression
  - tsa_class_notes
---

### Abstract

This is a note of thought that I bump into randomly. 

It has been a long history in statistics on model selection that penalizes the exploding number of parameters. **AIC, i.e., Alkaline information criterion**, is perhaps the most famous one due to its simplicity and generality. While, when $R^2$ is introduced in the class, immediately adjusted $R^2$ is introduced. The later one does not follow the "variance explanation" per se since there is no guarantee about the ratio being kept in $[0,1]$. But, it is supposed to penalize large number of parameters by showing a lower $R^2$. 

### Introduction

Before discussion, let's make the definition clear. $n \in \mathbb{N}$ is the total number of samples. $p$ is the number of predictors (excluding 1).

#### Adjusted $R^2$

The difference between common $R^2$ and adjusted $R^2$ is that, **adjusted $R^2$ considers the variance explanation by taking independency into account.** Therefore, the more parameter you have, the residuals of all data, would be **less** independent simply due to more and more constraints are imposed by the OLS formulation. 

The expression for adjusted $R^2$ is

$$
\begin{equation}
\overline{R}^2 \triangleq 1 - \dfrac{SSE}{SST} \dfrac{n-1}{n-p-1}.
\end{equation}
$$

### Minimal Description Length (MDL)

AIC originates in information theory. In Bayes belief net, there is a criterion called **Minimal Description Length (MDL)**. One would like to choose the Bayes belief net models with the **shortest** MDL.

In general, for a given training set $D = \{ {x}_1,\ldots,{x}_m \}$, the scoring function on Bayes net $B = \langle G, \Theta \rangle$ on the training set $D$ is

$$
\begin{equation}
s(B|D) = f(\theta)|B| - LL(B|D),
\end{equation}
$$

where $$f(\theta)$$ is the bits required to describe each parameter while $$\vert B \vert$$ is the number of parameters in the Bayes net. 

$$
\begin{equation}
LL(B|D) = \sum_{i=1}^m \log P_B({x}_i) \sim -\frac{m}{2} \log(\sigma^2) -\frac{1}{2\sigma^2} SSE \sim -\frac{m}{2}\log(SSE/m),
\end{equation}
$$

which is the log-likelihood of all data and $\sigma^2$ is the uncertainty in the likelihood model. Note that $\sigma^2 = SSE/m$ as a MLE estimation for the residual variance. Remember that this implies i.i.d, i.e., independently identical distribution. 

MDL induces several concepts which are shown below without proof. Note that the sources are from [here](https://www.goodreads.com/book/show/31193897-machine-learning).

- AIC

$$
\begin{equation}
AIC(B|D) = |B| - LL(B|D) = p - \frac{1}{2\sigma^2} SSE,
\end{equation}
$$

which assumes each parameter costs $1$ bit for description.

- BIC

$$
\begin{equation}
BIC(B|D) = \frac{\log m}{2}|B| - LL(B|D) = \frac{\log m}{2}p - \frac{1}{2\sigma^2} SSE,
\end{equation}
$$

which assumes each parameter costs $\log m /2$ bits for descriptions.

### Adjusted $R^2$ penalize weaker than AIC/BIC

Note that for model selection, we hope to select the one maximize criterion. 

Start with $\overline{R}^2$, so it is equivalent to minimize the $\dfrac{SSE}{SST} \dfrac{n-1}{n-p-1}$. Since $SST$ is fixed by the given data and $n$ is also fixed. It is simply the one that minimize the 

$$
\begin{equation}
SSE/(n-p-1)
\end{equation}
$$

while it does not hurt to take the $\log$ and add/minus constants so
$$
\begin{equation}
\log SSE/n + \log \frac{1}{n(1-(p+1)/n)} = \log SSE/n + \log \frac{1}{n} + \log \frac{1}{1-(p+1)/n} \\ 
\sim \log SSE/n  +\log \frac{1}{1-(p+1)/n} 
\end{equation}
$$


Second, for AIC/BIC, the equivalent quantity to minimize is 

$$
\begin{equation}
2 C p + n \log (SSE/n),
\end{equation}
$$

where $C = 1, \frac{\log n}{2}$ for AIC and BIC respectively. 

Note that $n$ is a constant, therefore it is equivalent to minimize

$$
\begin{equation}
2 C p/n + \log (SSE/n) \sim 2 C (p+1)/n + \log (SSE/n).
\end{equation}
$$

Clearly, the ratio between the $\log \frac{1}{1-(p+1)/n}$ and $2 C (p+1)/n$ determines the relative penalization between adjusted $R^2$ and AIC/BIC. 

First, let's investigate this ratio as follows

$$
\begin{equation}
f(x) = \frac{\log(\frac{1}{1-x})}{x} \ge 1, \forall x \in (0,1).
\end{equation}
$$

To see this, note $f(0^+) = 1$ and one simply take $f'(x)$ as 

$$
\begin{equation}
f'(x) = \frac{\frac{x}{1-x} - \log \frac{1}{1-x}}{x^2} \\
= \frac{ \frac{1}{1-x} - \log \frac{1}{1-x} - 1}{x^2} \ge 0, \forall x \in (0,1).
\end{equation}
$$

Therefore, take $x  = \frac{p+1}{n}$, we have it as a monotonic increasing function with respect to $(p+1)/n$ and when $p \ll n$,  we have the ratio between adjusted $R^2$ and AIC/BIC as the following:

$$
\begin{equation}
\frac{1}{2} \lt 2c
\end{equation}
$$

## \frac{\log(1/(1-(p+1)/n))}{(p+1)/n} < 2C,

where $C = 1, \frac{\log n}{2} $.
