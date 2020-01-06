---
layout: post
title:  Sampling smooth paths
date:   2020-01-06
math:   true
---

Sometimes it's useful to generate random smooth paths that hang around the origin.
The simplest way to obtain a random path is to generate a random walk where each step is independent and normally distributed.
To make the path smooth, we have a couple of options: (1) we could smooth the path with a low-pass filter, or (2) we could integrate the random walk again such that the second derivative is normally distributed rather than the velocity.
However, one problem with these approaches is that the paths might stray arbitrarily far from the origin.
Here's a simple technique that I use to generate smooth paths that don't stray too far.

Let's consider the problem of sampling a path of length $N$ in $D$ dimensions.
The idea is simply to sample from a Gaussian distribution with precision matrix (inverse covariance matrix)

$$ \Lambda = I + \alpha \Lambda_{1} + \beta \Lambda_{2} $$

where $\Lambda_1 = D_1^T D_1$ and $\Lambda_2 = D_2^T D_2$ and $D_{i}$ is a finite difference operator of order $i$.

$$ D_1 = \frac{1}{2} \begin{bmatrix} -1 & 1 \\ & -1 & 1 \\ && \ddots & \ddots \\ &&& -1 & 1 \end{bmatrix} $$
$$ D_2 = \frac{1}{4} \begin{bmatrix} -1 & 2 & -1 \\ & -1 & 2 & -1 \\ && \ddots & \ddots & \ddots \\ &&& -1 & 2 & -1 \end{bmatrix} $$

The matrix $D_i$ has shape $(N - i) \times N$ and therefore the $N \times N$ matrix $\Lambda_{i}$ has rank $(N - i)$.

To sample from the distribution $\mathcal{N}(0, \Lambda^{-1})$, we simply compute an eigendecomposition $V D V^T = \Lambda$ and then take $x = D^{-\frac{1}{2}} V^T \epsilon$ where $\epsilon$ is drawn from a unit normal distribution.

## Efficient technique

Work in progress...
