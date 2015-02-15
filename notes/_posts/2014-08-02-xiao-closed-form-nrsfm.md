---
layout: post
title:  Jing Xiao's closed-form NRSfM
date:   2014-08-02 15:42:25
math:   true
---

This is my attempt to give a clear and succinct exposition of Jing Xiao's method.

### Basics

First the problem definition.
A deformable 3D object consisting of $$P$$ points is observed in $$F$$ configurations.
The unknown shape in each frame $$t$$ is described by the position $$s_{ti}$$ of each point $$i$$.
We observe each configuration through an unknown orthonormal 2&times;3 linear projection $$R_{t}$$ which represents a camera.
The 2D projections $$w_{ti}$$ of all points $$i$$ in all frames $$t$$ are known and must satisfy $$w_{ti} = R_{t} s_{ti}$$.
The goal is to recover the sequence of 3D shapes, and optionally the camera matrices as well.

(Note that a camera model would typically incorporate a translation $$d_{t}$$ according to $$w_{ti} = R_{t} s_{ti} + d_{t}$$.
This can be eliminated by subtracting an anchor point such as the 2D centroid of the projections in each frame, although this might not be straightforward when some projections are unobserved.
We won't consider this missing data issue in this article.)

This is an under-constrained problem.
If the cameras $$R_{t}$$ were known, then the problem of estimating all points in all frames would decompose into that of estimating each point in each frame independently i.e. to estimate a 3D point from one observation of a 2D point.
Therefore it is necessary to impose some additional constraints.

Shape-basis NRSfM imposes the constraint that the shape in each frame can be represented as a linear combination of a small number $$K < F$$ of basis shapes.
Each 3D basis shape $$k$$ is described by $$P$$ points $$b_{ki}$$, and an instance in this shape space is described by a $$k$$D vector 

$$
s_{ti} = \sum_{k = 1}^{K} b_{ki} c_{tk}.
$$

Through some manipulation, this provides a bound on the rank of a matrix $$W$$ constructed from the 2D projections $$\operatorname{rank}(W) \le 3K$$.

$$
w_{ti} = R_{t} s_{ti} = \sum_{k = 1}^{K} c_{tk} R_{t} b_{ki}
$$

$$
W = \underset{2F \times P}{\underbrace{\begin{bmatrix}
    w_{11} & \cdots & w_{1P} \\
    \vdots & \ddots & \vdots \\
    w_{F1} & \cdots & w_{FP}\
  \end{bmatrix}}}
= \underset{2F \times 3K}{\underbrace{\begin{bmatrix}
    c_{11} R_{1} & \cdots & c_{1K} R_{1} \\
    \vdots & \ddots & \vdots \\
    c_{F1} R_{F} & \cdots & c_{FK} R_{F}
  \end{bmatrix}}}
  \underset{3K \times P}{\underbrace{\begin{bmatrix}
    b_{11} & \cdots & b_{1P} \\
    \vdots & \ddots & \vdots \\
    b_{K1} & \cdots & b_{KP}
  \end{bmatrix}}}
= M B
$$

The key idea in factorisation-based approaches to low-rank shape-basis NRSfM is that any rank-$$3K$$ factorisation $$W = \hat{M} \hat{B}$$ can be linearly "corrected" through the application of a full-rank $$3K \times 3K$$ linear transform $$G$$.
The correction should ensures that $$M = \hat{M} G$$ has the very specific structure above, where each row contains scaled repetitions of an orthonormal matrix.

Now the problem of finding M and B can be reduced to the problem of finding G which turns M-hat into M.
Note that in order for M and B to be full rank (and for G to be invertible), G must be full rank.
Dividing M-hat into row pairs and G into column triples, we can get an expression for each block of M.

$$
\mathbf{M} = \begin{bmatrix}
    \mathbf{M}_{11} & \cdots & \mathbf{M}_{1K} \\
    \vdots & \ddots & \vdots \\
    \mathbf{M}_{F1} & \cdots & \mathbf{M}_{FK}
  \end{bmatrix}
= \mathbf{\hat{M}} \mathbf{G}
= \begin{bmatrix}
    \mathbf{\hat{M}}_{1} \\ \vdots \\ \mathbf{\hat{M}}_{F}
  \end{bmatrix}
  \begin{bmatrix}
    \mathbf{G}_{1} & \cdots & \mathbf{G}_{K}
  \end{bmatrix}
= \begin{bmatrix}
    \mathbf{\hat{M}}_{1} \mathbf{G}_{1} & \cdots & \mathbf{\hat{M}}_{1} \mathbf{G}_{K} \\
    \vdots & \ddots & \vdots \\
    \mathbf{\hat{M}}_{F} \mathbf{G}_{1} & \cdots & \mathbf{\hat{M}}_{F} \mathbf{G}_{K}
  \end{bmatrix}
$$

We know that M should have this special form where each 2&times;3 block is a scaled truncated rotation matrix, and horizontally adjacent blocks should be scaled versions of the same matrix. Although we don't know the coefficients c(t, k), we can still get the following constraint.

$$
\mathbf{M}_{tk} \mathbf{M}_{tk}^{T}
= (c_{tk} \mathbf{R}_{tk}) (c_{tk} \mathbf{R}_{tk}^{T})
= c_{tk}^{2} \mathbf{I}_{2}
= \mathbf{\hat{M}}_{t} \mathbf{G}_{k} \mathbf{G}_{k}^{T} \mathbf{\hat{M}}_{t}^{T}
$$

Taking the difference between the diagonal entries of this matrix yields the following constraints.

$$
\left[\mathbf{\hat{M}}_{t} \mathbf{Q} \mathbf{\hat{M}}_{t}^{T}\right]_{11} - \left[\mathbf{\hat{M}}_{t} \mathbf{Q} \mathbf{\hat{M}}_{t}^{T}\right]_{22} = 0, \qquad
\left[\mathbf{\hat{M}}_{t} \mathbf{Q} \mathbf{\hat{M}}_{t}^{T}\right]_{12} = 0, \qquad
\mathbf{Q} = \mathbf{G}_{k} \mathbf{G}_{k}^{T}
$$

Since the above equations involve only the <a href="http://en.wikipedia.org/wiki/Gramian_matrix">Gram matrix</a> of G_k, we substitute Q = G_k G_k'. We only consider the upper triangular entries (1, 1), (1, 2) and (2, 2) of this matrix equation because it is symmetric.

Note that the equations for G_k don't otherwise depend on k. Therefore we need to find multiple G_k which satisfy these equations. The individual G_k have to be linearly independent of one another because G must be full rank.

While it's difficult to solve the multivariate quadratic equations for G_k, we note that the equations are linear in Q. However, Jing Xiao showed (and Ijaz Akhter clarified) that these orthonormality constraints are too ambiguous without the constraint that Q be rank 3, preventing a linear solution.

### Jing Xiao's method

Since any (full rank) linear transformation of the basis is still a valid basis (we can always apply the inverse transform to the coefficients), we are free to choose the basis (or coefficients) arbitrarily, so long as they are full rank. Jing Xiao's idea was to constrain the K shape bases to be the (unknown) 3D structure in a subset of K frames (without loss of generality we'll say the first K frames, since the frames can be arbitrarily reordered). This requires that we are able to choose K such frames, which can be done by examining the condition of the submatrix of W.

Knowing that the structure in each of these frames is a basis vector, we know a subset of the coefficients.

$$
c_{tk} = \delta[t-k] = \begin{cases} 1, & t = k, \\ 0, & t \ne k \end{cases} \qquad t = 1, \dots, K, \quad k = 1, \dots, K
$$

Now we have a different set of equations.

$$
\begin{aligned}
\mathbf{\hat{M}}_{t} \mathbf{Q} \mathbf{\hat{M}}_{t}^{T}
  &= c_{tk}^{2} \mathbf{R}_{t} \mathbf{R}_{t}^{T}
  = \begin{cases}
      \mathbf{I}, & t = k, \\
      \mathbf{0}, & t \ne k, \\
    \end{cases}
  & \quad & t \le K \\ \\
\mathbf{\hat{M}}_{t} \mathbf{Q} \mathbf{\hat{M}}_{t}^{T}
  &= c_{tk}^{2} \mathbf{R}_{t} \mathbf{R}_{t}^{T} = c_{tk}^{2} \mathbf{I},
  && t > K \\ \\
\mathbf{\hat{M}}_{t} \mathbf{Q} \mathbf{\hat{M}}_{u}^{T}
  &= c_{tk} c_{uk} \mathbf{R}_{t} \mathbf{R}_{u}^{T} = \mathbf{0},
  && t \ne u, \; t \text{ or } u \in \{v : v \le K, v \ne k \}
\end{aligned}
$$

The first equation gives three constraints because it's symmetric, the second equation gives two constraints because it's symmetric and we need to take the difference of the diagonals, and the third equation gives four constraints because it's not symmetric. Altogether there are K of the first constraints, (F &minus; K) of the second constraints and K(K &minus; 1)/2 + (K &minus; 1)(F &minus; K) of the third constraints. This system is also now inhomogeneous and dependent on k. Jing Xiao showed (although I couldn't quite follow the proof yet) that these constraints are enough to linearly solve for Q without the rank 3 constraint. In the code, this system is constructed in <tt>rotation_and_basis_constraints.m</tt>.

After solving for Q in each system we find G_k by taking the eigenvectors corresponding to the three greatest eigenvalues.

### Aligning column triples

However, we're not done yet. We still need to align the column triples G_k, since each solution is only unique up to an orthogonal 3&times;3 transform.

$$
\mathbf{E} \mathbf{E}^{T} = \mathbf{I} \qquad \Rightarrow \qquad
\mathbf{G}_{k} \mathbf{G}_{k}^{T} = \big(\mathbf{G}_{k} \mathbf{E}\big) \big(\mathbf{G}_{k} \mathbf{E}\big)^{T}
$$


This is shrugged off a little bit in Jing Xiao's paper as just using <a href="http://en.wikipedia.org/wiki/Orthogonal_Procrustes_problem">orthogonal Procrustes analysis</a> to align the column triples of cameras, but it's not as easy as it looks, as was also noted by Vincent Rabaud in his <a href="http://code.google.com/p/vincents-structure-from-motion-matlab-toolbox/">Structure from Motion Toolbox</a>.

The difficulty arises in the sign of the coefficients. Consider the problem of aligning two column triples of M. I'll introduce R-hat to denote the estimated camera from each column triple.

$$
\mathbf{\hat{M}} \mathbf{G}_{k} = \begin{bmatrix}
  c_{1k} \hat{\mathbf{R}}_{1k} \\
  \vdots \\
  c_{Fk} \hat{\mathbf{R}}_{Fk}
\end{bmatrix}, \qquad
\mathbf{\hat{M}} \mathbf{G}_{\ell} = \begin{bmatrix}
  c_{1\ell} \hat{\mathbf{R}}_{1\ell} \\
  \vdots \\
  c_{F\ell} \hat{\mathbf{R}}_{F\ell}
\end{bmatrix}
$$

We want to find E such that the cameras match.

$$
\begin{bmatrix}
  \hat{\mathbf{R}}_{1k} \\ \vdots \\ \hat{\mathbf{R}}_{Fk}
\end{bmatrix}
\approx \begin{bmatrix}
  \hat{\mathbf{R}}_{1\ell} \\ \vdots \\ \hat{\mathbf{R}}_{F\ell}
\end{bmatrix} \mathbf{E}
= \begin{bmatrix}
  \hat{\mathbf{R}}_{1\ell} \mathbf{E} \\
  \vdots \\
  \hat{\mathbf{R}}_{F\ell} \mathbf{E}
\end{bmatrix}
$$

But in order to do this, we need to extract cameras from M, and there's an ambiguity between coefficient and rotation sign.

$$
c_{tk} \mathbf{\hat{R}}_{tk} = (-c_{tk}) (-\mathbf{\hat{R}}_{tk})
$$

The proper way to solve this is to enumerate all combinations of signs, about 2^F, solving the Procrustes problem for each combination. The solution I use is to align one camera, use this to find the correct sign of every other camera, then align all cameras. This is only guaranteed to work in the noiseless case, something more global would be better. Perhaps doing this for every camera and voting for the sign of all cameras each time? Anyway, I'll present my solution here.

Picking a frame u to align, such that c(u,k) and c(u,l) are non-zero (I use the frame with the largest magnitude coefficients), assume both coefficients are positive and solve the Procrustes problem to align the 2&times;3 rotation matrices.

$$
\arg \min_\mathbf{E} \| \mathbf{R}_{uk} - \mathbf{R}_{u\ell} \mathbf{E} \|_{F} \;\; \text{s.t.} \;\; \mathbf{E} \mathbf{E}^{T} = \mathbf{I}
$$

The solution is given by an SVD and is only unique up to the relative sign of the last singular vectors, since the matrix is only rank 2 and the singular vectors must be orthonormal.

$$
\mathbf{R}_{u\ell}^{T} \mathbf{R}_{uk} = \mathbf{U} \mathbf{\Sigma} \mathbf{V}^{T}, \qquad
\mathbf{E} = \mathbf{U} \begin{bmatrix} 1 \\ & 1 \\ & & \pm 1 \end{bmatrix} \mathbf{V}^{T}
$$

For each solution for E, I find the best sign for the coefficient in every frame after applying E, then pick the solution with the lowest residual. Then I re-estimate E using all cameras. The code to do this is found in <tt>align_corrective_triples.m</tt>. I align every triple to the first triple. Again, this might not be optimal, but it at least works in the noiseless case.

Note that the final solution is up to a reflection per frame, but that's an inherent ambiguity in non-rigid structure from motion using only a rank constraint.
