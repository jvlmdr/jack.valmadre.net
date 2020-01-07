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

### Efficient technique

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

This means that the matrix $U^{\star} = 1/\sqrt{N} F^

Now we can use this to diagonalize the finite difference matrix:

$$ D_{1} = U^{\ast} \operatorname{diag}(F d_{1}) U $$

where $d_{1} = (-1, 1, 0, \dots, 0)$. The Gram matrix becomes

$$ D_{1}^{T} D_{1} = D_{1}^{\ast} D_{1} = U^{\ast} \operatorname{diag}(F d_{1})^{\ast} U U^{\ast} \operatorname{diag}(F d_{1}) U = U^{\ast} \operatorname{diag}(\lvert F d_{1} \rvert^{2}) U $$

Finally, the entire precision matrix $\Lambda$ can therefore be diagonalized:

$$ \Lambda = U^{\ast} \operatorname{diag}(1 + \alpha \lvert F d_{1} \rvert^{2} + \beta \lvert F d_{2} \rvert^{2}) U $$

Incidentally, since $d_2 = d_1 \star d_1$, we see that $F d_{2} = (F d_{1})^2$ and therefore $\lvert F d_{2} \rvert^2 = \lvert F d_{1} \rvert^4$.

Therefore, to sample from the distribution $\mathcal{N}(0, \Lambda^{-1})$, we use the matrix

$$ A = U^{\ast} \operatorname{diag}(\lambda^{-\frac{1}{2}}) $$

where $\lambda = 1 + \alpha \lvert F d_{1} \rvert^{2} + \beta \lvert F d_{2} \rvert^{2}$.

But wait, something seems wrong here.
The precision matrix $\Lambda$ is symmetric positive-definite and yet the eigenvectors in $U$ are complex?
In order to obtain samples $x = A \epsilon$, we need a _real_ factorization $\Sigma = A A^{T}$.

### Real diagonalization

What's happened here is that some eigenvalues occur twice and therefore the eigenvectors are not unique: we can take any (complex) rotation of two eigenvectors with the same eigenvalue and they are still unit-norm eigenvectors of the same matrix.
It should be possible to mix these eigenvectors in a way that makes them real.

Let's seek a complex matrix $Q$ that is unitary $Q^{\ast} = Q^{-1}$ and such that $V = U Q$ is real.
This would ensure that $V^{T} = V^{\ast} = V^{-1} $ since the product of two unitary matrices is a unitary matrix.
Then we would have a real diagonalization of the precision matrix

$$ \Lambda = (U Q)^{\ast} \operatorname{diag}(\lambda) U Q = V^{T} \operatorname{diag}(\lambda) V $$

Recall that the Fourier transform of a real-valued signal has _conjugate_ symmetry.
We can immediately observe that the signal $\lambda$ possesses _real_ symmetry $\lambda[k] = \lambda[N - k]$ since it is obtained from the magnitude of a signal with conjugate symmetry.

Recall the definition of the DFT matrix $F$ (which is related to the unitary matrix $U$ by a scalar).
Element $s, t$ of the DFT matrix is $F_{s, t} = \omega_{N}^{s t}$ where $\omega_{N} = \exp(i 2 \pi / N)$.
The product $F Q$ will be a new matrix whose columns are linear combinations of the columns of $F$.
Using the fact that $\omega_{N}^{n} = \omega_{N}^{n \bmod N}$ we can observe conjugate symmetry in the columns of $F$:

$$ \left\{ \begin{aligned}
F_{s, t} & = \omega_{N}^{s t} \\
F_{s, N - t} & = \omega_{N}^{s (N - t)} = \omega_{N}^{-s t}
\end{aligned} \right. $$

From here, it seems natural to try and obtain a real version of the DFT matrix by combining the column pairs.

$$ \left\{ \begin{aligned}
\omega_{N}^{s t} + \omega_{N}^{-s t} & = 2 \operatorname{Re}(\omega_{N}^{s t}) \\
\omega_{N}^{s t} - \omega_{N}^{-s t} & = 2 i \operatorname{Im}(\omega_{N}^{s t})
\end{aligned} \right. $$

Note that there are two special cases.
When $t = 0$, the entire column $F_{s, 0} = \omega_{N}^0 = 1$, which is already real.
If $N$ is even, then the column $F_{s, N / 2} = \omega_{N}^{(N/2) s} = (-1)^{s}$, which is also already real.

To make $F Q$ real, we therefore try using a matrix $Q$ of the form

$$ Q = \begin{bmatrix}
1 \\
& \alpha &&&&&& -i \beta \\
&& \alpha &&&& -i \beta \\
&&& \ddots && \ddots \\
&&&& (1) \\
&&& \ddots && \ddots \\
&& \alpha &&&& i \beta \\
& \alpha &&&&&& i \beta
\end{bmatrix} $$

with $\alpha, \beta \in \mathbb{R}$.
This will separate the real and imaginary components of the columns of the DFT matrix.
Now the question is: do there exist $\alpha$ and $\beta$ that satisfy $Q^{\ast} Q = Q Q^{\ast} = I$?
Indeed, this can be achieved with simply $\alpha = \beta = \frac{1}{\sqrt{2}}$.

$$ Q = \begin{bmatrix}
1 \\
& \frac{1}{\sqrt{2}} &&&&&& -i \frac{1}{\sqrt{2}} \\
&& \frac{1}{\sqrt{2}} &&&& -i \frac{1}{\sqrt{2}} \\
&&& \ddots && \ddots \\
&&&& (1) \\
&&& \ddots && \ddots \\
&& \frac{1}{\sqrt{2}} &&&& i \frac{1}{\sqrt{2}} \\
& \frac{1}{\sqrt{2}} &&&&&& i \frac{1}{\sqrt{2}}
\end{bmatrix} $$

Finally, we have our real $A = Q^{\ast} U^{\ast} \operatorname{diag}(\lambda^{-\frac{1}{2}})$.
