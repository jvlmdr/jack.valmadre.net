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

Let's consider the problem of sampling a path of length $N$ in $K$ dimensions.
The idea is simply to sample from a Gaussian distribution with precision matrix (inverse covariance matrix)

$$ \Lambda = I + \alpha \Lambda_{1} + \beta \Lambda_{2} $$

where $\Lambda_1 = D_1^T D_1$ and $\Lambda_2 = D_2^T D_2$ and $D_{i}$ is a finite difference operator of order $i$.

$$ D_1 = \begin{bmatrix} -1 & 1 \\ & -1 & 1 \\ && \ddots & \ddots \\ &&& -1 & 1 \end{bmatrix} $$

$$ D_2 = \begin{bmatrix} -1 & 2 & -1 \\ & -1 & 2 & -1 \\ && \ddots & \ddots & \ddots \\ &&& -1 & 2 & -1 \end{bmatrix} $$

Then finite difference matrix $D_i$ has shape $(N - i) \times N$.

To sample from the distribution $\mathcal{N}(0, \Lambda^{-1})$, we need a matrix $A$ such that $A A^{T} = \Lambda^{-1}$.
This can be obtained by computing an eigendecomposition $\Lambda = V D V^T$ and setting $A = V D^{-\frac{1}{2}}$.
To obtain a path, we then take $x = A \epsilon$ where $\epsilon$ is drawn from a unit normal distribution.

## Efficient technique

However, the above approach requires us to compute a factorization of a large (admittedly sparse) matrix.
This can be avoided if we are willing to instead consider sampling _periodic_ smooth paths.
Since all the matrices become circulant (and square), they can be diagonalized using the DFT.

For engineers like me, the diagonalization is hiding in the familiar convolution identity.
Let $Y$ denote the matrix which corresponds to circular convolution with a signal $y$.

$$ Y x = y \star x $$

$$ F Y x = F (y \star x) = (F y) \odot (F x) = \operatorname{diag}(F y) F x $$

$$ Y x = F^{-1} \operatorname{diag}(F y) F x \qquad \forall x $$

$$ Y = F^{-1} \operatorname{diag}(F y) F $$

Here the $F$ matrix computes the DFT in the same way as the function `fft()` in numpy.
Note that this matrix is not unitary but instead satisfies

$$ F^{-1} = \frac{1}{N} F^{\ast} $$

For convenience, let us define $U = \frac{1}{\sqrt{N}} F$ so that $U^{-1} = U^{\ast}$ and

$$ Y = (\sqrt{N} U)^{-1} \operatorname{diag}(F y) (\sqrt{N} U) = U^{\ast} \operatorname{diag}(F y) U $$

Now we can use this to diagonalize the finite difference matrix:

$$ D_{1} = U^{\ast} \operatorname{diag}(F d_{1}) U $$

where $d_{1} = (-1, 1, 0, \dots, 0)$.

The Gram matrix becomes

$$ D_{1}^{T} D_{1} = D_{1}^{\ast} D_{1} = U^{\ast} \operatorname{diag}(F d_{1})^{\ast} U U^{\ast} \operatorname{diag}(F d_{1}) U = U^{\ast} \operatorname{diag}(|F d_{1}|^{2}) U $$
