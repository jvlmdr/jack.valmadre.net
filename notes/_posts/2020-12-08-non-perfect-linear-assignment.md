---
layout: post
title:  Linear assignment with non-perfect matching
math:   true
---

The [linear assignment problem][1] (or simply assignment problem) is the problem of finding a matching between two sets that minimizes the sum of pair-wise assignment costs.
This can be expressed as finding a [matching][2] (or independent edge set) in a [bipartite graph][5] $$G = (U, V, E)$$ that minimizes the sum of edge weights.
The edge weights may be positive or negative and the bipartite graph does not need to be [complete][4]: if there is no edge between two vertices then they cannot be associated.
Note that a maximum-weight assignment can be obtained by negating the weights and finding a minimum-weight assignment.

The simplest form of the assignment problem assumes that the bipartite graph is _balanced_ (the two sets of vertices are the same size) and that there exists a _perfect_ matching (in which every vertex has a match).
Let $$n$$ be the number of elements in each set and let $$C$$ be a square matrix of size $$n \times n$$ that contains the edge weights.
Missing edges are represented by $$\infty$$, such that $$C_{i j} < \infty \Leftrightarrow (i, j) \in E$$.
The assignment problem can then be clearly expressed as an [integer linear program][6]: (note that the problem is not actually solved using a general-purpose ILP solver, it is just a convenient framework in which to express the problem)

$$
\begin{aligned}
\text{minimize } & \textstyle \sum_{i, j} A_{i j} W_{i j} \\
\text{subject to }
& \textstyle A \in \{0, 1\}^{n \times n} \\
& \textstyle \forall i : \sum_{j} A_{i j} = 1 \\
& \textstyle \forall j : \sum_{i} A_{i j} = 1
\end{aligned}
$$

The constraint that the sum of each row and column is equal to one ensures that each element has exactly one match.
However, what happens when the graph is not balanced or does not contain a perfect matching?
We cannot enforce the sums to be equal to one.
Which problem should be solved?

I recommend reading the tech report ["On Minimum-Cost Assignments in Unbalanced Bipartite Graphs"][7] by Lyle Ramshaw and Robert E. Tarjan.
I will summarize some of the main points here.

Let us consider a more general, rectangular problem of size $$r \times n$$ and assume (without loss of generality) that $$r \le n$$.
If $$r = n$$ then the problem is balanced, if $$r < n$$ it is unbalanced.

Clearly an unbalanced probem cannot have a perfect matching, since there will be at least $$n - r$$ unmatched elements in the larger set.
However, it may be possible to find a matching in which every vertex in the smaller set has a match.
This is referred to as a _one-sided perfect_ matching and the optimization problem can be expressed:

$$
\begin{aligned}
\text{minimize } & \textstyle \sum_{i, j} A_{i j} W_{i j} \\
\text{subject to }
& \textstyle A \in \{0, 1\}^{r \times n} \\
& \textstyle \forall i : \sum_{j} A_{i j} = 1 \\
& \textstyle \forall j : \sum_{i} A_{i j} \le 1
\end{aligned}
$$

The inequality constraints enforce that each element in the smaller set has _exactly_ one match while each element in the larger set has _at most_ one match.
Ramshaw and Tarjan outline a method to reduce from an unbalanced problem to a balanced problem while preserving sparsity.
A simple alternative is to add $$n - r$$ rows of zeros and then exclude these edges from the eventual solution.
Most libraries for the assignment problem solve either the balanced or unbalanced version of this problem (see the later section).

However, whether balanced or unbalanced, it may still occur that the constraint set is infeasible, meaning that there does not exist a (one-sided) perfect matching.
Let $$\nu(W) \le r$$ denote the size of the largest matching in the graph.
If $$\nu(W) < r$$, then there does not exist a one-sided perfect matching and all possible matchings are imperfect.

Ramshaw and Tarjan actually outline three different versions of the assignment problem:

1. perfect matching
2. imperfect matching
3. minimum-weight matching

The imperfect matching problem is to find the matching of size $$\nu(G)$$ with the minimum cost.
The minimum-weight matching problem is to find the matching of _any_ size with the minimum cost.
If $$\nu(G) = r = n$$, then perfect and imperfect matching coincide.
Otherwise, when $$\nu(G) < n$$, there does not exist a perfect matching.

The imperfect matching problem can be expressed

$$
\begin{aligned}
\text{minimize } & \textstyle \sum_{i, j} A_{i j} W_{i j} \\
\text{subject to }
& \textstyle A \in \{0, 1\}^{r \times n} \\
& \textstyle \forall i : \sum_{j} A_{i j} \le 1 \\
& \textstyle \forall j : \sum_{i} A_{i j} \le 1 \\
& \textstyle \sum_{i, j} A_{i j} = \nu(W)
\end{aligned}
$$

and the minimum-weight matching problem can be expressed

$$
\begin{aligned}
\text{minimize } & \textstyle \sum_{i, j} A_{i j} W_{i j} \\
\text{subject to }
& \textstyle A \in \{0, 1\}^{r \times n} \\
& \textstyle \forall i : \sum_{j} A_{i j} \le 1 \\
& \textstyle \forall j : \sum_{i} A_{i j} \le 1
\end{aligned}
$$

Ramshaw and Tarjan show that both of these problems can be reduced to finding a perfect matching in a balanced graph.
When using linear assignment, we should carefully consider which of the three problems we actually want to solve.


### Remarks

#### In support of minimum-weight matching

The minimum-weight matching problem is often the most natural choice, since it puts no constraint on the size of the matching.
To illustrate the difference between this and the other problems, consider the following balanced problem:

$$
\begin{bmatrix}
2 & -1 \\
-2 & -4 \\
\end{bmatrix}
$$

The solution to perfect (or imperfect) matching is to choose -1 and -2 for a total score of -3 and a cardinality of 2.
The solution to minimum-weight matching is to choose -4 with a cardinality of 1.

Minimum-weight matching will never select an edge with positive cost: it is better to simply leave it unselected.
Edges with zero cost have no impact.

It may be more natural to consider the maximization of positive weights than the minimization of negative costs.

#### Min-cost imperfect matching with positive weights

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

#### Quick and dirty transformations

Ramshaw and Tarjan above describes some clever techniques to transform imperfect and minimum-weight matching problems into perfect matching problems while preserving sparsity.
Here we describe some quick and dirty alternatives.

We can always transform an unbalanced (and therefore imperfect) problem into a balanced problem by adding $$n - r$$ rows of zeros.
The resulting balanced graph has a perfect matching if and only if the original unbalanced graph had a matching of size $$r$$ (in which every vertex in the smaller set is matched).

If we need to solve imperfect matching but we only have a solver for perfect matching, it suffices to replace the infinite edge weights with a large, finite cost (e.g. larger than the total absolute value of all weights).
The resulting graph must contain a perfect matching since it is a complete bipartite graph, and each high-cost edge is worth more than all original edges combined.
The high-cost edges can be excluded at the end.

Most existing packages either solve perfect, one-sided perfect or imperfect matching.
To use one of these solvers for the minimum-weight matching problem, it suffices to replace all positive edges (including infinite edges) with zero.
If using a solver that leverages sparsity, it is better to use the technique described by Ramshaw and Tarjan.


### Python packages

The table below outlines the different behaviour of several popular packages.

Module | Function | Behaviour
-------|----------|----------
[lapjv](https://github.com/src-d/lapjv) | `lapjv()` | Requires that problem is balanced (or else raises an exception). Requires that a perfect matching exists (or else returns infinite cost).
[lap](https://github.com/gatagat/lap) | `lapjv()` | Supports unbalanced problems (with `extend_cost=True`; although check issues [#21](https://github.com/gatagat/lap/issues/21), [#22](https://github.com/gatagat/lap/issues/22)). Requires that a one-sided perfect matching exists (or else returns infinite cost).
[scipy](https://docs.scipy.org/doc/scipy/reference/optimize.html) | `linear_sum_assignment()` | Supports unbalanced problems. Requires that a one-sided perfect matching exists (or else raises an exception).
[lapsolver](https://github.com/cheind/py-lapsolver) | `solve_dense()` | Supports unbalanced problems. Supports imperfect matching.
[ortools](https://developers.google.com/optimization/assignment/linear_assignment) | `LinearSumAssignment` | Requires problem is balanced. Requires that a perfect matching exists (or else raises an exception). Requires that costs are integer-valued.
{: .table}

[1]: https://en.wikipedia.org/wiki/Assignment_problem
[2]: https://en.wikipedia.org/wiki/Matching_(graph_theory)
[3]: https://en.wikipedia.org/wiki/Hungarian_algorithm
[4]: https://en.wikipedia.org/wiki/Complete_bipartite_graph
[5]: https://en.wikipedia.org/wiki/Bipartite_graph
[6]: https://en.wikipedia.org/wiki/Integer_programming
[7]: https://www.hpl.hp.com/techreports/2012/HPL-2012-40R1.pdf
[8]: https://en.wikipedia.org/wiki/Maximum_cardinality_matching
