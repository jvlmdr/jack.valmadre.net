---
layout: post
title:  Variance of a function of two random variables
date:   2018-05-16
math:   true
---

Suppose that we want to compute $\operatorname{Var}[f(X, Y)]$ where both $X$ and $Y$ are independent random variables.
Imagine that, for a single sample $X = x$, we have an efficient method to estimate $\operatorname{Var}[f(x, Y)]$.
For example, if $Y$ is a testing set and $f(X, Y)$ is the mean performance on the testing set, then its variance can be obtained efficiently using the bootstrap estimator.
Then, the question is, how can we compute the $\operatorname{Var}[f(X, Y)]$ using this estimator?

Note that we will use lower-case to denote a normal variable and upper-case to denote a random variable.

First, let's look at the decomposition of the variance that we can compute easily

$$\begin{aligned}
\operatorname{Var}[f(x, Y)] & = \mathbb{E}[\{f(x, Y) - \mathbb{E}[f(x, Y)]\}^2] \\
& = \mathbb{E}[f(x, Y)^2] - (\mathbb{E}[f(x, Y)])^2 \\
\operatorname{Var}[f(x, Y)] + (\mathbb{E}[f(x, Y)])^2 & = \mathbb{E}[f(x, Y)^2]
\end{aligned}$$

Now, let's consider the mean and variance of the quantity that we are interested in

$$\begin{aligned}
\mathbb{E}[f(X, Y)] & = \int_x \int_y f(x, y) p(x) p(y) dx dy \\
& = \int_x \left( \int_y f(x, y) p(y) dy \right) p(x) dx \\
& = \int_x \mathbb{E}[f(x, Y)] p(x) dx
\end{aligned}$$

$$\begin{aligned}
\operatorname{Var}[f(X, Y)] & = \mathbb{E}[f(X, Y)^2] - (\mathbb{E}[f(X, Y)])^2
\end{aligned}$$

The first term of the variance is

$$\begin{aligned}
\mathbb{E}[f(X, Y)^2] & = \int_x \int_y f(x, y)^2 p(x) p(y) dx dy \\
& = \int_x \left( \int_y f(x, y)^2 p(y) dy \right) p(x) dx \\
& = \int_x \mathbb{E}[f(x, Y)]^2 p(x) dx
\end{aligned}$$

Hence, the variance can be computed

$$\begin{aligned}
\operatorname{Var}[f(X, Y)] & = \int_x \mathbb{E}[f(x, Y)]^2 p(x) dx - \left( \int_x \mathbb{E}[f(x, Y)] p(x) dx \right)^2 \\
& = \int_x \left( \operatorname{Var}[f(x, Y)] + (\mathbb{E}[f(x, Y)])^2 \right) p(x) dx - \left( \int_x \mathbb{E}[f(x, Y)] p(x) dx \right)^2
\end{aligned}$$
