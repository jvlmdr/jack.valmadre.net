---
layout: post
title:  Sampling smooth paths
date:   2020-01-06
math:   true
---

Sometimes it's useful to generate random smooth paths that hang around the origin.
The simplest way to obtain a random path is to generate a random walk where each step is independent and normally distributed.
To make the path smooth, we have a couple of options: (1) we could smooth the path with a low-pass filter, or (2) we could integrate the random walk again such that the second derivative is normally distributed rather than the first derivative.
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
If we assume the paths to be periodic, then the finite difference matrices will be square and circulant.

$$ D_1 = \begin{bmatrix} -1 & 1 \\ & -1 & 1 \\ && \ddots & \ddots \\ &&& -1 & 1 \\ 1 &&&& -1 \end{bmatrix} $$

Circulant matrices can be diagonalized using the Discrete Fourier Transform.
For engineers like me, this diagonalization is hiding in the familiar convolution identity.
Let $Y$ denote the matrix which corresponds to circular convolution with a signal $y$.

$$ Y x = y \star x $$

$$ F Y x = F (y \star x) = (F y) \odot (F x) = \operatorname{diag}(F y) F x $$

$$ Y x = F^{-1} \operatorname{diag}(F y) F x \qquad \forall x $$

$$ Y = F^{-1} \operatorname{diag}(F y) F $$

Here the $F$ matrix computes the DFT in the same way as the function `fft()` in numpy.
Note that this matrix is not unitary but instead satisfies

$$ F^{-1} = \frac{1}{N} F^{\ast} $$

For convenience, let us define $U = \frac{1}{\sqrt{N}} F$ so that $U^{-1} = U^{\ast}$ and the diagonalization can be expressed:

$$ Y = (\sqrt{N} U)^{-1} \operatorname{diag}(F y) (\sqrt{N} U) = U^{\ast} \operatorname{diag}(F y) U $$

Now we can use this to diagonalize the finite difference matrix:

$$ D_{1} = U^{\ast} \operatorname{diag}(F d_{1}) U $$

where $d_{1} = (-1, 1, 0, \dots, 0)$. The Gram matrix becomes

$$ D_{1}^{T} D_{1} = D_{1}^{\ast} D_{1} = U^{\ast} \operatorname{diag}(F d_{1})^{\ast} U U^{\ast} \operatorname{diag}(F d_{1}) U = U^{\ast} \operatorname{diag}(\lvert F d_{1} \rvert^{2}) U $$

Finally, the entire precision matrix $\Lambda$ can therefore be diagonalized:

$$ \Lambda = U^{\ast} \operatorname{diag}(1 + \alpha \lvert F d_{1} \rvert^{2} + \beta \lvert F d_{2} \rvert^{2}) U $$

Incidentally, since $d_2 = d_1 \star d_1$, we see that $F d_{2} = (F d_{1})^2$ and therefore $\lvert F d_{2} \rvert^2 = \lvert F d_{1} \rvert^4$.

Therefore, to sample from the distribution $\mathcal{N}(0, \Lambda^{-1})$, we use the matrix

$$ A = U^{\ast} \operatorname{diag}(1 + \alpha \lvert F d_{1} \rvert^{2} + \beta \lvert F d_{2} \rvert^{2})^{-\frac{1}{2}} $$

But wait, something seems wrong here.
The precision matrix $\Lambda$ is symmetric positive definite and yet the eigenvectors in $U$ are complex?
What has happened here is that some eigenvalues occur twice and therefore the eigenvectors are not unique: we can take any (complex) rotation of the eigenvectors with the same eigenvalue and they are still eigenvectors.
