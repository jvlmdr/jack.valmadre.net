---
layout: post
title:  Derivative of non-linear least-squares minimiser
date:   2015-08-31 18:00:00
math:   true
---

If $y^{\star}(x)$ is the minimiser of a non-linear least-squares problem

$$
y^{\star}(x) = \arg \min_{y} \|r(x, y)\|
$$

where function $r$ maps $x \in \mathbb{R}^{m}$ and $y \in \mathbb{R}^{n}$ to a vector in $r(x, y) \in \mathbb{R}^{p}$, then how can we estimate its derivative

$$
\frac{\partial y^{\star}}{\partial x}(x) \enspace ?
$$

This page uses some techniques from the [generalised Wiberg paper of Strelow (pdf)](http://www.dennis-strelow.com/papers/documents/strelow_cvpr12.pdf).
It also makes use of some notation and results from [the post about the linear problem]({% post_url 2015-08-31-deriv-lsq %}).

Assume that we are able to obtain an estimate of the minimiser $\hat{y} \approx y^{\star}(x)$.
The true minimiser can be represented as a displacement from this estimate $y^{\star}(x) = \hat{y} + \delta y^{\star}(x)$.
The derivative of this displacement with respect to $x$ is equal to that of the minimiser

$$
\frac{\partial y^{\star}}{\partial x}
= \frac{\partial \delta y^{\star}}{\partial x} \enspace .
$$

If the estimate is good, then the optimal displacement $\delta y^{\star}(x)$ is small and

$$
r(x, \hat{y} + \delta y)
\approx r(x, \hat{y}) + \left[\frac{\partial r}{\partial y}(x, \hat{y})\right] \delta y
$$

and hopefully (perhaps this can be shown with Lipschitz continuity?)

$$
\begin{align}
\delta y^{\star}(x) & = \arg\min_{\delta y} \|r(x, \hat{y} + \delta y)\|^{2} \\
& \approx \arg\min_{\delta y}
  \left\|r(x, \hat{y}) + \left[\frac{\partial r}{\partial y}(x, \hat{y})\right] \delta y\right\|^{2} \enspace .
\end{align}
$$

This is the solution to a linear least-squares objective

$$
\delta y^{\star}(x) = \arg\min_{\delta y} \|A(x) \cdot \delta y - b(x)\|^{2}
$$

where $A(x) = \frac{\partial r}{\partial y}(x, \hat{y})$ is $p \times n$ and $b(x) = -r(x, \hat{y})$.
The derivative of this expression is

$$
\frac{\partial \delta y^{\star}}{\partial x}
= \underbrace{\left[\frac{\partial}{\partial a} A^{\dagger} b\right]}_{n \times (p, n)}
  \underbrace{\left[\frac{\partial}{\partial x} \operatorname{vec} A(x)\right]}_{(p, n) \times m}
  + \underbrace{\left[\frac{\partial}{\partial b} A^{\dagger} b\right]}_{n \times p}
  \underbrace{\left[\frac{\partial}{\partial x} b(x)\right]}_{p \times m}
$$

where $A^{\dagger} = (A^{T} A)^{-1} A^{T}$ is the left-inverse and $a \equiv \operatorname{vec}(A)$.

The derivatives of $\delta y^{\star}$ with respect to $A$ and $b$ are known from [the post about the linear problem]({% post_url 2015-08-31-deriv-lsq %}).
The derivatives of the parameters of the linear system are

$$
\frac{\partial}{\partial x} \operatorname{vec} A(x)
  = \underbrace{\frac{\partial}{\partial x} \operatorname{vec}\underbrace{\left(\frac{\partial r}{\partial y}(x, \hat{y})\right)}_{p \times n}}_{(p, n) \times m}
$$

$$
\frac{\partial}{\partial x} b(x)
  = -\frac{\partial r}{\partial x}(x, \hat{y})
$$

This is enough to compute derivatives

<script src="https://gist.github.com/jvlmdr/1238cd9815524523e83a.js"></script>

The complete source for this experiment can be [found on Github](https://github.com/jvlmdr/arg-min-deriv).

The overall expression for the derivative can be found

$$
\begin{align}
\frac{\partial \delta y^{\star}}{\partial x}
& = \left\{-[(\delta y^{\star})^{T} \otimes G^{-1} A^{T}] - [(A \delta y^{\star} - b)^{T} \otimes G^{-1}] J_{m n}\right\}
  \left[\frac{\partial}{\partial x} \operatorname{vec}\left(\frac{\partial r}{\partial y}(x, \hat{y})\right)\right] \\
& \qquad - A^{\dagger} \left(\frac{\partial r}{\partial x}(x, \hat{y})\right)
\end{align}
$$

where $G = A^{T} A$ and $J_{m n}$ is the linear operator such that $\operatorname{vec}(X^{T}) = J_{m n} \operatorname{vec}(X)$ if $X$ is $m \times n$.

There might be some tensor notation to better express this.
The derivatives with respect to single elements of $x$ at least have a neater expression

$$
\frac{\partial}{\partial x_{i}} \operatorname{vec} A(x)
  = \frac{\partial}{\partial x} \operatorname{vec} \left(\frac{\partial r}{\partial y}(x, \hat{y})\right)
  = \operatorname{vec}
    \underbrace{\left(\frac{\partial^{2} r}{\partial x_{i} \partial y}(x, \hat{y})\right)}_{p \times n}
$$

$$
\begin{align}
\frac{\partial \delta y^{\star}}{\partial x_{i}}
& = -G^{-1} \left\{ A^{T} ( C_{i} \delta y^{\star} + d_{i} )
  + C_{i}^{T} (A \delta y^{\star} - b) \right\}
\end{align}
$$

where $C_{i}$ is a $p \times n$ matrix and $d_{i}$ is a vector of length $p$

$$
\begin{align}
C_{i} & = \frac{\partial^{2} r}{\partial x_{i} \partial y} (x, \hat{y}) \enspace , &
d_{i} & = \frac{\partial r}{\partial x_{i}}(x, \hat{y}) \enspace .
\end{align}
$$

Therefore it is necessary to compute $\frac{\partial r}{\partial x}(x, \hat{y})$, $\frac{\partial r}{\partial y}(x, \hat{y})$ and $\frac{\partial^{2} r}{\partial x \partial y}(x, \hat{y})$ as well as the QR factorisation of $\frac{\partial r}{\partial y}(x, \hat{y})$.
