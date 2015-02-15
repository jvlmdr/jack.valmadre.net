---
layout: post
title:  Asymmetric least squares
math:   true
---

$$
E(w, b) = \gamma \mathbb{E}_{x|c=1} (w^{T} x + b - y_{1})^{2}
  + (1 - \gamma) \mathbb{E}_{x|c=2} (w^{T} x + b - y_{2})^{2}
$$

$$
\frac{\partial E}{\partial b} =
  2 \gamma (b + w^{T} \bar{x}_{1} - y_{1})
  + 2 (1 - \gamma) (b + w^{T} \bar{x}_{2} - y_{2}) = 0
$$

$$
\begin{align}
b & = \gamma (y_{1} - w^{T} \bar{x}_{1})
  + (1 - \gamma) (y_{2} - w^{T} \bar{x}_{2}) \\
& = \gamma y_{1} + (1 - \gamma) y_{2}
  - w^{T} (\gamma \bar{x}_{1} + (1 - \gamma) \bar{x}_{2})
\end{align}
$$

Introducing $$\nu = \gamma \bar{x}_{1} + (1-\gamma) \bar{x}_{2}$$, the first expectation becomes

$$
\begin{align}
& \;\; \mathbb{E}_{x|c=1} (w^{T} x - w^{T} \nu - y_{1} + \gamma y_{1} + (1 - \gamma) y_{2})^{2} \\
& = \mathbb{E}_{x|c=1} \{w^{T} (x - \nu) - (1-\gamma) (y_{1} - y_{2})\}^{2}
\end{align}
$$

and the second

$$
\begin{align}
& \;\; \mathbb{E}_{x|c=2} (w^{T} x - w^{T} \nu - y_{2} + \gamma y_{1} + (1 - \gamma) y_{2})^{2} \\
& = \mathbb{E}_{x|c=2} \{w^{T} (x - \nu) - \gamma (y_{2} - y_{1})\}^{2}
\end{align}
$$

Together this gives

$$
\begin{align}
E(w, b^{\star}(w)) & =
  \gamma \mathbb{E}_{x|c=1} \{w^{T} (x - \nu) - (1-\gamma) (y_{1} - y_{2})\}^{2} \\
  & \quad + (1 - \gamma) \mathbb{E}_{x|c=2} \{w^{T} (x - \nu) - \gamma (y_{2} - y_{1})\}^{2} \\
& = w^{T} S w - 2 w^{T} r + \text{const}
\end{align}
$$

where

$$
\begin{align}
S & = \gamma \mathbb{E}_{x|c=1} (x-\nu)(x-\nu)^{T} + (1-\gamma) \mathbb{E}_{x|c=2} (x-\nu)(x-\nu)^{T} \\
& = \gamma \left[ \mathbb{E}_{x|c=1} x x^{T} - \bar{x}_{1} \nu^{T} - \nu \bar{x}_{1}^{T} + \nu \nu^{T} \right]
  + (1-\gamma) \left[ \mathbb{E}_{x|c=2} x x^{T} - \bar{x}_{2} \nu^{T} - \nu \bar{x}_{2}^{T} + \nu \nu^{T} \right] \\
& = \gamma \mathbb{E}_{x|c=1} x x^{T} + (1-\gamma) \mathbb{E}_{x|c=2} x x^{T} - \nu \nu^{T}
\end{align}
$$

$$
\begin{align}
r & = \gamma (1-\gamma) \left[ (y_{1} - y_{2}) (\bar{x}_{1} - \nu)
  + (y_{2} - y_{1}) (\bar{x}_{2} - \nu) \right] \\
& = \gamma (1-\gamma) (y_{1} - y_{2}) (\bar{x}_{1} - \bar{x}_{2})
\end{align}
$$

and then finally

$$
w = \gamma (1-\gamma) (y_{1} - y_{2}) \cdot S^{-1} (\bar{x}_{1} - \bar{x}_{2})
$$

giving the overall function

$$
\begin{align}
f(x) & = w^{T} x + b \\
& = \gamma (1-\gamma) (y_{1} - y_{2}) \cdot (x - \nu)^{T} S^{-1} (\bar{x}_{1} - \bar{x}_{2})
  + \gamma y_{1} + (1 - \gamma) y_{2} \\
& = (y_{1} - y_{2}) \gamma (1-\gamma) \cdot (x - \nu)^{T} S^{-1} (\bar{x}_{1} - \bar{x}_{2})
  + y_{2} + \gamma (y_{1} - y_{2}) \\
& = y_{2} + (y_{1} - y_{2}) \gamma \left[ 1 + (1-\gamma) \cdot (x - \nu)^{T} S^{-1} (\bar{x}_{1} - \bar{x}_{2}) \right]
\end{align}
$$
