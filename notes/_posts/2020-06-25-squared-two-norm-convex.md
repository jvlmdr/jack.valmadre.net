---
layout: post
title:  Inf-norm versus squared two-norm on probability simplex
math:   true
---

The $$\pi$$ be an $$n$$-dimensional vector on the probability simplex, that is $$\pi_{i} \ge 0$$ and $$\sum_{i} \pi_{i} = 1$$.

It is trivial to show that $$\|\pi\|_{2} \ge \|\pi\|_{\infty}$$.
Let us instead compare the _squared_ 2-norm to the infinity norm.
Without loss of generality, assume that $$\pi_{1}$$ is the largest element.

$$
\begin{align}
\|\pi\|_2^2 - \|\pi\|_{\infty}
& = \pi_{1}^{2} + \cdots + \pi_{n}^{2} - \pi_{1} \\
& = -\pi_{1}(1 - \pi_{1}) + \cdots + \pi_{n}^{2} \\
& = -\pi_{1}(\pi_{2} + \cdots + \pi_{n}) + \pi_{2}^{2} + \cdots + \pi_{n}^{2} \\
& = \underbrace{\pi_{2}}_{\ge 0} \underbrace{(\pi_{2} - \pi_{1})}_{\le 0} + \cdots + \pi_{n} (\pi_{n} - \pi_{1}) \\
& \le 0
\end{align}
$$

Overall we have $$\|\pi\|_{2}^{2} \le \|\pi\|_{\infty} \le \|\pi\|_{2}$$.

This also provides a sufficient (and probably necessary) condition for equality: $$\pi_{i} = 0$$ or $$\pi_{i} = \pi_{1}$$ for all $$i$$.
