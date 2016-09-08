---
layout: post
title:  Differentials for back-propagation
date:   2016-09-04
math:   true
macros: '{reals: "\\mathbb{R}", trace: "\\operatorname{tr}", tovec: "\\operatorname{vec}"}'
---

- dummy list
{:toc}

Introduction to differentials
-----------------------------

### Differential of a vector function ###

Let $f(x)$ be a function that maps $\reals^{n} \to \reals^{m}$.
The derivative $\partial f(x) / \partial x$ is an $m \times n$ matrix that defines the linear approximation
\begin{equation}
f(x + u) \approx f(x) + \frac{\partial f(x)}{\partial x} \times u \enspace .
\end{equation}
The differential $df$ is an alternative way of expressing this approximation
\begin{equation}
f(x + u) \approx f(x) + df(x; u)
\end{equation}
where $df(x; u)$ must be linear in $u$.

### Chain rule for differentials ###

The chain rule for differentials states that if $h(x) = g(f(x))$ then
\begin{equation}
dh(x; u) = dg(f(x); df(x; u)) \enspace .
\end{equation}
The differential $dh$ is clearly linear in $u$ because it is the composition of two linear functions.

### Differential of a variable ###

The chain rule enables some useful notation.
Let us introduce the variable $y = f(x)$ and write the differential of the *variable* $y$ as a function of the differential of $x$
\begin{equation} dy = df(x; dx) \enspace . \end{equation}
We can similarly introduce $z = g(y)$ and write the differential of $z$
\begin{equation} dz = dg(y; dy) \enspace . \end{equation}
While these expressions were obtained in isolation, each without knowledge of the other, they can be combined and the result agrees with the chain rule
\begin{equation}
dz = dg(y; df(x; dx)) \enspace .
\end{equation}
Therefore, knowledge of the entire system in which the expression resides is not required to take differentials.
That is, the expression of $dz$ in terms of $dy$ was valid whether $dy$ was an arbitrary vector or a function of $dx$.
This is known as Cauchy's rule of invariance.

### Common rules in differential form ###

The product rule becomes
\begin{equation}
d(a \odot b) = da \odot b + a \odot db
\end{equation}
where a product is any operation that distributes over addition
\begin{equation}
a \odot (b + c) = a \odot b + a \odot c \enspace .
\end{equation}
This includes the element-wise product, matrix multiplication, the inner product, the Kronecker product, etc.

### Extension to matrices ###

The differential form is easily extended to matrix functions $F : \reals^{m \times n} \to \reals^{p \times q}$
\begin{equation}
F(X + U) \approx F(X) + dF(X; U)
\end{equation}
where the differential $dF(X; U)$ is linear in the elements of $U$.

We will now use matrix differentials and the product rule to find the derivative of the $n \times n$ matrix inverse $Y = f(X) = X^{-1}$.
This function can alternatively be defined implicitly $X Y = I$.
Taking differentials of both sides gives
\begin{align}
dY X + Y dX & = 0 \\\
dY & = -Y dX Y
\end{align}
This gives an expression for the differential $dY = df(X; dX)$ that is linear in $dX$ as expected.

### Finding derivatives using differentials ###

Note that the differential provides the derivative as an *operator*
\begin{equation}
u \mapsto \frac{\partial f(x)}{\partial x} \times u
\end{equation}
rather than as an array of values.
The explicit derivative can be trivially extracted from an expression for the differential $df(x; dx)$ by taking the coefficients of $dx$ since
\begin{equation}
dy_{i} = df_{i}(x; dx) = \sum_{j} \frac{\partial f_{i}(x)}{\partial x_{j}} dx_{j} \enspace .
\end{equation}
This is known as the identification theorem.

For the example of the matrix inverse, this must be generalized to matrices
\begin{equation}
dY_{i j} = dF_{i j}(X; dX) = \sum_{k = 1}^{n} \sum_{\ell = 1}^{n} \frac{\partial F_{i j}(X)}{\partial X_{k \ell}} dX_{k \ell} \enspace .
\end{equation}
Comparing this to the element-wise expression of the differential
\begin{equation}
dY_{i j} = - \sum_{k = 1}^{n} \sum_{\ell = 1}^{n} Y_{i k} dX_{k \ell} Y_{\ell j}
\end{equation}
provides the derivative
\begin{equation}
\frac{\partial F_{i j}(X)}{\partial X_{k \ell}} = -Y_{i k} Y_{\ell j} \enspace .
\end{equation}


Back-propagation and differentials
----------------------------------

### Back-propagation ###

Back-propagation is an algorithm for computing the derivatives of a *scalar* energy $E$, which is a composition of $n$ functions
\begin{equation}
E = f_{n}( \cdots f_{2}(f_{1}( x , \theta_{1}), \theta_{2}) \cdots , \theta_{n}) \enspace ,
\end{equation}
with respect to the parameters $\theta_{i}$ of each function $f_{i}$.
That is, we want to find
\begin{equation}
\frac{\partial E}{\partial \theta_{1}}, \frac{\partial E}{\partial \theta_{2}}, \dots, \frac{\partial E}{\partial \theta_{n}} \enspace .
\end{equation}
Let the variables $y_{i}$ represent the intermediate results so that
\begin{equation}
y_{i} = f_{i}(y_{i-1}, \theta_{i}) \enspace ,
\end{equation}
with $y_{0} = x$ and $y_{n} = E$.

It is trivial to obtain the derivatives with respect to $y_{n-1}$ and $\theta_{n}$ from the derivatives of $f_{n}$ according to
\begin{align}
\frac{\partial E}{\partial y_{n-1}}
  & = \frac{\partial f_{n}(y_{n-1}, \theta_{n})}{\partial y_{n-1}} \enspace , &
\frac{\partial E}{\partial \theta_{n}}
  & = \frac{\partial f_{n}(y_{n-1}, \theta_{n})}{\partial \theta_{n}} \enspace .
\end{align}
Back-propagation proceeds by obtaining the derivatives with respect to $\theta_{i}$ and $y_{i-1}$ from the derivative with respect to $y_{i}$
\begin{align}
\frac{\partial E}{\partial \theta_{i}}
  & = \frac{\partial E}{\partial y_{i}} \times \frac{\partial y_{i}}{\partial \theta_{i}}
    = \frac{\partial E}{\partial y_{i}} \times \frac{\partial f_{i}(y_{i-1}, \theta_{i})}{\partial \theta_{i}} \\\
\frac{\partial E}{\partial y_{i-1}}
  & = \frac{\partial E}{\partial y_{i}} \times \frac{\partial y_{i}}{\partial y_{i-1}}
    = \frac{\partial E}{\partial y_{i}} \times \frac{\partial f_{i}(y_{i-1}, \theta_{i})}{\partial y_{i-1}}
\end{align}
and so on.

### Forward propagation using differentials ###

Whereas back-propagation computes
\begin{equation}
\frac{\partial E}{\partial \theta_{i}}
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
  \enspace ,
\end{equation}
forward propagation computes
\begin{equation}
\frac{\partial E}{\partial \theta_{i}}
= \frac{\partial E}{\partial y_{n-1}} \times \left(
  \frac{\partial y_{n-1}}{\partial y_{n-2}} \times \left(
  \cdots
  \times \left(
  \frac{\partial y_{i+1}}{\partial y_{i}} \times \frac{\partial y_{i}}{\partial \theta_{i}}
  \right)
  \cdots
  \right)
  \right)
  \enspace .
\end{equation}
Forward propagation is much less efficient for two main reasons: the computation is not shared for different $i$, and one of the matrix dimensions is always 1 in back propagation.

Breaking forward propagation down into steps, the first step is to compute
\begin{equation}
\frac{\partial y_{i}}{\partial \theta_{i}}
  = \frac{\partial f_{i}(y_{i-1}, \theta_{i})}{\partial \theta_{i}}
\end{equation}
and the next steps are to compute, for $j = i+1, \dots, n$,
\begin{equation}
\frac{\partial y_{j}}{\partial \theta_{i}}
  = \frac{\partial f_{j}(y_{j-1}, \theta_{j})}{\partial y_{j-1}}
    \times \frac{\partial y_{j-1}}{\partial \theta_{i}} \enspace .
\end{equation}
Note that this product is exactly the operator that is defined by the differential.
Therefore, if we have the differential $df_{j}(y_{j-1}, \theta_{j}; dy_{j-1}, d\theta_{j})$, we can use it to perform forward propagation
\begin{equation}
\frac{\partial y_{j}}{\partial \theta_{i}}
  = df_{j}\bigg(y_{j-1}, \theta_{j}; \frac{\partial y_{j-1}}{\partial \theta_{i}}, 0\bigg) \enspace .
\end{equation}

However, recall that forward propagation is much less efficient than back-propagation.
The question remains: how to use differentials to obtain an operator for back-propagation?

### Back-propagation using differentials ###

Consider a scalar cost $E = g(y)$ where $y = f(x)$, and define $h(x) = g(f(x))$.
The definition of the differential and Cauchy's rule of invariance provide
\begin{equation}
dE = \frac{\partial E}{\partial y} \times dy = \frac{\partial E}{\partial x} \times dx \enspace .
\end{equation}
Since $E$ is a scalar, it's more natural to write this as an inner product
\begin{equation}
\langle \nabla_{y} E, dy \rangle = \langle \nabla_{x} E, dx \rangle
\end{equation}
where the gradient $\nabla_{x} E$ denotes a vector in the same space as $x$, instead of the operator $\partial E / \partial x$ that maps that space to $\reals$.
When $x$ is a vector, the gradient is the transpose of the derivative $\nabla_{x} E = (\partial E / \partial x)^{T}$.
A useful short-hand is to adopt $\bar{x} = \nabla_{x} E$, since no specific knowledge of $E$ is required for this identity
\begin{equation}
\langle \bar{y}, dy \rangle = \langle \bar{x}, dx \rangle \enspace .
\end{equation}
Manipulation of this equation after substituting an expression for $dy$ in terms of $dx$ can provide an expression for $\bar{x} = \nabla_{x} E$ as a linear function of $\bar{y} = \nabla_{y} E$, precisely the operator required for back-propagation!

Critically, observe that it is much easier to preserve the structure of the intermediate variables with the back-propagation operator, whereas the forward-propagation operator requires all variables to be vectorized.

Let's return to the example of the matrix inverse.
For matrices, it's useful to recall the inner product $\langle A, B \rangle = \trace(A^{T} B)$ and the trace rotation $\trace(A B C) = \trace(C A B) = \trace(B C A)$ for compatible dimensions.
Substituting the expression for $dY$ in the identity gives
\begin{align}
\langle \bar{X}, dX \rangle = \langle \bar{Y}, dY \rangle
& = \langle \bar{Y}, -Y dX Y \rangle \\\
& = -\trace(\bar{Y}^{T} Y dX Y) \\\
& = \langle -Y^{T} \bar{Y} Y^{T}, dX \rangle
\end{align}
and therefore
\begin{equation}
\nabla_{X} E = -Y^{T} \left(\nabla_{Y} E\right) Y^{T}
\end{equation}

In the case of the matrix inverse, this expression could have been obtained from the forward operator using vectorization and Kronecker product identities
\begin{align}
\tovec(dY) & = \tovec(-Y dX Y) \\\
\frac{\partial \tovec(Y)}{\partial \tovec(X)} \times \tovec(dX) & = -(Y^{T} \otimes Y) \tovec(dX) \\\
\frac{\partial \tovec(Y)}{\partial \tovec(X)} & = -Y^{T} \otimes Y
\end{align}
and then
\begin{align}
\frac{\partial E}{\partial \tovec(X)}
  & = \frac{\partial E}{\partial \tovec(Y)} \times \frac{\partial \tovec(Y)}{\partial \tovec(X)} \\\
\tovec(\nabla_{X} E)^{T} & = \tovec(\nabla_{Y} E)^{T} \times (-Y^{T} \otimes Y) \\\
\nabla_{X} E & = -Y^{T} \left(\nabla_{Y} E\right) Y^{T}
\enspace .
\end{align}
However, it is somewhat messier.
Since we used differentials to find the forward operator, we might as well use them to find the backward operator.

More examples
--------------

### Gram matrix ###

Consider the function $Y = f(X) = X^{T} X$. Taking differentials and applying the product rule gives

\begin{equation}
dY = dX^{T} X + X^{T} dX
\end{equation}

then making the substitution gives

\begin{align}
\langle \bar{Y}, dY \rangle & = \langle \bar{Y}, dX^{T} X + X^{T} dX \rangle \\\
& = \trace(\bar{Y}^{T} dX^{T} X) + \trace(\bar{Y}^{T} X^{T} dX) \\\
& = \trace(dX^{T} X \bar{Y}^{T}) + \trace[(X \bar{Y})^{T} dX] \\\
& = \langle dX, X \bar{Y}^{T} + X \bar{Y} \rangle = \langle \bar{X}, dX \rangle
\end{align}

and therefore $$\bar{X} = X (\bar{Y} + \bar{Y}^{T})$$.
