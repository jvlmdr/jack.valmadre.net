---
layout: post
title:  "Solving linear equations: Which factorisation to use?"
math:   true
---

When you use LAPACK directly to solve a system of equations $$A x = b$$, you have to know which algorithm to use.
The choice of algorithm is determined by what is known about the matrix.

The table below lists the properties that each LAPACK routine requires, and the factorisation that it uses.
This information is available from the [documentation](http://www.netlib.org/lapack/double/).
It's probably best to use the most specific algorithm.

| Function | Properties                    | Factorisation | Subroutine        |
|----------+-------------------------------+---------------+-------------------|
| `Xgels`  | full rank                     | QR/LQ         | `Xgeqrf`/`Xgelqf` |
| `Xgesv`  | full rank, square             | LU            | `Xgbtrf`          |
| `Xsysv`  | full rank, symmetric          | LDL           | `Xsytrf`          |
| `Xposv`  | full rank, symmetric, pos def | Cholesky      | `Xpotrf`          |

.

If the matrix is not full rank, then you can use the SVD or the eigendecomposition, choose a threshold below which values are deemed to be zero, and then solve the system with matrix multiplication.

| Function | Properties | Factorisation |
|----------+------------+---------------|
| `Xgesvd` |            | SVD           |
| `Xsyev`  | symmetric  | eigen         |
