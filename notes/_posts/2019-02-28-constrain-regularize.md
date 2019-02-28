---
layout: post
title:  Equivalence of constraint and regularizer under strong duality
date:   2019-02-28
math:   true
---

## Question

Consider the constrained optimization problem in $\mathbb{R}^{n}$

$$ \min_{x} \; f(x) \quad \text{s.t.} \quad h(x) \le \tau $$

and the regularized optimization problem

$$ \min_{x} \; f(x) + \lambda h(x) $$

The question is: for each value of $\tau$, does there exist some $\lambda$ such that a minimizer of the constrained problem is also a minimizer of the regularized problem?

## Method

Let $x^{\star}$ denote a minimizer of the constrained problem, which therefore satisfies $h(x^{\star}) \le \tau$ (feasibility).
We seek to show that $x^{\star}$ is also a minimizer of the regularized problem.

First we observe that, if the inequality constraint is inactive (strict) at the minimizer $h(x^{\star}) < \tau$ then the constrained optimization problem is identical to the regularized problem with $\lambda = 0$.
Therefore we henceforth assume that the inequality constraint is active at the minimizer $h(x^{\star}) = \tau$.

Let us now consider the Lagrangian dual of the constrained problem, introducing the scalar dual variable $\alpha$.

$$ L(x, \alpha) = f(x) + \alpha (h(x) - \tau) $$

The dual function is obtained by the unconstrained minimization

$$ g(\alpha) = \min_{x} L(x, \alpha) = \min_{x} \{ f(x) + \alpha (h(x) - \tau) \} $$

and the dual problem is

$$ \max_{\alpha} \; g(\alpha) \quad \text{s.t.} \quad \alpha \ge 0 $$

Let $\alpha^{\star}$ denote a maximizer of the dual problem, which therefore satisfies $\alpha^{\star} \ge 0$ (dual feasibility).
If the constrained problem possesses _strong duality_ (for example, by Slater's condition), then we know that

$$ \begin{aligned}
f(x^{\star}) & = g(\alpha^{\star}) \\
& = \min_{x} \{ f(x) + \alpha^{\star} (h(x) - \tau) \} \\
f(x^{\star}) + \alpha^{\star} \tau & = \min_{x} \{ f(x) + \alpha^{\star} h(x) \}
\end{aligned} $$

Now we know the _value_ of the minimum of the regularized problem with $\lambda = \alpha^{star}$.
All that remains is to show that this minimum is obtained at $x = x^{\star}$, the minimizer of the constrained problem.
This is evident from the fact that $h(x^{\star}) = \tau$.

## Alternative method

Instead of separating the two cases of active and inactive constraints at the start, we can drop the assumption that $h(x^{\star}) = \tau$ and use the inequality chain that proves complementary slackness.

$$ \begin{aligned}
f(x^{\star}) & = g(\alpha^{\star}) \\
& = \min_{x} \{ f(x) + \alpha^{\star} (h(x) - \tau) \} \\
& \le f(x^{\star}) + \underbrace{\underbrace{\alpha^{\star}}_{\ge 0} \underbrace{(h(x^{\star}) - \tau)}_{\le 0}}_{\le 0} \\
& \le f(x^{\star})
\end{aligned} $$

This means that all inequalities in the chain hold with equality, and therefore

$$ \begin{aligned}
\min_{x} \{ f(x) + \alpha^{\star} (h(x) - \tau) \} & = f(x^{\star}) \\
\min_{x} \{ f(x) + \alpha^{\star} h(x) \} & = f(x^{\star}) + \alpha^{\star} \tau
\end{aligned} $$

This yields the complementary slackness condition

$$ \alpha^{\star} (h(x^{\star}) - \tau) = 0 $$

Hence either $h(x^{\star}) = \tau$ as above, or $\alpha^{\star} = 0$ (and the optimization problems are equivalent).
