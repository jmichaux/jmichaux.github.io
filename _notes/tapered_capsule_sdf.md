# Tapered Capsule Signed Distance Function

## Problem Formulation

### Geometric Setup

A **tapered capsule** (also called a cone-sphere or spherical cone) is defined by:
- A line segment from point $p_1$ to point $p_2$
- A radius $r_1$ at $p_1$ and radius $r_2$ at $p_2$
- The surface is the union of all spheres with centers on the line segment and radii linearly interpolated between $r_1$ and $r_2$

For two tapered capsules, we parameterize points along each capsule's axis:
- **Capsule 1**: Point at parameter $t \in [0,1]$ is $p_1 + t \cdot d_1$ with radius $r_1(t) = r_1^{(0)} + t(r_1^{(1)} - r_1^{(0)})$
- **Capsule 2**: Point at parameter $u \in [0,1]$ is $p_2 + u \cdot d_2$ with radius $r_2(u) = r_2^{(0)} + u(r_2^{(1)} - r_2^{(0)})$

Where:
- $d_1 = p_1^{end} - p_1^{start}$ (direction vector of capsule 1)
- $d_2 = p_2^{end} - p_2^{start}$ (direction vector of capsule 2)
- $d_{12} = p_1^{start} - p_2^{start}$ (offset between capsule origins)

For simplicity, we use linear radius functions:
- $r_1 = r_1 \cdot t$ (radius along capsule 1)
- $r_2 = r_2 \cdot u$ (radius along capsule 2)

---

## Optimization Problem (SD-OPT)

The signed distance between two tapered capsules is found by solving:

$$
\min_{t, u} \quad \|d_1 t - d_2 u - d_{12}\| - r_1 t - r_2 u
$$

Subject to:
$$
\begin{aligned}
t - 1 &\leq 0 \\
u - 1 &\leq 0 \\
-t &\leq 0 \\
-u &\leq 0
\end{aligned}
$$

This minimizes the distance between sphere centers minus their radii.

---

## Lagrangian Formulation

$$
\mathcal{L}(t, u, \lambda) = \|d_1 t - d_2 u - d_{12}\|_2 - r_1 t - r_2 u + \lambda_1(t-1) + \lambda_2(u-1) + \lambda_3(-t) + \lambda_4(-u)
$$

### KKT Conditions

**Primal Feasibility:**
$$
t^* - 1 \leq 0, \quad u^* - 1 \leq 0, \quad -t^* \leq 0, \quad -u^* \leq 0
$$

**Dual Feasibility:**
$$
\lambda_1^* \geq 0, \quad \lambda_2^* \geq 0, \quad \lambda_3^* \geq 0, \quad \lambda_4^* \geq 0
$$

**Complementary Slackness:**
$$
\begin{aligned}
\lambda_1^*(t^* - 1) &= 0 \\
\lambda_2^*(u^* - 1) &= 0 \\
\lambda_3^* t^* &= 0 \\
\lambda_4^* u^* &= 0
\end{aligned}
$$

---

## Stationarity Conditions

Define the shorthand notation for the norm:
$$
\|d_1 t - d_2 u - d_{12}\| = \sqrt{(d_1 t - d_2 u - d_{12})^\top (d_1 t - d_2 u - d_{12})}
$$

**Stationarity with respect to $t$:**
$$
\frac{\partial \mathcal{L}}{\partial t} = \frac{at - bu - c}{\|d_1 t - d_2 u - d_{12}\|} - r_1 + \lambda_1 - \lambda_3 = 0
$$

**Stationarity with respect to $u$:**
$$
\frac{\partial \mathcal{L}}{\partial u} = \frac{-bt + eu + f}{\|d_1 t - d_2 u - d_{12}\|} - r_2 + \lambda_2 - \lambda_4 = 0
$$

### Coefficient Substitutions

To simplify expressions, define:
$$
\begin{aligned}
a &= d_1^\top d_1 \\
b &= d_1^\top d_2 \\
c &= d_1^\top d_{12} \\
e &= d_2^\top d_2 \\
f &= d_2^\top d_{12} \\
g &= d_{12}^\top d_{12}
\end{aligned}
$$

The squared norm expands as:
$$
\|d_1 t - d_2 u - d_{12}\|^2 = at^2 - 2btu - 2ct + eu^2 + 2fu + g
$$

---

## Case Analysis

The box constraints $t, u \in [0, 1]$ create 9 possible cases based on which constraints are active:

### Non-Trivial Solutions (Interior or Single Boundary)

| Case | $t$ | $u$ | Description |
|------|-----|-----|-------------|
| 1 | $0$ | $u \in (0,1)$ | $t$ at lower bound |
| 2 | $1$ | $u \in (0,1)$ | $t$ at upper bound |
| 3 | $t \in (0,1)$ | $0$ | $u$ at lower bound |
| 4 | $t \in (0,1)$ | $1$ | $u$ at upper bound |
| 5 | $t \in (0,1)$ | $u \in (0,1)$ | Both in interior |

### Trivial Solutions (Corner Cases)

| Case | $t$ | $u$ |
|------|-----|-----|
| 6 | $0$ | $0$ |
| 7 | $0$ | $1$ |
| 8 | $1$ | $0$ |
| 9 | $1$ | $1$ |

*Note: There are also 7 infeasible solution regions.*

---

## Case 1: $t = 0$, $u \in (0, 1)$

**Active constraints:** $\lambda_1 = \lambda_2 = \lambda_4 = 0$, $\lambda_3 \geq 0$

**Approach:** Solve for $u$, then $\lambda_3$

### Stationarity Conditions

With $t = 0$:
$$
\frac{\partial \mathcal{L}}{\partial t} = \frac{-bu - c}{\|-d_2 u - d_{12}\|} - r_1 - \lambda_3 = 0
$$

$$
\frac{\partial \mathcal{L}}{\partial u} = \frac{eu + f}{\|-d_2 u - d_{12}\|} - r_2 = 0
$$

### Solution for $u$

From the second equation, squaring both sides:
$$
r_2^2 = \frac{(eu + f)^2}{\|d_2 u + d_{12}\|^2} = \frac{e^2 u^2 + 2efu + f^2}{eu^2 + 2fu + g}
$$

Rearranging to a quadratic in $u$:
$$
e(e - r_2^2)u^2 + 2(ef - r_2^2 f)u + (f^2 - r_2^2 g) = 0
$$

Let $\bar{a} = e(e - r_2^2)$, $\bar{b} = 2f(e - r_2^2)$, $\bar{c} = f^2 - r_2^2 g$

$$
\boxed{u = \frac{-f(e - r_2^2) \pm r_2\sqrt{(e - r_2^2)(eg - f^2)}}{e(e - r_2^2)}}
$$

### Solution for $\lambda_3$

$$
\lambda_3 = \frac{-bu - c}{\|d_1 t - d_2 u - d_{12}\|} - r_1 \quad (t = 0)
$$

---

## Case 2: $t = 1$, $u \in (0, 1)$

**Active constraints:** $\lambda_2 = \lambda_3 = \lambda_4 = 0$, $\lambda_1 \geq 0$

**Approach:** Solve for $u$, then $\lambda_1$

### Solution for $u$

Following similar algebra to Case 1, squaring the stationarity condition for $u$:

$$
\boxed{u = \frac{-(f-b)(e - r_2^2) \pm r_2\sqrt{(e - r_2^2)[e(g - 2c + a) - (f-b)^2]}}{e(e - r_2^2)}}
$$

### Solution for $\lambda_1$

$$
\lambda_1 = r_1 - \frac{at - bu - c}{\|d_1 t - d_2 u - d_{12}\|} \quad (t = 1)
$$

---

## Case 3: $t \in (0, 1)$, $u = 0$

**Active constraints:** $\lambda_1 = \lambda_2 = \lambda_3 = 0$, $\lambda_4 \geq 0$

**Approach:** Solve for $t$, then $\lambda_4$

### Solution for $t$

From stationarity with $u = 0$:
$$
\frac{at - c}{\|d_1 t - d_{12}\|} - r_1 = 0
$$

Squaring and solving the resulting quadratic:

$$
\boxed{t = \frac{c(a - r_1^2) \pm r_1\sqrt{(a - r_1^2)(ag - c^2)}}{a(a - r_1^2)}}
$$

### Solution for $\lambda_4$

$$
\lambda_4 = \frac{-bt + eu + f}{\|d_1 t - d_2 u - d_{12}\|} - r_2 \quad (u = 0)
$$

---

## Case 4: $t \in (0, 1)$, $u = 1$

**Active constraints:** $\lambda_1 = \lambda_3 = \lambda_4 = 0$, $\lambda_2 \geq 0$

**Approach:** Solve for $t$, then $\lambda_2$

### Solution for $t$

With $u = 1$:
$$
\frac{at - (b + c)}{\|d_1 t - (d_{12} + d_2)\|} - r_1 = 0
$$

Squaring and solving:

$$
\boxed{t = \frac{-(b+c)(a - r_1^2) \pm r_1\sqrt{(a - r_1^2)[a(g + 2f + e) - (b+c)^2]}}{a(a - r_1^2)}}
$$

### Solution for $\lambda_2$

$$
\lambda_2 = r_2 - \frac{-bt + eu + f}{\|d_1 t - d_2 u - d_{12}\|} \quad (u = 1)
$$

---

## Case 5: $t \in (0, 1)$, $u \in (0, 1)$ — Interior Solution

**Active constraints:** $\lambda_1 = \lambda_2 = \lambda_3 = \lambda_4 = 0$ (all inactive)

This is the most complex case where both parameters are in the interior.

### Stationarity Conditions

$$
\frac{at - bu - c}{\|d_1 t - d_2 u - d_{12}\|} - r_1 = 0 \quad (1)
$$

$$
\frac{-bt + eu + f}{\|d_1 t - d_2 u - d_{12}\|} - r_2 = 0 \quad (2)
$$

### Solving for $t$ in Terms of $u$

From equations (1) and (2), multiply both sides by the norm and eliminate the right-hand side:

$$
r_2(at - bu - c) = r_1 r_2 \|d_1 t - d_2 u - d_{12}\|
$$
$$
r_1(-bt + eu + f) = r_1 r_2 \|d_1 t - d_2 u - d_{12}\|
$$

Subtracting:
$$
r_2(at - bu - c) - r_1(-bt + eu + f) = 0
$$

Rearranging:
$$
(r_1 b + r_2 a)t = (r_2 b + r_1 e)u + (r_2 c + r_1 f)
$$

Define:
$$
\phi = \frac{r_2 b + r_1 e}{r_1 b + r_2 a}, \quad \gamma = \frac{r_2 c + r_1 f}{r_1 b + r_2 a}
$$

Then:
$$
t = \phi u + \gamma \quad (3)
$$

*Note: These expressions fail if TC1 and TC2 are standard capsules (constant radius), as the denominator may vanish.*

### Solving for $u$

Substituting $t = \phi u + \gamma$ into equation (1) or (2) and squaring yields a quadratic in $u$:

Define:
$$
\alpha = a\phi - b, \quad \beta = a\gamma - c
$$

After substitution and simplification:

$$
\bar{a} = \alpha^2 - r_1^2 \phi^2 a - r_1^2 e + 2r_1^2 b\phi
$$

$$
\bar{b} = 2\alpha\beta - 2r_1^2 \phi\gamma a + 2r_1^2 b\gamma + 2r_1^2 c\phi - 2r_1^2 f
$$

$$
\bar{c} = \beta^2 - r_1^2 a\gamma^2 + 2r_1^2 c\gamma - r_1^2 g
$$

$$
\boxed{u = \frac{-\bar{b} \pm \sqrt{\bar{b}^2 - 4\bar{a}\bar{c}}}{2\bar{a}}}
$$

$$
\boxed{t = \phi u + \gamma}
$$

---

## Solutions Summary

| Case | $t$ | $u$ |
|------|-----|-----|
| 1 | $0$ | $\displaystyle\frac{-f(e - r_2^2) \pm r_2\sqrt{(e - r_2^2)(eg - f^2)}}{e(e - r_2^2)}$ |
| 2 | $1$ | $\displaystyle\frac{-(f-b)(e - r_2^2) \pm r_2\sqrt{(e - r_2^2)[e(g - 2c + a) - (f-b)^2]}}{e(e - r_2^2)}$ |
| 3 | $\displaystyle\frac{c(a - r_1^2) \pm r_1\sqrt{(a - r_1^2)(ag - c^2)}}{a(a - r_1^2)}$ | $0$ |
| 4 | $\displaystyle\frac{-(b+c)(a - r_1^2) \pm r_1\sqrt{(a - r_1^2)[a(g + 2f + e) - (b+c)^2]}}{a(a - r_1^2)}$ | $1$ |
| 5 | $\phi u + \gamma$ | Quadratic formula (see above) |

---

## Gradient Computation

### Motivation

To use this SDF in optimization (e.g., trajectory optimization, collision avoidance), we need:
$$
\frac{\partial}{\partial k_i} \left( \|d_1 t - d_2 u - d_{12}\| - r_1 t - r_2 u \right)
$$

where $k_i$ represents any parameter (positions, orientations, etc.).

### Gradient Structure

The gradient expands via chain rule:
$$
\frac{\partial \text{SDF}}{\partial k_i} = \frac{(d_1 t - d_2 u - d_{12})^\top}{\|d_1 t - d_2 u - d_{12}\|} \left( \frac{\partial d_1}{\partial k_i}t + d_1\frac{\partial t}{\partial k_i} - \frac{\partial d_2}{\partial k_i}u - d_2\frac{\partial u}{\partial k_i} - \frac{\partial d_{12}}{\partial k_i} \right) - \left( \frac{\partial r_1}{\partial k_i}t + r_1\frac{\partial t}{\partial k_i} \right) - \left( \frac{\partial r_2}{\partial k_i}u + r_2\frac{\partial u}{\partial k_i} \right)
$$

### Classification of Derivatives

**Easy (direct computation):**
$$
\frac{\partial d_1}{\partial k_i}, \quad \frac{\partial d_2}{\partial k_i}, \quad \frac{\partial d_{12}}{\partial k_i}, \quad \frac{\partial r_1}{\partial k_i}, \quad \frac{\partial r_2}{\partial k_i}
$$

**Difficult (require implicit function theorem):**
$$
\frac{\partial t}{\partial k_i}, \quad \frac{\partial u}{\partial k_i}
$$

---

## Computing $\frac{\partial t}{\partial k_i}$ and $\frac{\partial u}{\partial k_i}$ via Implicit Function Theorem

### Approach: Sensitivity Analysis of KKT System

At the optimal solution, the KKT conditions define $t^*$ and $u^*$ implicitly as functions of parameters $k_i$. We can differentiate these conditions to find the sensitivities.

### Quadratic Program Formulation

For each case, we solve a quadratic program (QP) to find the sensitivities:

$$
\text{QP}(d): \quad \min_{w \in \mathbb{R}^2} \frac{1}{2} w^\top \begin{pmatrix} \frac{\partial^2 \mathcal{L}}{\partial t^2} & \frac{\partial^2 \mathcal{L}}{\partial t \partial u} \\ \frac{\partial^2 \mathcal{L}}{\partial t \partial u} & \frac{\partial^2 \mathcal{L}}{\partial u^2} \end{pmatrix} w + w^\top \begin{pmatrix} \frac{\partial^2 \mathcal{L}}{\partial t \partial k_i} \\ \frac{\partial^2 \mathcal{L}}{\partial u \partial k_i} \end{pmatrix}
$$

Subject to constraints depending on which bounds are active.

The solution gives:
$$
w = \begin{pmatrix} \frac{\partial t}{\partial k_i} \\ \frac{\partial u}{\partial k_i} \end{pmatrix}
$$

### Second Derivatives (Hessian Components)

Define:
$$
L_{11} = \frac{\partial^2 \mathcal{L}}{\partial t^2} = \frac{a\|d_1 t - d_2 u - d_{12}\| - (at - bu - c) \cdot \frac{at - bu - c}{\|d_1 t - d_2 u - d_{12}\|}}{\|d_1 t - d_2 u - d_{12}\|^2}
$$

$$
L_{22} = \frac{\partial^2 \mathcal{L}}{\partial u^2} = \frac{e\|d_1 t - d_2 u - d_{12}\| - (-bt + eu + f) \cdot \frac{-bt + eu + f}{\|d_1 t - d_2 u - d_{12}\|}}{\|d_1 t - d_2 u - d_{12}\|^2}
$$

$$
L_{12} = \frac{\partial^2 \mathcal{L}}{\partial t \partial u} = \frac{-b\|d_1 t - d_2 u - d_{12}\| - (at - bu - c) \cdot \frac{-bt + eu + f}{\|d_1 t - d_2 u - d_{12}\|}}{\|d_1 t - d_2 u - d_{12}\|^2}
$$

### Mixed Partial Derivatives

$$
H_1 = \frac{\partial^2 \mathcal{L}}{\partial t \partial k_i} = \frac{\left(\frac{\partial a}{\partial k_i}t - \frac{\partial b}{\partial k_i}u - \frac{\partial c}{\partial k_i}\right)\|d_1 t - d_2 u - d_{12}\| - (at - bu - c)\frac{\partial}{\partial k_i}\|d_1 t - d_2 u - d_{12}\|}{\|d_1 t - d_2 u - d_{12}\|^2} - \frac{\partial r_1}{\partial k_i} + \frac{\partial \lambda_1}{\partial k_i} - \frac{\partial \lambda_3}{\partial k_i}
$$

$$
H_2 = \frac{\partial^2 \mathcal{L}}{\partial u \partial k_i} = \frac{\left(-\frac{\partial b}{\partial k_i}t + \frac{\partial e}{\partial k_i}u + \frac{\partial f}{\partial k_i}\right)\|d_1 t - d_2 u - d_{12}\| - (-bt + eu + f)\frac{\partial}{\partial k_i}\|d_1 t - d_2 u - d_{12}\|}{\|d_1 t - d_2 u - d_{12}\|^2} - \frac{\partial r_2}{\partial k_i} + \frac{\partial \lambda_2}{\partial k_i} - \frac{\partial \lambda_4}{\partial k_i}
$$

---

## Solution Gradients Summary

| Case | $\frac{\partial t}{\partial k_i}$ | $\frac{\partial u}{\partial k_i}$ |
|------|-----------------------------------|-----------------------------------|
| 1 | $0$ | $\displaystyle\left(-\frac{H_2}{L_{22}}\right)\bigg\|_{t^*=0}$ |
| 2 | $0$ | $\displaystyle\left(-\frac{H_2}{L_{22}}\right)\bigg\|_{t^*=1}$ |
| 3 | $\displaystyle\left(-\frac{H_1}{L_{11}}\right)\bigg\|_{u^*=0}$ | $0$ |
| 4 | $\displaystyle\left(-\frac{H_1}{L_{11}}\right)\bigg\|_{u^*=1}$ | $0$ |
| 5 | $\displaystyle\frac{L_{12}H_2 - L_{22}H_1}{L_{11}L_{22} - L_{12}^2}$ | $\displaystyle\frac{L_{12}H_1 - L_{11}H_2}{L_{11}L_{22} - L_{12}^2}$ |

---

## Auxiliary Derivatives

### Norm Gradient with Respect to $t$ and $u$

$$
\frac{\partial}{\partial t}\|d_1 t - d_2 u - d_{12}\| = \frac{d_1^\top d_1 t - d_1^\top d_2 u - d_1^\top d_{12}}{\|d_1 t - d_2 u - d_{12}\|} = \frac{at - bu - c}{\|d_1 t - d_2 u - d_{12}\|}
$$

$$
\frac{\partial}{\partial u}\|d_1 t - d_2 u - d_{12}\| = \frac{-d_1^\top d_2 t + d_2^\top d_2 u + d_2^\top d_{12}}{\|d_1 t - d_2 u - d_{12}\|} = \frac{-bt + eu + f}{\|d_1 t - d_2 u - d_{12}\|}
$$

### Norm Gradient with Respect to Parameters $k_i$

$$
\frac{\partial}{\partial k_i}\|d_1 t - d_2 u - d_{12}\| = \frac{\frac{\partial d_1^\top}{\partial k_i}(d_1 t^2 - d_2 tu - d_{12}t) - \frac{\partial d_2^\top}{\partial k_i}(d_1 tu - d_2 u^2 - d_{12}u) - \frac{\partial d_{12}}{\partial k_i}(d_1 - d_2 - d_{12})}{\|d_1 t - d_2 u - d_{12}\| \cdot \|d_1 t - d_2 u - d_{12}\|}
$$

---

## Multiplier Gradients

For completeness, the gradients of the Lagrange multipliers are:

$$
\frac{\partial \lambda_1}{\partial k_i} = \frac{\partial r_1}{\partial k_i} - \frac{\frac{\partial}{\partial k_i}(at - bu - c)\|d_1 t - d_2 u - d_{12}\| - (at - bu - c)\frac{\partial}{\partial k_i}\|d_1 t - d_2 u - d_{12}\|}{\|d_1 t - d_2 u - d_{12}\|^2} \quad (t = 1)
$$

$$
\frac{\partial \lambda_2}{\partial k_i} = \frac{\partial r_2}{\partial k_i} - \left[\frac{\frac{\partial}{\partial k_i}(-bt + eu + f)\|d_1 t - d_2 u - d_{12}\| - (-bt + eu + f)\frac{\partial}{\partial k_i}\|d_1 t - d_2 u - d_{12}\|}{\|d_1 t - d_2 u - d_{12}\|^2}\right] \quad (u = 1)
$$

$$
\frac{\partial \lambda_3}{\partial k_i} = \frac{\left(\frac{\partial}{\partial k_i}at - \frac{\partial b}{\partial k_i}u - \frac{\partial c}{\partial k_i}\right)\|d_1 t - d_2 u - d_{12}\| - (at - bu - c)\frac{\partial}{\partial k_i}\|d_1 t - d_2 u - d_{12}\|}{\|d_1 t - d_2 u - d_{12}\|^2} - \frac{\partial r_1}{\partial k_i} \quad (t = 0)
$$

$$
\frac{\partial \lambda_4}{\partial k_i} = \frac{\left(-\frac{\partial b}{\partial k_i}t + \frac{\partial e}{\partial k_i}u + \frac{\partial f}{\partial k_i}\right)\|d_1 t - d_2 u - d_{12}\| - (-bt + eu + f)\frac{\partial}{\partial k_i}\|d_1 t - d_2 u - d_{12}\|}{\|d_1 t - d_2 u - d_{12}\|^2} - \frac{\partial r_2}{\partial k_i} \quad (u = 0)
$$

---

## Implementation Notes

### Case Selection Logic

1. Compute solutions for all 5 non-trivial cases
2. Check feasibility: $t \in [0, 1]$, $u \in [0, 1]$
3. Check dual feasibility: appropriate $\lambda_i \geq 0$
4. Among feasible solutions, select the one with minimum objective value
5. Also check the 4 corner cases as potential solutions

### Numerical Considerations

- **Degeneracy**: When $r_1 = r_2 = 0$ (line segments), the problem simplifies
- **Parallel capsules**: Special handling may be needed when $d_1 \parallel d_2$
- **Exactly overlapping**: When the tapered capsules are exactly overlapping, the constraint $\frac{\partial \mathcal{L}}{\partial u} = 0$ can become $0 = 0$
- **Sign of square root**: Choose the $\pm$ sign that gives a feasible solution

### Geometric Intuition

The minimum of a quadratic function subject to box constraints occurs either:
1. At the unconstrained minimum (if feasible)
2. On a face of the box (one constraint active)
3. At a corner (two constraints active)

This is illustrated by the parabola constrained to $x \leq x_0$: the minimum is either the global minimum or occurs at the constraint boundary.

---

## References

- Boyd & Vandenberghe, *Convex Optimization*, Chapter 5 (KKT Conditions)
- Nocedal & Wright, *Numerical Optimization*, Chapter 12 (Sensitivity Analysis)
- Ericson, *Real-Time Collision Detection*, Chapter 5 (Capsule-Capsule Distance)

---

## Status

- [x] Cases 1-4: Complete with solutions and gradients
- [ ] Case 5: Work in progress (solution derived, gradients need verification)
- [ ] Corner cases 6-9: Trivial (direct evaluation)
- [ ] Implementation and testing
