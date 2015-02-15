---
layout: post
title:  Least-squares regression and the difference of means
math:   true
---

Consider the two-class least-squares regression problem

$$
f(w, b) = \frac{1}{2} \sum_{i = 1}^{n} \left( w^{T} x_{i} + b - y_{i} \right)^{2} + \frac{\lambda}{2} \|w\|^{2}
$$

where each $$y_{i}$$ takes one of two distinct values $$\ell_{1} \ne \ell_{2}$$ depending on which class it belongs to.

Setting to zero the derivative with respect to $$b$$ yields

$$
\begin{align}
\sum_{i = 1}^{n} \left( w^{T} x_{i} + b - y_{i} \right) & = 0 \\
b & = \bar{y} - w^{T} \bar{x}
\end{align}
$$

where $$\bar{x} = \frac{1}{n} \sum_{i = 1}^{n} x_{i}$$ and $$\bar{y} = \frac{1}{n} \sum_{i = 1}^{n} y_{i}$$.

Substituting this expression into the objective gives

$$
g(w) = \frac{1}{2} \sum_{i = 1}^{n} \left[ w^{T} (x_{i}-\bar{x}) - (y_{i}-\bar{y}) \right]^{2} + \frac{\lambda}{2} \|w\|^{2}
$$

Then differentiating with respect to $$w$$ and setting the expression to zero, we obtain a linear system of equations for the solution

$$
\left[\sum_{i = 1}^{n} (x_{i}-\bar{x}) (x_{i}-\bar{x})^{T} + \lambda I\right] w =
  \sum_{i = 1}^{n} (y_{i}-\bar{y}) (x_{i}-\bar{x})
$$

### The right-hand side

Noticing that some terms can be eliminated, the right-hand side is re-expressed

$$
r = \sum_{i = 1}^{n} (y_{i}-\bar{y}) (x_{i}-\bar{x}) =
  \sum_{i = 1}^{n} y_{i} x_{i} - n \bar{y} \bar{x}
$$

Let us introduce $$\bar{x}_{1}$$ and $$\bar{x}_{2}$$ to represent the means of the two classes, as well as $$n_{1}$$ and $$n_{2}$$ to denote the number of examples in each class.
We can see that $$n \bar{x} = n_{1} \bar{x}_{1} + n_{2} \bar{x}_{2}$$ and similarly $$n \bar{y} = n_{1} \ell_{1} + n_{2} \ell_{2}$$.

$$
\begin{align}
\sum_{i = 1}^{n} y_{i} x_{i} - n \bar{y} \bar{x}
& = \ell_{1} n_{1} \bar{x}_{1} + \ell_{2} n_{2} \bar{x}_{1}
  - \frac{1}{n} (n_{1} \ell_{1} + n_{2} \ell_{2}) (n_{1} \bar{x}_{1} + n_{2} \bar{x}_{2}) \\
& = \frac{n_{1} n_{2}}{n} (\ell_{1} - \ell_{2}) (\bar{x}_{1} - \bar{x}_{2})
\end{align}
$$

Thus we obtain the somewhat surprising result that $$r \propto \bar{x}_{1} - \bar{x}_{2}$$ regardless of the number of examples in each class or the desired labels $$\ell_{1}$$ and $$\ell_{2}$$.

### The left-hand side

The covariance can be re-expressed

$$
S = \sum_{i = 1}^{n} (x_{i}-\bar{x}) (x_{i}-\bar{x})^{T}
= \sum_{i = 1}^{n} x_{i} x_{i}^{T} - n \bar{x} \bar{x}^{T}
$$

Let us consider the "within-class scatter" matrix from Linear Discriminant Analysis (LDA).
Its terms can be re-arranged

$$
\begin{align}
S_{W} & = \sum_{i \in \mathcal{X}_{1}} (x_{i}-\bar{x}_{1}) (x_{i}-\bar{x}_{1})^{T}
  + \sum_{i \in \mathcal{X}_{2}} (x_{i}-\bar{x}_{2}) (x_{i}-\bar{x}_{2})^{T} \\
& = \sum_{i = 1}^{n} x_{i} x_{i}^{T} - n_{1} \bar{x}_{1} \bar{x}_{1}^{T} - n_{2} \bar{x}_{2} \bar{x}_{2}^{T}
\end{align}
$$

Substituting this into the expression for the covariance matrix gives

$$
\begin{align}
S & = S_{W} + n_{1} \bar{x}_{1} \bar{x}_{1}^{T} + n_{2} \bar{x}_{2} \bar{x}_{2}^{T}
  - \frac{1}{n} (n_{1} \bar{x}_{1} + n_{2} \bar{x}_{2}) (n_{1} \bar{x}_{1} + n_{2} \bar{x}_{2})^{T} \\
& = S_{W} + \frac{n_{1} n_{2}}{n} (\bar{x}_{1} - \bar{x}_{2}) (\bar{x}_{1} - \bar{x}_{2})^{T}
\end{align}
$$

### Altogether now

Introducing $$\delta = \bar{x}_{1} - \bar{x}_{2}$$ for the difference in class means, the system of equations is

$$
\left(S_{W} + \lambda I + \frac{n_{1} n_{2}}{n} \delta \delta^{T}\right) w
  = \frac{n_{1} n_{2}}{n} (\ell_{1} - \ell_{2}) \delta
$$

Finally, let us note an interesting property of systems of the form $$(A + k b b^{T}) x = b$$.
The solution will satisfy $$x \propto A^{-1} b$$ since

$$
A x = b - k b b^{T} x = (1 - k b^{T} x) b
$$

This allows us to draw the conclusion that

$$
w \propto (S_{W} + \lambda I)^{-1} (\bar{x}_{1} - \bar{x}_{2})
$$
