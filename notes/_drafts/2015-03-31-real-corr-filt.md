---
layout: post
title:  Real solution to multi-channel correlation filters
math:   true
---

First let's consider solving $$C z = w$$ where $$z = x + i y$$, $$C = A + i B$$ and $$w = u + i v$$.
This $$\ell \times \ell$$ complex system can be solved as a $$2 \ell \times 2 \ell$$ real system.

$$
u + i v = (A + i B) (x + i y) = (A x - B y) + i(B x + A y)
$$

$$
\begin{bmatrix} u \\ v \end{bmatrix}
  = \begin{bmatrix} A & -B \\ B & A \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix}
$$

Now let's consider the problem which arises in a $$k$$-channel correlation filter.

$$
r = S w
$$

$$
r_{p} = \sum_{q = 1}^{k} S_{pq} w_{q}
$$

$$
r_{p} = \sum_{q = 1}^{k} s_{pq} \ast w_{q}
$$

$$
\begin{align}
\hat{r}_{p} & = \sum_{q = 1}^{k} \hat{s}_{pq} \odot \hat{w}_{q}, && p = 1, \dots, k \\
\hat{r}_{p}[u] & = \sum_{q = 1}^{k} \hat{s}_{pq}[u] \, \hat{w}_{q}[u], && p = 1, \dots, k; \; u \in \mathcal{U} \\
\hat{r}[u] & = \hat{S}[u] \, \hat{w}[u], && u \in \mathcal{U}
\end{align}
$$

Since $$s_{qp}[u] = s_{pq}[-u]$$ due to the symmetry of the covariance matrix, in the frequency domain $$\hat{s}_{qp}[u] = \hat{s}_{pq}^{*}[u]$$ (complex conjugate).

and therefore $$\hat{S}[u] = \hat{S}[u]^{H}$$ and $$\hat{S}[u] = \hat{S}[\ell-u]^{*} = \hat{S}[\ell-u]^{T}$$

this ensures that $$\hat{w}_{p}[u] = \hat{w}_{p}^{*}[-u]$$ so that each $$w_{p} = \mathcal{F}^{-1} \hat{w}_{p}$$ is real.

$$
\begin{bmatrix} \hat{S}[u] & 0 \\ 0 & \hat{S}[\ell-u] \end{bmatrix}
  \begin{bmatrix} \hat{w}[u] \\ \hat{w}[\ell-u] \end{bmatrix}
= \begin{bmatrix} \hat{r}[u] \\ \hat{r}[\ell-u] \end{bmatrix}
$$

Introduce

$$ P = \begin{bmatrix} 1 & 1 \\ -i & i \end{bmatrix} $$

and $$ Q = \frac{1}{\sqrt{2}} P $$ such that $$Q^{H} = Q^{-1}$$

$$
(Q \otimes I) \begin{bmatrix} \hat{S}[u] & 0 \\ 0 & \hat{S}[\ell-u] \end{bmatrix}
  (Q^{H} \otimes I) (Q \otimes I) \begin{bmatrix} \hat{w}[u] \\ \hat{w}[\ell-u] \end{bmatrix}
= (Q \otimes I) \begin{bmatrix} \hat{r}[u] \\ \hat{r}[\ell-u] \end{bmatrix}
$$

$$
\begin{align}
(P \otimes I) \begin{bmatrix} A & 0 \\ 0 & B \end{bmatrix} (P^{H} \otimes I)
& = (P \otimes I) \begin{bmatrix} A & 0 \\ 0 & B \end{bmatrix} \begin{bmatrix} I & i I \\ I & -i I \end{bmatrix} \\
& = (P \otimes I) \begin{bmatrix} A & i A \\ B & -i B \end{bmatrix} \\
& = \begin{bmatrix} I & I \\ -i I & i I \end{bmatrix} \begin{bmatrix} A & i A \\ B & -i B \end{bmatrix} \\
& = \begin{bmatrix} A + B & i (A - B) \\ i (B - A) & A + B \end{bmatrix}
\end{align}
$$

If $$B = A^{*} = A^{T}$$ then

$$
(P \otimes I) \begin{bmatrix} A & 0 \\ 0 & B \end{bmatrix} (P^{H} \otimes I)
= 2 \begin{bmatrix}
  \operatorname{Re} A & \operatorname{Im} A \\
  \operatorname{Im} A^{T} & \operatorname{Re} A \end{bmatrix}
$$

and therefore we can solve

$$
\begin{bmatrix} \operatorname{Re} \hat{r}[u] \\ \operatorname{Im} \hat{r}[u] \end{bmatrix}
 = \begin{bmatrix}
    \operatorname{Re} \hat{S}[u] & \operatorname{Im} \hat{S}[u] \\
    \operatorname{Im} \hat{S}[u]^{T} & \operatorname{Re} \hat{S}[u] \end{bmatrix}
  \begin{bmatrix} \operatorname{Re} \hat{w}[u] \\ \operatorname{Im} \hat{w}[u] \end{bmatrix}
$$

Therefore we need to solve 1-2 real systems of size $$k \times k$$ and $$\lfloor (\ell-1)/2 \rfloor$$ systems of size $$2k \times 2k$$.

This is JUST the complex problem expressed as a real problem, and then only solving for the unique ones!!
