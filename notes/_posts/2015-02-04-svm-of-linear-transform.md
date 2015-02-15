---
layout: post
title:  SVM with linear features
math:   true
---

This post derives a result from the 2010 PAMI paper of Ashraf, Lucey and Chen, <a href="http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=5432220">Reinterpreting the Application of Gabor Filters as a Manipulation of the Margin in Linear Support Vector Machines</a>.

The support vector machine optimisation problem is

$$
\min_{w} \left\{ \frac{1}{2} \|w\|^{2} + C \sum_{i} \max(0, 1 - y_{i} \phi(x_{i})^{T} w) \right\} .
$$

Here we consider the case where the feature transform is linear $$\phi(x) = A x $$, such that $$w^{T} \phi(x) = \langle w, A x \rangle = \langle A^{T} w, x \rangle$$.
This can also be expressed as the constrained optimisation problem

$$
\begin{aligned}
\min_{w, z} \; & \left\{ \frac{1}{2} \|w\|^{2} + C \sum_{i} \max(0, 1 - y_{i} x_{i}^{T} z) \right\} \\
\text{s.t.} \; & z = A^{T} w \enspace .
\end{aligned}
$$

We now look to remove $$w$$ from this problem.
There are a few different cases depending on the shape and rank of the matrix.

### Higher dimension, full rank

Let's assume that $$A$$ is $$m \times n$$ where $$m \ge n$$ and the rank of $$A$$ is $$n$$ (the maximum possible).
This is where $$A$$ is increasing the dimension of the examples without losing any information.
The constraint $$z = A^{T} w$$ implies that

$$
z \in \operatorname{col}(A^{T}) = \mathbb{R}^{n}
$$

which importantly means that $$z$$ alone does not need to be constrained.
However, for a given $$z$$ there will be many $$w$$ that satisfy the constraint.

To resolve the ambiguity of $$w$$, we examine the regularisation term.
First, let's decompose $$A = U S V^{T}$$ into its "thin" SVD, where $$U$$ is $$m \times n$$, $$V$$ is $$n \times n$$ and $$S$$ is $$n \times n$$ and diagonal.
We have $$U^{T} U = I_{n}$$ and $$V^{T} V = V V^{T} = I_{n}$$.
Then

$$
\begin{align}
z & = A^{T} w = V S U^{T} w \\
S^{-1} V^{T} z & = U^{T} w
\end{align}
$$

All solutions for $$w$$ can be parametrised 

$$
w = U S^{-1} V^{T} z + U_{\bot} a
$$

for arbitrary $$a$$ where $$U_{\bot}$$ is $$m \times (m - n)$$ and the basis for $$\operatorname{null}(A^{T})$$.
It satisfies $$U^{T} U_{\bot} = 0$$ and $$U_{\bot}^{T} U_{\bot} = I_{m-n}$$.
Note that $$U S^{-1} V^{T} = (A^{T})^{\dagger}$$ is the pseudo-inverse.

Introducing this result into the regularisation term, we see

$$
w^{T} w = z^{T} V S^{-2} V^{T} z + a^{T} a = z^{T} (A^{T} A)^{-1} z + a^{T} a
$$

and then the objective becomes separable

$$
\min_{z} \left\{ \frac{1}{2} \|z\|^{2}_{(A^{T} A)^{-1}} + C \sum_{i} \max(0, 1 - y_{i} x_{i}^{T} z) \right\}
+ \min_{a} \frac{1}{2} \|a\|^{2} \enspace .
$$

Thus the solution is the same as the original SVM, except the margin is measured in a weighted space.

### Lower dimension, full rank

When $$m < n$$ things are not so simple.
The constraint $$z = A^{T} w$$ will restrict

$$
z \in \operatorname{col}(A^{T}) \subset \mathbb{R}^{n} \enspace .
$$

While it is possible to make a similar substitution for $$w$$, the final optimisation problem is

$$
\begin{aligned}
\min_{z} \; & \frac{1}{2} \|z\|^{2}_{(A^{T} A)^{-1}} + C \sum_{i} \max(0, 1 - y_{i} x_{i}^{T} z) \\
\text{s.t.} \; & z \in \operatorname{col}(A^{T})
\end{aligned}
$$

which does not resemble a canonical SVM.
