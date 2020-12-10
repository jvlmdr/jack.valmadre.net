---
layout: post
title:  Linear assignment with non-perfect matching
math:   true
---

(work in progress)

The [linear assignment problem][1] (or simply assignment problem) is the problem of finding a matching between two sets that minimizes the sum of pair-wise assignment costs.
This can be expressed as finding a [matching][2] (or independent edge set) in a [bipartite graph][5] $$G = (U, V, E)$$ that minimizes the sum of edge weights.
The edge weights may be positive or negative and the bipartite graph does not need to be [complete][4] (i.e. there may be missing edges).
Note that a maximum-weight assignment can be obtained by negating the weights and finding a minimum-weight assignment.

The simplest form of the assignment problem assumes that the bipartite graph is _balanced_ (the two sets of vertices are the same size) and that there exists a _perfect_ matching (where every vertex has a match).
Let $$n$$ be the size of the vertex sets and let $$C$$ be a square matrix of size $$n \times n$$ that contains the edge weights.
Missing edges are represented by $$\infty$$, such that $$C_{i j} < \infty \Leftrightarrow (i, j) \in E$$.
The assignment problem can then be clearly expressed as an [integer linear program][6]

$$
\begin{aligned}
\text{minimize } & \textstyle \sum_{i, j} A_{i j} W_{i j} \\
\text{subject to }
& \textstyle A \in \{0, 1\}^{n \times n} \\
& \textstyle \forall i : \sum_{j} A_{i j} = 1 \\
& \textstyle \forall j : \sum_{i} A_{i j} = 1
\end{aligned}
$$

The constraints that the sum of each row and column is equal to one ensures that each element has exactly one match.
However, what happens when the graph is not balanced (i.e. the matrix is not square) or when it does not contain a perfect matching?
What problem should we solve?
Moreover, several packages exist for solving the assignment problem.
How do they behave when given one of these problems and how can we achieve our desired behaviour?

Let us consider a more general, rectangular problem of size $$r \times n$$ with $$r \le n$$.
Let $$\nu(G) \le r$$ denote the size of the largest matching in the graph.

The tech report ["On Minimum-Cost Assignments in Unbalanced Bipartite Graphs"][7] by Lyle Ramshaw and Robert E. Tarjan lays out three different types of assignment problem.

1. perfect matching
2. imperfect matching
3. minimum-weight matching

The imperfect matching problem is to find the matching of size $$\nu(G)$$ with the minimum cost.
The minimum-weight matching problem is to find the matching of _any_ size with the minimum cost.
If $$\nu(G) = r = n$$, then perfect and imperfect matching coincide.
Otherwise (when $$\nu(G) < n$$), perfect matching has no solution.

When using linear assignment, we should carefully consider which of these problems we are actually interested in.
The minimum-weight matching problem is often the most natural choice (although none of the packages provide it directly).
To illustrate the difference between this and the other problems, consider the balanced problem with weights

$$
\begin{bmatrix}
~ & -1 \\
-2 & -4 \\
\end{bmatrix}
$$

The solution to perfect/imperfect matching is to choose -1 and -2 for a total score of -3.
The solution to minimum-weight matching is to choose -4 alone.


### Problem transformations

The tech report above describes some clever techniques to transform imperfect and minimum-weight matching problems into perfect matching problems while preserving sparsity.
Here we describe some quick and dirty methods.

We can always transform an unbalanced (and therefore imperfect) problem into a balanced problem by adding $$n - r$$ rows of zeros.
The resulting balanced graph has a perfect matching if and only if the original unbalanced graph had a matching of size $$r$$ (in which every vertex in the smaller set is matched).

How can we transform a balanced but imperfect matching problem into a perfect matching problem?
It suffices to replace the infinite edge weights with a large, finite cost (e.g. larger than the total absolute value of all weights).
The resulting graph must contain a perfect matching since it is a complete bipartite graph.
Each high-cost edge is worth more than all original edges combined.
The high-cost edges can be excluded at the end.

How can we solve a minimum-weight matching problem, given that most existing packages either solve perfect or imperfect matching?
It suffices to replace all positive-weight edges with zero-weight edges (including infinite edges) and then solve imperfect matching.
Positive-weight edges will never be selected in a minimum-weight matching.


### Imperfect matching with positive weights

Be careful when solving imperfect matching problems with positive edge weights!
I would avoid this situation altogether due to the tension that exists between maximizing the number of matches and minimizing the sum of (positive) costs.
This may result in the unexpected behaviour that _adding_ an edge to the graph _increases_ the minimum cost.
For example, compare the following two problems:

$$
W = \begin{bmatrix}
  ~ & 1 & 2 \\
  3 & ~ & ~ \\
  4 & ~ & ~ \\
\end{bmatrix}
\quad \Rightarrow \quad
\langle A^{\star}, W \rangle = 1 + 3 = 4
$$

$$
W = \begin{bmatrix}
~ & 1 & 2 \\
3 & 5 & ~ \\
4 & ~ & ~ \\
\end{bmatrix}
\quad \Rightarrow \quad 
\langle A^{\star}, W \rangle = 2 + 5 + 4 = 11
$$


### Python packages

The table below outlines the different behaviour of several popular packages.

Module | Function | Behaviour
-------|----------|----------
[`lapjv`](https://github.com/src-d/lapjv) | `lapjv()` | Requires that problem is balanced (or else raises an exception). Requires that a perfect matching exists (or else returns infinite cost).
[`lap`](https://github.com/gatagat/lap) | `lapjv()` | Supports unbalanced problems (with `extend_cost=True`; although check issues [#21](https://github.com/gatagat/lap/issues/21), [#22](https://github.com/gatagat/lap/issues/22)). Requires that a maximal matching exists (or else returns infinite cost).
[`scipy`](https://docs.scipy.org/doc/scipy/reference/optimize.html) | `linear_sum_assignment()` | Supports unbalanced problems. Requires that a maximal matching exists (or else raises an exception).
[`lapsolver`](https://github.com/cheind/py-lapsolver) | `solve_dense()` | Supports unbalanced problems. Supports imperfect matching.
[`ortools`](https://developers.google.com/optimization/assignment/linear_assignment) | `LinearSumAssignment` | Requires problem is balanced. Requires that a perfect matching exists (or else raises an exception). Requires that costs are integer-valued.
{: .table}

[1]: https://en.wikipedia.org/wiki/Assignment_problem
[2]: https://en.wikipedia.org/wiki/Matching_(graph_theory)
[3]: https://en.wikipedia.org/wiki/Hungarian_algorithm
[4]: https://en.wikipedia.org/wiki/Complete_bipartite_graph
[5]: https://en.wikipedia.org/wiki/Bipartite_graph
[6]: https://en.wikipedia.org/wiki/Integer_programming
[7]: https://www.hpl.hp.com/techreports/2012/HPL-2012-40R1.pdf
[8]: https://en.wikipedia.org/wiki/Maximum_cardinality_matching
