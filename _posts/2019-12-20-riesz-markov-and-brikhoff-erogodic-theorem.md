---
title: 'Observation on some similarity between Riesz-Markov Theorem and Birkhoff theorem'
date: 2019-12-20
excerpt: 'Some difference and some similarity.'
permalink: /posts/2019/12/blog-post-observation-riesz-markov-birkhoff/
tags:
  - ergodicity
---

# Two theorems at a glance.
## Riesz - Markov Theorem

**Theorem**: Let $X$ be a locally compact Hausdorff space. For any positive linear functional $\psi$ on $C(X)$, there is a unique regular Borel measure $\mu$ on $X$ such that

$$
\begin{equation}
\forall f \in C(X), \psi(f) = \int_X f(x) d\mu(x)
\end{equation}
$$

## Birkhoff's ergodic theorem

**Theorem**: Let $(X, \mathcal{B}, \mu, T)$ be a measure-preserving system ($T: X \mapsto X$ is measure-preserving transformation). For any $f \in \mathcal{L}_{\mu}^1$, 

$$
\begin{equation}
\lim_{n\rightarrow \infty} \frac{1}{n} \sum_{i=0}^{n-1} f\circ T^i(x) = \int_X f d\mu,
\end{equation}
$$

is true almost everywhere in $X$. 

# Discussions

First, both right hand sides are the same. 

If we take the finite approximation of the left hand side on the second equation, denote $\mathcal{K}$ as the Koopman operator on the measure-preserving system associated with $T$, then we have 

$$
\begin{equation} 
\frac{1}{n} \sum_{i=0}^{n-1} f \circ T^i(x) = \left(\frac{1}{n} \sum_{i=0}^{n-1} \mathcal{K}^i\right) f \triangleq \bar{\mathcal{K}}_n f. 
\end{equation}
$$

Since $\bar{\mathcal{K}_n}$ is a linear operator (so as the corresponding limit) rather than a positive linear functional, one cannot directly apply RMT to obtain Birkhoff theorem. However, I guess the ergodic nature makes the linear operator evaluated pointwise resembles a linear functional. But there is still some difference that makes them quite different. 



Just to take the note here to not confuse one with another. 
