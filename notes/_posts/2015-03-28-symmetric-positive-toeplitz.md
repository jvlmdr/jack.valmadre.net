---
layout: post
title:  Generating symmetric positive semi-definite Toeplitz matrices
math:   true
---

A $$p \times p$$ real Toeplitz matrix has elements $$A_{u v} = a_{u - v}$$ and is fully defined by the elements of $$a_{n}$$ for $$-p+1 \le n \le p-1$$.

$$
A = \begin{bmatrix}
  a_{0} & a_{-1} & a_{-2} & \cdots & a_{-p+1} \\
  a_{1} & a_{0} & a_{-1} & \cdots & a_{-p+2} \\
  a_{2} & a_{1} & a_{0} & \cdots & a_{-p+3} \\
  \vdots & \vdots & \vdots & \ddots & \vdots \\
  a_{p-1} & a_{p-2} & a_{p-3} & \cdots & a_{0}
\end{bmatrix}
$$

If this is a symmetric matrix then the signal has even symmetry $$a_{n} = a_{-n}$$.
This post is going to look at two ways of generating symmetric Toeplitz matrices which are positive semi-definite.

### Auto-correlation of an infinite signal

One way to generate symmetric Toeplitz matrices which are positive semi-definite is to compute the auto-covariance of an infinite signal.
An easy way to obtain an infinite signal is to use the periodic extension of a finite signal.
The period $$m$$ should be at least $$2p - 1$$ to avoid periodic effects.
If $$m = p$$ then the matrix will be circulant Toeplitz.
The Fourier transform can be used to compute the auto-correlation.

```
p = 100;
m = 2*p-1;
x = randn(m, 1);
a = ifft(conj(fft(x)) .* fft(x)) / m;
a = a(1:p);
A = toeplitz(a, a);
```

### Sampled continuous function

It is possible to consider an infinite Toeplitz matrix with elements $$a_{n}$$ for $$n \in \mathcal{Z}$$.
This matrix maps infinite sequences to infinite sequences such that $$y = A x$$ implies

$$ y_{u} = \sum_{v \in \mathcal{Z}} a_{u-v} z_{v} . $$

The infinite sequence $$a_{n}$$ defines a $$2 \pi$$-periodic function in its discrete-time Fourier transform (DTFT)

$$ \hat{a}(\theta) = \sum_{n \in \mathcal{Z}} a_{n} e^{-i n \theta} . $$

The values that this function takes in a period are the eigenvalues of the infinite Toeplitz matrix.
Therefore, an infinite Toeplitz matrix will be positive semi-definite if and only if the DTFT of its elements is a non-negative function.

It can be shown that if a continuous function has a positive Fourier transform, then sampling the function will result in a sequence with a positive DTFT.
The DTFT of a sampled continuous function $$a_{n} = x(n)$$ can be written as the continuous transform of the function multiplied by a "Dirac comb" or "impulse train"

$$
\hat{a}(\theta)
= \sum_{n \in \mathcal{Z}} x(n) e^{-i n \theta}
= \int_{t \in \mathcal{R}} x(t) \left[\sum_{n \in \mathcal{Z}} \delta(t-n)\right] e^{-i t \theta} dt .
$$

Using the transform of a Dirac comb and the fact that multiplication in time is convolution in frequency,

$$
\hat{a}(\theta) = \mathcal{F} \left\{ x(t) \sum_{n \in \mathcal{Z}} \delta(t-n) \right\}(\theta)
= \sum_{n \in \mathcal{Z}} \hat{x}(\theta - 2 \pi n) .
$$

Therefore if the continuous transform of $$x(t)$$ is non-negative $$\hat{x}(\theta) \ge 0$$, then the sequence obtained by sampling that function $$a_{n} = x(n)$$ will have a non-negative DTFT $$\hat{a}(\theta) \ge 0$$.
This gives us many ways to construct infinite symmetric Toeplitz matrices which are positive semi-definite.
For example, the elements can be sampled from a Gaussian (transform is a Gaussian), triangle (transform is a squared sinc), sinc (transform is a rectangle), squared sinc (transform is a triangle), cosine, sech, Laplacian density function, etc.

Finally, we need a way to go from infinite matrices to finite matrices.
This is easily achieved using the fact that any subset of the rows and columns of a positive semi-definite matrix is itself semi-definite.
If $$x^{T} A x$$ for all $$x$$, then $$y^{T} P^{T} A P y = (P y)^{T} A (P y) \ge 0$$ for all $$y$$.
Therefore we can simply take a finite section of the infinite matrix and be guaranteed that it is positive semi-definite.

```
p = 100;
t = 0:p-1;
a = exp(-abs(t)/10);
A = toeplitz(a, a);
```

This gives us two alternative methods by which to obtain symmetric positive semi-definite Toeplitz matrices.
