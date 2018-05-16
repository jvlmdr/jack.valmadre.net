---
layout: post
title:  Variance of a function of two random variables
date:   2018-05-16
math:   true
---

Suppose that we want to compute $\operatorname{Var}[f(X, Y)]$ where $X$ and $Y$ are independent random variables.
Imagine that, for a single sample $X = x$, we have an efficient method to estimate $\operatorname{Var}[f(x, Y)]$.
For example, if $Y$ is a testing set and $f(x, Y)$ is the mean performance on the testing set, then its variance can be obtained efficiently using the bootstrap estimator.
Note that we will use lower-case to denote a normal variable and upper-case to denote a random variable.

Let $M(x)$ and $S(x)$ denote the mean and variance that can be computed efficiently

$$\begin{aligned}
M(x) & = \mathbb{E}[f(x, Y)] & 
S(x) & = \operatorname{Var}[f(x, Y)]
\end{aligned}$$

Then, the question is, how can we compute $\operatorname{Var}[f(X, Y)]$ in terms of $M(X)$ and $S(X)$?

First, let's look at the decomposition of the variance that we can compute easily

$$\begin{aligned}
S(x) & = \mathbb{E}[f(x, Y)^2] - (\mathbb{E}[f(x, Y)])^2 \\
& = \mathbb{E}[f(x, Y)^2] - M^2(x)
\end{aligned}$$

Now, let's examine the joint mean and variance

$$\begin{aligned}
\mathbb{E}[f(X, Y)] & = \int_x \int_y f(x, y) p(x) p(y) dx dy \\
& = \int_x \left( \int_y f(x, y) p(y) dy \right) p(x) dx \\
& = \int_x \mathbb{E}[f(x, Y)] p(x) dx = \mathbb{E}[M(X)]
\end{aligned}$$

$$\begin{aligned}
\operatorname{Var}[f(X, Y)] & = \mathbb{E}[f(X, Y)^2] - (\mathbb{E}[f(X, Y)])^2
\end{aligned}$$

The first term of the variance is

$$\begin{aligned}
\mathbb{E}[f(X, Y)^2] & = \int_x \int_y f(x, y)^2 p(x) p(y) dx dy \\
& = \int_x \left( \int_y f(x, y)^2 p(y) dy \right) p(x) dx \\
& = \int_x \mathbb{E}[f(x, Y)^2] p(x) dx \\
& = \int_x \{ S(x) + M^2(x) \} p(x) dx \\
& = \mathbb{E}[S(X) + M^2(X)]
\end{aligned}$$

Hence, the joint variance can be computed entirely in terms of $M(X)$ and $S(X)$.

$$\begin{aligned}
\operatorname{Var}[f(X, Y)]
& = \mathbb{E}[f(X, Y)^2] - (\mathbb{E}[f(X, Y)])^2 \\
& = \mathbb{E}[S(X) + M^2(X)] - (\mathbb{E}[M(X)])^2 \\
& = \mathbb{E}[S(X)] + \mathbb{E}[M^2(X)] - (\mathbb{E}[M(X)])^2 \\
& = \mathbb{E}[S(X)] + \operatorname{Var}[M(X)]
\end{aligned}$$

This is the mean of the variances plus the variance of the means.
