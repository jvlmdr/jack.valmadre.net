---
layout: post
title:  Linear discriminant analysis and unsupervised covariance
math:   true
---

In linear discriminant analysis, there are two classes and their distributions are assumed to be Gaussian with the same covariance.
The means of the two distributions are $$\mu_{1}$$ and $$\mu_{2}$$ and the shared covariance is $$\Sigma$$.
The maximum likelihood estimates of the conditional means are simply the empirical means

$$
\mu_{k} = \bar{x}_{k} = \frac{1}{n_{k}} \sum_{i \in \mathcal{X}_{k}} x_{i}
$$

and the maximum likelihood estimate of the shared covariance matrix is the emprical "within-class" covariance matrix

$$
\Sigma = S_{W} = \frac{1}{n} \sum_{k \in \{1, 2\}} \sum_{i \in \mathcal{X}_{k}} (x_{i} - \bar{x}_{k}) (x_{i} - \bar{x}_{k})^{T}
= \frac{1}{n} (n_{1} S_{1} + n_{2} S_{2}) .
$$

Here $$\mathcal{X}_{k}$$ is the set of examples in class $$k$$, $$n_{k} = |\mathcal{X}_{k}|$$ is the number of examples in each class, $$n = n_{1} + n_{2}$$ is the total number of examples, and $$S_{k}$$ is the empirical covariance of each class.
The within-class covariance matrix can also be expressed

$$
S_{W} = \frac{1}{n} \sum_{i = 1}^{n} x_{i} x_{i}^{T}
  - \frac{n_{1}}{n} \bar{x}_{1} \bar{x}_{1}^{T} - \frac{n_{2}}{n} \bar{x}_{2} \bar{x}_{2}^{T} .
$$

The _unsupervised_ covariance is

$$
S = \frac{1}{n} \sum_{i = 1}^{n} (x_{i} - \bar{x}) (x_{i} - \bar{x})^{T}
= \frac{1}{n} \sum_{i = 1}^{n} x_{i} x_{i}^{T} - \bar{x} \bar{x}^{T}
$$

where $$\bar{x}$$ is the mean of all examples.
Combining these gives

$$
\frac{1}{n} \sum_{i = 1}^{n} x_{i} x_{i}^{T}
  = S_{W} + \frac{n_{1}}{n} \bar{x}_{1} \bar{x}_{1}^{T} + \frac{n_{2}}{n} \bar{x}_{2} \bar{x}_{2}^{T}
  = S + \bar{x} \bar{x}^{T}
$$

and then with some manipulation

$$
\begin{align}
n^{2} S_{W} + n_{1} (n_{1} + n_{2}) \bar{x}_{1} \bar{x}_{1}^{T} + n_{2} (n_{1} + n_{2}) \bar{x}_{2} \bar{x}_{2}^{T}
  & = n^{2} S + (n_{1} \bar{x}_{1} + n_{2} \bar{x}_{2}) (n_{1} \bar{x}_{1} + n_{2} \bar{x}_{2})^{T} \\
n^{2} S_{W} + n_{1} n_{2} \bar{x}_{1} \bar{x}_{1}^{T} + n_{1} n_{2} \bar{x}_{2} \bar{x}_{2}^{T}
  & = n^{2} S + n_{1} n_{2} \bar{x}_{1} \bar{x}_{2}^{T} + n_{1} n_{2} \bar{x}_{2} \bar{x}_{1}^{T} \\
S_{W} + \frac{n_{1} n_{2}}{n^{2}} (\bar{x}_{1} - \bar{x}_{2}) (\bar{x}_{1} - \bar{x}_{2})^{T} & = S .
\end{align}
$$

The system of equations $$S w = \bar{x}_{1} - \bar{x}_{2}$$ has a special form.
If $$x$$ is a solution to the square system of equations $$A x = b$$, then for any scalar $$\alpha$$

$$ (A + \alpha b b^{T}) x = b + \alpha b b^{T} x = (1 + \alpha b^{T} x) b . $$

This means that a solution to $$(A + \alpha b b^{T}) z = b$$ is

$$ z = \frac{1}{1 + \alpha b^{T} x} x . $$

This allows us to draw the conclusion that

$$
S^{-1} (\bar{x}_{1} - \bar{x}_{2}) \propto S_{W}^{-1} (\bar{x}_{1} - \bar{x}_{2})
$$

and therefore the LDA solutions using the within-class covariance and the unsupervised covariance are equivalent.
