---
layout: post
title:  Differentials for back-propagation
date:   2016-09-04
math:   true
---

- dummy list
{:toc}

Introduction to differentials
-----------------------------

### Differential of a vector function ###

Let $f(x)$ be a function that maps
${\mathbb{R}}^{n} \to {\mathbb{R}}^{m}$. The derivative
$\partial f(x) / \partial x$ is an $m \times n$ matrix that defines the
linear approximation

$$f(x + u) \approx f(x) + \frac{\partial f(x)}{\partial x} \times u \enspace .$$

The differential $df$ is an alternative way of expressing this
approximation

$$f(x + u) \approx f(x) + df(x; u)$$

where $df(x; u)$ must
be linear in $u$.

### Chain rule for differentials ###

The chain rule for differentials states that if $h(x) = g(f(x))$ then

$$dh(x; u) = dg(f(x); df(x; u)) \enspace .$$

The differential $dh$ is
clearly linear in $u$ because it is the composition of two linear
functions.

### Differential of a variable ###

The chain rule enables some useful notation. Let us introduce the
variable $y = f(x)$ and write the differential of the *variable* $y$ as
a function of the differential of $x$

$$dy = df(x; dx) \enspace .$$

We
can similarly introduce $z = g(y)$ and write the differential of $z$

$$dz = dg(y; dy) \enspace .$$

While these expressions were obtained in
isolation, each without knowledge of the other, they can be combined and
the result agrees with the chain rule

$$dz = dg(y; df(x; dx)) \enspace .$$

Therefore, knowledge of the entire
system in which the expression resides is not required to take
differentials. That is, the expression of $dz$ in terms of $dy$ was
valid whether $dy$ was an arbitrary vector or a function of $dx$. This
is known as Cauchy’s rule of invariance.

### Common rules in differential form ###

The product rule becomes

$$d(a \otimes b) = da \otimes b + a \otimes db$$

where the product
$\odot$ can be any operation that distributes over addition

$$a \otimes (b + c) = a \otimes b + a \otimes c \enspace .$$

This
includes the element-wise product, matrix multiplication, the inner
product, the Kronecker product, etc.

### Extension to matrices ###

The differential form is easily extended to matrix functions
$F : {\mathbb{R}}^{m \times n} \to {\mathbb{R}}^{p \times q}$

$$F(X + U) \approx F(X) + dF(X; U)$$

where the differential $dF(X; U)$
is linear in the elements of $U$.

We will now use matrix differentials and the product rule to find the
derivative of the $n \times n$ matrix inverse $Y = f(X) = X^{-1}$. This
function can alternatively be defined implicitly $X Y = I$. Taking
differentials of both sides gives

$$\begin{aligned}
dY X + Y dX & = 0 \\
dY & = -Y dX Y\end{aligned}$$

This gives an expression for the
differential $dY = df(X; dX)$ that is linear in $dX$ as expected.

### Finding derivatives using differentials ###

Note that the differential provides the derivative as an *operator*

$$u \mapsto \frac{\partial f(x)}{\partial x} \times u$$

rather than as
an array of values. The explicit derivative can be trivially extracted
from an expression for the differential $df(x; dx)$ by taking the
coefficients of $dx$ since

$$dy_{i} = df_{i}(x; dx) = \sum_{j} \frac{\partial f_{i}(x)}{\partial x_{j}} dx_{j} \enspace .$$

This is known as the identification theorem.

For the example of the matrix inverse, this must be generalized to
matrices

$$dY_{i j} = dF_{i j}(X; dX) = \sum_{k = 1}^{n} \sum_{\ell = 1}^{n} \frac{\partial F_{i j}(X)}{\partial X_{k \ell}} dX_{k \ell} \enspace .$$

Comparing this to the element-wise expression of the differential

$$dY_{i j} = - \sum_{k = 1}^{n} \sum_{\ell = 1}^{n} Y_{i k} dX_{k \ell} Y_{\ell j}$$

provides the derivative

$$\frac{\partial F_{i j}(X)}{\partial X_{k \ell}} = -Y_{i k} Y_{\ell j} \enspace .$$


Back-propagation and differentials
----------------------------------

### Back-propagation ###

Back-propagation is an algorithm for computing the derivatives of a
*scalar* energy $E$, which is a composition of $n$ functions

$$E = f_{n}( \cdots f_{2}(f_{1}( x , \theta_{1}), \theta_{2}) \cdots , \theta_{n}) \enspace ,$$

with respect to the parameters $\theta_{i}$ of each function $f_{i}$.
That is, we want to find

$$\frac{\partial E}{\partial \theta_{1}}, \frac{\partial E}{\partial \theta_{2}}, \dots, \frac{\partial E}{\partial \theta_{n}} \enspace .$$

Let the variables $y_{i}$ represent the intermediate results so that

$$y_{i} = f_{i}(y_{i-1}, \theta_{i}) \enspace ,$$

with $y_{0} = x$ and
$y_{n} = E$.

It is trivial to obtain the derivatives with respect to $y_{n-1}$ and
$\theta_{n}$ from the derivatives of $f_{n}$ according to

$$\begin{aligned}
\frac{\partial E}{\partial y_{n-1}}
  & = \frac{\partial f_{n}(y_{n-1}, \theta_{n})}{\partial y_{n-1}} \enspace , &
\frac{\partial E}{\partial \theta_{n}}
  & = \frac{\partial f_{n}(y_{n-1}, \theta_{n})}{\partial \theta_{n}} \enspace .\end{aligned}$$

Back-propagation proceeds by obtaining the derivatives with respect to
$\theta_{i}$ and $y_{i-1}$ from the derivative with respect to $y_{i}$

$$\begin{aligned}
\frac{\partial E}{\partial \theta_{i}}
& = \frac{\partial E}{\partial y_{i}} \times \frac{\partial y_{i}}{\partial \theta_{i}}
= \frac{\partial E}{\partial y_{i}} \times \frac{\partial f_{i}(y_{i-1}, \theta_{i})}{\partial \theta_{i}} \\
\frac{\partial E}{\partial y_{i-1}}
& = \frac{\partial E}{\partial y_{i}} \times \frac{\partial y_{i}}{\partial y_{i-1}}
= \frac{\partial E}{\partial y_{i}} \times \frac{\partial f_{i}(y_{i-1}, \theta_{i})}{\partial y_{i-1}} \enspace .\end{aligned}$$

and so on.

### Forward propagation using differentials ###

Whereas back-propagation computes

$$\frac{\partial E}{\partial \theta_{i}}
= \left(
  \left(
  \cdots
  \left(
  \frac{\partial E}{\partial y_{n-1}}
    \times \frac{\partial y_{n-1}}{\partial y_{n-2}}
  \right) \times
  \cdots
  \right) \times \frac{\partial y_{i+1}}{\partial y_{i}}
  \right) \times \frac{\partial y_{i}}{\partial \theta_{i}}
  \enspace ,$$

forward propagation computes

$$\frac{\partial E}{\partial \theta_{i}}
= \frac{\partial E}{\partial y_{n-1}} \times \left(
  \frac{\partial y_{n-1}}{\partial y_{n-2}} \times \left(
  \cdots
  \times \left(
  \frac{\partial y_{i+1}}{\partial y_{i}} \times \frac{\partial y_{i}}{\partial \theta_{i}}
  \right)
  \cdots
  \right)
  \right)
  \enspace .$$

Forward propagation is much less efficient for two main
reasons: the computation is not shared for different $i$, and one of the
matrix dimensions is always 1 in back propagation.

Breaking forward propagation down into steps, the first step is to
compute

$$\frac{\partial y_{i}}{\partial \theta_{i}}
  = \frac{\partial f_{i}(y_{i-1}, \theta_{i})}{\partial \theta_{i}}$$

and the next steps are to compute, for $j = i+1, \dots, n$,

$$\frac{\partial y_{j}}{\partial \theta_{i}}
  = \frac{\partial f_{j}(y_{j-1}, \theta_{j})}{\partial y_{j-1}}
    \times \frac{\partial y_{j-1}}{\partial \theta_{i}} \enspace .$$

Note that this product is exactly the operator that is defined by the
differential. Therefore, if we have the differential
$df_{j}(y_{j-1}, \theta_{j}; dy_{j-1}, d\theta_{j})$, we can use it to
perform forward propagation

$$\frac{\partial y_{j}}{\partial \theta_{i}}
  = df_{j}\bigg(y_{j-1}, \theta_{j}; \frac{\partial y_{j-1}}{\partial \theta_{i}}, 0\bigg) \enspace .$$


However, recall that forward propagation is much less efficient than
back-propagation. The question remains: how to use differentials to
obtain an operator for back-propagation?

### Back-propagation using differentials ###

Consider a scalar cost $E = g(y)$ where $y = f(x)$, and define
$h(x) = g(f(x))$. The definition of the differential and Cauchy’s rule
of invariance provide

$$dE = \frac{\partial E}{\partial y} \times dy = \frac{\partial E}{\partial x} \times dx \enspace .$$

Since $E$ is a scalar, it’s more natural to write this as an inner
product

$$\langle \nabla_{y} E, dy \rangle = \langle \nabla_{x} E, dx \rangle$$

where the gradient $\nabla_{x} E$ denotes a vector in the same space as
$x$, instead of the operator $\partial E / \partial x$ which maps that
space to ${\mathbb{R}}$. When $x$ is a vector, the gradient is the
transpose of the derivative
$\nabla_{x} E = (\partial E / \partial x)^{T}$. A useful short-hand is
to adopt $\bar{x} = \nabla_{x} E$, since no specific knowledge of $E$ is
required for this identity

$$\langle \bar{y}, dy \rangle = \langle \bar{x}, dx \rangle \enspace .$$

Manipulation of this equation after substituting an expression for $dy$
in terms of $dx$ can provide an expression for $\bar{x} = \nabla_{x} E$
as a linear function of $\bar{y} = \nabla_{y} E$, precisely the operator
required for back-propagation!

Critically, observe that it is much easier to preserve the structure of
the intermediate variables with the back-propagation operator, whereas
the forward-propagation operator requires all variables to be
vectorized.

Let’s return to the example of the matrix inverse. For matrices, it’s
useful to recall the inner product
$\langle A, B \rangle = {\operatorname{tr}}(A^{T} B)$ and the trace
rotation
${\operatorname{tr}}(A B C) = {\operatorname{tr}}(C A B) = {\operatorname{tr}}(B C A)$
for compatible dimensions. Substituting the expression for $dY$ in the
identity gives

$$\begin{aligned}
\langle \bar{X}, dX \rangle = \langle \bar{Y}, dY \rangle
& = \langle \bar{Y}, -Y dX Y \rangle \\
& = -{\operatorname{tr}}(\bar{Y}^{T} Y dX Y) \\
& = \langle -Y^{T} \bar{Y} Y^{T}, dX \rangle\end{aligned}$$

and
therefore

$$\nabla_{X} E = -Y^{T} \left(\nabla_{Y} E\right) Y^{T}$$


In the case of the matrix inverse, this expression could have been
obtained from the forward operator using vectorization and Kronecker
product identities

$$\begin{aligned}
{\operatorname{vec}}(dY) & = {\operatorname{vec}}(-Y dX Y) \\
\frac{\partial {\operatorname{vec}}(Y)}{\partial {\operatorname{vec}}(X)} \times {\operatorname{vec}}(dX) & = -(Y^{T} \otimes Y) {\operatorname{vec}}(dX) \\
\frac{\partial {\operatorname{vec}}(Y)}{\partial {\operatorname{vec}}(X)} & = -Y^{T} \otimes Y\end{aligned}$$

and then

$$\begin{aligned}
\frac{\partial E}{\partial {\operatorname{vec}}(X)}
  & = \frac{\partial E}{\partial {\operatorname{vec}}(Y)} \times \frac{\partial {\operatorname{vec}}(Y)}{\partial {\operatorname{vec}}(X)} \\
{\operatorname{vec}}(\nabla_{X} E)^{T} & = {\operatorname{vec}}(\nabla_{Y} E)^{T} \times (-Y^{T} \otimes Y) \\
\nabla_{X} E & = -Y^{T} \left(\nabla_{Y} E\right) Y^{T}
\enspace .\end{aligned}$$

However, it is somewhat messier. Since we used
differentials to find the forward operator, we might as well use them to
find the backward operator.

More examples
-------------

### Gram matrix ###

Consider the function $Y = f(X) = X^{T} X$. Taking differentials and
applying the product rule gives

$$dY = dX^{T} X + X^{T} dX$$

then making
the substitution gives

$$\begin{aligned}
\langle \bar{Y}, dY \rangle & = \langle \bar{Y}, dX^{T} X + X^{T} dX \rangle \\
& = {\operatorname{tr}}(\bar{Y}^{T} dX^{T} X) + {\operatorname{tr}}(\bar{Y}^{T} X^{T} dX) \\
& = {\operatorname{tr}}(dX^{T} X \bar{Y}^{T}) + {\operatorname{tr}}[(X \bar{Y})^{T} dX] \\
& = \langle dX, X \bar{Y}^{T} + X \bar{Y} \rangle = \langle \bar{X}, dX \rangle\end{aligned}$$

and therefore $\bar{X} = X (\bar{Y} + \bar{Y}^{T})$.

### Circular convolution ###

Consider the function $y = f(x) = a * x$. Taking differentials and
applying the product rule gives

$$dy = da * x + a * dx$$

then making the
substitution gives

$$\langle \bar{y}, dy \rangle = \langle \bar{y}, da * x + a * dx \rangle \enspace .$$

This inner product has an equivalent expression in the Fourier domain
since inner products are preserved.

$$\begin{aligned}
\langle F\bar{y}, F\{da * x + a * dx\} \rangle
& = \langle \hat{\bar{y}}, \hat{da} \circ \hat{x} \rangle
  + \langle \hat{\bar{y}}, \hat{a} \circ \hat{dx} \rangle \\
& = \langle \hat{\bar{y}} \circ \hat{x}^*, \hat{da} \rangle
  + \langle \hat{\bar{y}} \circ \hat{a}^*, \hat{dx} \rangle\end{aligned}$$

and therefore

$$\begin{aligned}
\langle \bar{y}, dy \rangle
& = \langle x \star \bar{y}, da \rangle + \langle a \star \bar{y}, dx \rangle \\
& = \langle \bar{a}, da \rangle + \langle \bar{x}, dx \rangle
\enspace .\end{aligned}$$

