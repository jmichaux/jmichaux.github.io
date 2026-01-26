# A Treatise on the "Equivalence" between ARMTD and its Smooth Reformulation

## Preliminaries and Assumptions

- We can generate safe motion plans for a given agent by optimizing over a trajectory parameter $K$.
- We can represent the **Forward Occupancy** of the agent using trajectory parameter-dependent polynomial zonotopes, which we denote $FO(k)$.
- We can represent an obstacle by a convex polytope, denoted $(A, b)$, where $A$ does not contain any rows where each element is 0.
- The agent never intersects with any obstacles.
- The agent may reach its acceleration limits, which are the bounds on $K$.

---

## Symbols

| Symbol | Description |
|--------|-------------|
| $n$ | DOF of agent |
| $N$ | Number of obstacle avoidance constraints, where $N = n \times \text{(\# time steps)} \times \text{(\# obstacles)}$ |
| $(A_i, b_i)$ | Polytope corresponding to the $i$-th obstacle avoidance constraint, where $i \in \{1, \ldots, N\}$ |
| $m_i$ | Number of rows of matrix $A_i \in \mathbb{R}^{m_i \times n}$ |
| $FO_i$ | Forward Occupancy polyzonotope corresponding to the $i$-th obstacle avoidance constraint |
| $K$ | Trajectory parameter / decision variable of ARMTD |
| $u / \ell$ | Upper / lower bounds on $K$ |
| $\mu$ | Lagrange multiplier corresponding to obstacle avoidance constraint |
| $\sigma_1$ | Lagrange multiplier corresponding to lower bound constraint on $K$ |
| $\sigma_2$ | Lagrange multiplier corresponding to upper bound constraint on $K$ |
| $\lambda = (\lambda_1, \ldots, \lambda_N)^T$ | Decision variable in smoothed ARMTD (B-ARMTD), where $\lambda_i \in \mathbb{R}^{m_i}$ |
| $\sigma_3$ | Lagrange multiplier corresponding to the half-space constraint in B-ARMTD |
| $\sigma_4$ | Lagrange multiplier corresponding to lower bound constraint on $\lambda$ |

---

## Formulation of ARMTD

Following Holmes et al., we generate safe motion plans by solving the nonlinear program (NLP):

$$
\textbf{(A)} \quad \min_K f(K)
$$

subject to:
$$
\begin{aligned}
-\max(A_i \cdot FO_i(K) - b_i) &\leq 0 & i = 1, \ldots, N \\
\ell_j - K_j &\leq 0 & j = 1, \ldots, n \\
K_j - u_j &\leq 0 & j = 1, \ldots, n
\end{aligned}
$$

> **Note:** The $i$-th obstacle avoidance constraint $-\max(A_i \cdot FO_i(K) - b_i) \leq 0$ is **nonsmooth**.

---

## KKT Conditions for Problem (A)

The KKT conditions for (A) at $x_A^* = (K^*, \mu^*, \sigma_1^*, \sigma_2^*)$ are:

### Primal Feasibility
$$
\begin{aligned}
-\max(A_i \cdot FO_i(K^*) - b_i) &\leq 0 & i = 1, \ldots, N \\
\ell_j - K_j^* &\leq 0 & j = 1, \ldots, n \\
K_j^* - u_j &\leq 0 & j = 1, \ldots, n
\end{aligned}
$$

### Dual Feasibility
$$
\begin{aligned}
\mu_i^* &\geq 0 & i = 1, \ldots, N \\
\sigma_{1,j}^* &\geq 0 & j = 1, \ldots, n \\
\sigma_{2,j}^* &\geq 0 & j = 1, \ldots, n
\end{aligned}
$$

### Complementarity
$$
\begin{aligned}
-\mu_i^* \max(A_i \cdot FO_i(K^*) - b_i) &= 0 & i = 1, \ldots, N \\
\sigma_{1,j}^* (\ell_j - K_j^*) &= 0 & j = 1, \ldots, n \\
\sigma_{2,j}^* (K_j^* - u_j) &= 0 & j = 1, \ldots, n
\end{aligned}
$$

### Stationarity
$$
\nabla_K L(K_j^*, \mu^*, \sigma_1^*, \sigma_2^*) = \nabla_K f(K^*) - \sum_{i=1}^{N} \mu_i^* \nabla_K \max(A_i \cdot FO_i(K^*) - b_i) + \sigma_1^* - \sigma_2^* = 0
$$

---

## Proposition 1: LICQ for Problem (A)

> **Proposition 1.** Problem (A) satisfies LICQ for any feasible point $K$.

**Proof:** By assumption, the agent never intersects obstacles. That is, for any feasible point $K$, the constraint $-\max(A_i \cdot FO_i(K) - b_i) \leq 0$ is always **inactive** for all $i \in \{1, \ldots, N\}$.

Let $\mathcal{L} = \{j \mid \ell_j - K_j = 0\}$ and $\mathcal{U} = \{j \mid K_j - u_j = 0\}$ denote the set of indices corresponding to the active constraints on the lower and upper bounds on $K$, respectively.

Note that for each $j \in \{1, \ldots, n\}$, at most one of the constraints
$$
\ell_j - K_j \leq 0 \quad \text{and} \quad K_j - u_j \leq 0
$$
can be active.

Thus $\mathcal{L} \cap \mathcal{U} = \emptyset$.

Let
$$
d_j = \begin{cases}
1 & \text{if } j \in \mathcal{U} \\
-1 & \text{if } j \in \mathcal{L} \\
0 & \text{otherwise}
\end{cases}
$$

and note that $\nabla_K(K_j - u_j) = (0, \ldots, 1, \ldots, 0)^T$ and $\nabla_K(\ell_j - K_j) = (0, \ldots, -1, \ldots, 0)^T$.

Then the set of active constraint gradients for (A) is:
$$
G = \{d_j e_j \mid j \in \{1, \ldots, n\}, \, d_j \in \{1, 0, -1\}\}
$$
where $e_j$ is the $j$-th basis vector.

Thus if any of the constraints are active, the set of vectors in $G$ are **linearly independent**. Therefore problem (A) satisfies LICQ. $\square$

---

## Formulation of B-ARMTD

Following Borrelli et al., we use a smooth reformulation of the obstacle avoidance constraints to rewrite our NLP as:

$$
\textbf{(B)} \quad \min_{K, \lambda} f(K)
$$

subject to:
$$
\begin{aligned}
-(A_i \cdot FO_i(K) - b_i)^T \lambda_i &\leq 0 & i = 1, \ldots, N \\
\ell_j - K_j &\leq 0 & j = 1, \ldots, n \\
K_j - u_j &\leq 0 & j = 1, \ldots, n \\
\|A_i^T \lambda_i\|_* - 1 &\leq 0 & i = 1, \ldots, N \\
-\lambda_i &\leq 0 & i = 1, \ldots, N
\end{aligned}
$$

---

## KKT Conditions for Problem (B)

The KKT conditions for (B) at $x_B^* = (K^*, \lambda^*, \mu^*, \sigma_1^*, \sigma_2^*, \sigma_3^*, \sigma_4^*)$ are:

### Primal Feasibility
$$
\begin{aligned}
-(A_i \cdot FO_i(K^*) - b_i)^T \lambda_i^* &\leq 0 & i = 1, \ldots, N \\
K_j^* - u_j &\leq 0 & j = 1, \ldots, n \\
\ell_j - K_j^* &\leq 0 & j = 1, \ldots, n \\
\|A_i^T \lambda_i^*\|_* - 1 &\leq 0 & i = 1, \ldots, N \\
-\lambda_i^* &\leq 0 & i = 1, \ldots, N
\end{aligned}
$$

### Dual Feasibility
$$
\begin{aligned}
\mu_i^* &\geq 0 & i = 1, \ldots, N \\
\sigma_{1,j}^* &\geq 0 & j = 1, \ldots, n \\
\sigma_{2,j}^* &\geq 0 & j = 1, \ldots, n \\
\sigma_{3,i}^* &\geq 0 & i = 1, \ldots, N \\
\sigma_{4,i} &\geq 0 & i = 1, \ldots, N
\end{aligned}
$$

### Complementarity
$$
\begin{aligned}
-\mu_i^* (A_i \cdot FO_i(K^*) - b_i)^T \lambda_i^* &= 0 & i = 1, \ldots, N \\
\sigma_{1,j}^* (K_j^* - u_j) &= 0 & j = 1, \ldots, n \\
\sigma_{2,j}^* (\ell_j - K_j^*) &= 0 & j = 1, \ldots, n \\
\sigma_{3,i}^* (\|A_i^T \lambda_i^*\|_* - 1) &= 0 & i = 1, \ldots, N \\
-\sigma_{4,i}^* \odot \lambda_i^* &= 0 & i = 1, \ldots, N
\end{aligned}
$$

### Stationarity
$$
\nabla L(K^*, \lambda^*, \mu^*, \sigma_1^*, \sigma_2^*, \sigma_3^*, \sigma_4^*) = 
\begin{pmatrix}
\nabla_K f(K^*) \\
\nabla_\lambda f(K^*)
\end{pmatrix}
- \sum_{i=1}^{N} \mu_i^* \begin{pmatrix}
\nabla_K (A_i \cdot FO_i(K^*) - b_i)^T \lambda \\
(A_i \cdot FO_i(K^*) - b_i)
\end{pmatrix}
+ \sum_{i=1}^{N} \sigma_{3,i}^* \begin{pmatrix}
\nabla_K (\|A_i^T \lambda_i\|_* - 1) \\
\nabla_\lambda (\|A_i^T \lambda_i\|_* - 1)
\end{pmatrix}
$$
$$
+ \sum_{j=1}^{n} \sigma_{1,j}^* \begin{pmatrix}
\nabla_K (K_j - u_j) \\
\nabla_\lambda (K_j - u_j)
\end{pmatrix}
- \sum_{j=1}^{n} \sigma_{2,j}^* \begin{pmatrix}
\nabla_K (\ell_j - K_j) \\
\nabla_\lambda (\ell_j - K_j)
\end{pmatrix}
- \sum_{i=1}^{N} \begin{pmatrix}
\nabla_K (\sigma_{4,i}^* \odot \lambda_i) \\
\nabla_\lambda (\sigma_{4,i}^* \odot \lambda_i)
\end{pmatrix} = 0
$$

---

## Equivalence Results

### Proposition 2: Feasibility Transfer from (A) to (B)

> **Proposition 2.** Suppose $\tilde{K}$ is feasible for (A). Then there exists $\tilde{\lambda} \geq 0$ such that $(\tilde{K}, \tilde{\lambda})$ is feasible for (B).

**Proof:** Let $r_i \in \mathbb{Z}$ be the index of the max row of $(A_i \cdot FO_i(K) - b_i)$ and define $\tilde{\lambda}_i$ such that:

$$
\tilde{\lambda}_i = \frac{1}{2\|A_i^T\|} \begin{pmatrix} 0 \\ \vdots \\ 1 \\ \vdots \\ 0 \end{pmatrix}_{r_i} \geq 0
$$

Then the primal feasibility conditions for (B) at $(\tilde{K}, \tilde{\lambda})$ are:

$$
-(A_i \cdot FO_i(\tilde{K}) - b_i)^T \tilde{\lambda}_i = \frac{-\max(A_i \cdot FO_i(\tilde{K}) - b_i)}{2\|A_i^T\|} \leq 0 \quad i = 1, \ldots, N
$$

$$
\tilde{K}_j - u_j \leq 0 \quad j = 1, \ldots, n
$$

$$
\ell_j - \tilde{K}_j \leq 0 \quad j = 1, \ldots, n
$$

$$
\|A_i^T \tilde{\lambda}_i\|_* - 1 = \frac{\|A_i^T\|}{2\|A_i^T\|} - 1 = -\frac{1}{2} \leq 0 \quad i = 1, \ldots, N
$$

$$
-\tilde{\lambda}_i = \frac{-1}{2\|A_i^T\|} \begin{pmatrix} 0 \\ \vdots \\ 1 \\ \vdots \\ 0 \end{pmatrix}_{r_i} \leq 0 \quad i = 1, \ldots, N
$$

It is worth noting that the constraints $-(A_i \cdot FO_i(\tilde{K}) - b_i)^T \tilde{\lambda}_i$ and $\|A_i^T \tilde{\lambda}_i\|_* - 1$ are **inactive** for $(\tilde{K}, \tilde{\lambda})$. $\square$

---

### Proposition 3: Dual Feasibility Transfer

> **Proposition 3.** If $\tilde{x}_A = (\tilde{K}, \tilde{\mu}, \tilde{\sigma}_1, \tilde{\sigma}_2)$ is a KKT point of (A), then $\tilde{\mu} = 0$ and $(0, \tilde{\sigma}_1, \tilde{\sigma}_2, 0, 0)$ is dual feasible for (B).

**Proof:** $\tilde{\mu} = 0$ follows from complementary slackness. The point $(0, \tilde{\sigma}_1, \tilde{\sigma}_2, 0, 0)$ being dual feasible for (B) by definition. $\square$

---

### Proposition 4: Complementary Slackness Transfer

> **Proposition 4.** Suppose $\tilde{x}_A = (\tilde{K}, 0, \tilde{\sigma}_1, \tilde{\sigma}_2)$ satisfies the complementary condition for (A). Then $\tilde{x}_B = (\tilde{K}, \tilde{\lambda}, 0, \tilde{\sigma}_1, \tilde{\sigma}_2, 0, 0)$, where $\tilde{\lambda}$ is defined as in Proposition 2, satisfies the complementary slackness condition for the KKT conditions of (B).

**Proof:** The complementary conditions for (B) are:

$$
\begin{aligned}
-0 \cdot (A_i \cdot FO_i(K) - b_i)^T \lambda_i &= 0 & i = 1, \ldots, N & \quad (1) \\
\sigma_{1,j}^* (K_j - u_j) &= 0 & j = 1, \ldots, n & \quad (2) \\
\sigma_{2,j}^* (\ell_j - K_j) &= 0 & j = 1, \ldots, n & \quad (3) \\
0 \cdot (\|A_i^T \lambda_i\|_* - 1) &= 0 & i = 1, \ldots, N & \quad (4) \\
-0 \odot \lambda_i &= 0 & i = 1, \ldots, N & \quad (5)
\end{aligned}
$$

Note that (2) and (3) inherit complementary satisfaction from $\tilde{x}_A$. $\square$

---

### Proposition 5: Stationarity Transfer

> **Proposition 5.** The point $\tilde{x}_B = (\tilde{K}, \tilde{\lambda}, 0, \tilde{\sigma}_1, \tilde{\sigma}_2, 0, 0)$ satisfies the stationarity condition for (B).

**Proof:** The gradient of the Lagrangian of (B) at $\tilde{x}_B$ is:

$$
\nabla L(\tilde{K}, \tilde{\lambda}, 0, \tilde{\sigma}_1, \tilde{\sigma}_2, 0, 0) = 
\begin{pmatrix}
\nabla_K f(\tilde{K}) \\
\nabla_\lambda f(\tilde{K})
\end{pmatrix}
- \sum_{i=1}^{N} \mu_i \begin{pmatrix}
\nabla_K (A_i \cdot FO_i(\tilde{K}) - b_i)^T \lambda \\
(A_i \cdot FO_i(\tilde{K}) - b_i)
\end{pmatrix}
+ \ldots
$$

$$
= \begin{pmatrix}
\nabla_K f(\tilde{K}) \\
0
\end{pmatrix}
+ \sum_{j=1}^{n} \sigma_{1,j}^* \begin{pmatrix}
\nabla_K (K_j - u_j) \\
0
\end{pmatrix}
- \sum_{j=1}^{n} \sigma_{2,j}^* \begin{pmatrix}
\nabla_K (\ell_j - K_j) \\
0
\end{pmatrix}
$$

$$
= \begin{pmatrix}
\nabla_K f(\tilde{K}) \\
0
\end{pmatrix}
+ \begin{pmatrix}
\tilde{\sigma}_1 \\
0
\end{pmatrix}
- \begin{pmatrix}
\tilde{\sigma}_2 \\
0
\end{pmatrix}
= \begin{pmatrix}
0 \\
0
\end{pmatrix}
$$

by the stationarity condition of (A). Taken together, Propositions (2)-(5) show that $\tilde{x}_B = (\tilde{K}, \tilde{\lambda}, 0, \tilde{\sigma}_1, \tilde{\sigma}_2, 0, 0)$ is a KKT point for (B). $\square$

---

### Proposition 6: LICQ for Problem (B)

> **Proposition 6.** For a KKT point $\tilde{x}_B$ of (B), the set of active constraint gradients is LICQ.

**Proof:** TODO

---

## Deriving the QP

For B-ARMTD, consider the decision variable:
$$
\chi = \begin{pmatrix} K \\ \lambda \end{pmatrix}
$$

The Lagrangian is:
$$
L = f(K) - \sum_{i=1}^{N} \mu_i (A_i \cdot FO_i(K) - b_i)^T \lambda_i + \sum_{j=1}^{n} \sigma_{1,j} (\ell_j - K_j) + \sum_{j=1}^{n} \sigma_{2,j} (K_j - u_j)
$$
$$
+ \sum_{i=1}^{N} \sigma_{3,i} \odot (L_i - \lambda_i) + \sum_{i=1}^{N} \sigma_{4,i} \odot (\lambda_i - U_i)
$$

The gradient is:
$$
\nabla_\chi L = \begin{pmatrix}
\nabla_K f(K) \\
0_{M \times 1}
\end{pmatrix}
- \sum_{i=1}^{N} \mu_i \begin{pmatrix}
\nabla_K (A_i \cdot FO_i(K) - b_i)^T \lambda_i \\
A_i \cdot FO_i(K) - b_i
\end{pmatrix}
- \begin{pmatrix}
I_{n \times n} \sigma_1 \\
0_{M \times 1}
\end{pmatrix}
+ \begin{pmatrix}
I_{n \times n} \sigma_2 \\
0_{M \times 1}
\end{pmatrix}
- \begin{pmatrix}
0_{n \times 1} \\
I_{M \times M} \sigma_3
\end{pmatrix}
+ \begin{pmatrix}
0_{n \times 1} \\
I_{M \times M} \sigma_4
\end{pmatrix}
$$

Note: $\mu_i = 0$ by complementary slackness (obstacle constraints are inactive).

The Hessian simplifies to:
$$
\nabla_{\chi\chi}^2 L = \begin{pmatrix}
\nabla_{KK}^2 f(K) & 0_{n \times M} \\
0_{M \times n} & 0_{M \times M}
\end{pmatrix}
$$

---

## Deriving the Solution to the QP

### Simplifying the Objective Function

$$
f(\rho, K) = \|\dot{q}_0 + \ddot{q}_0 t_\rho + \frac{1}{2}(C_u + g_u \odot K) t_\rho^2 - \rho\|_2^2 + \alpha \|K\|^2
$$

$$
= \|\gamma + S \odot K - \rho\|^2 + \alpha \|K\|^2
$$

where:
- $\gamma = \dot{q}_0 + \ddot{q}_0 t_\rho + \frac{1}{2} C_u t_\rho^2$
- $S = \frac{1}{2} g_u t_\rho^2$

Thus:
$$
f(\rho, K) = \sum_{j=1}^{n} (\gamma_j + S_j K_j - \rho_j)^2 + \alpha K_j^2
$$

### Computing Gradients

$$
\frac{\partial f}{\partial K_j} = 2(\gamma_j + S_j K_j - \rho_j) \cdot S_j + 2\alpha K_j
$$

$$
\frac{\partial^2 f}{\partial K_j^2} = 2S_j^2 + 2\alpha
$$

$$
\frac{\partial^2 f}{\partial K_j \partial \rho_j} = -2S_j
$$

$$
\frac{\partial f}{\partial \lambda_i} = 0
$$

### Hessians for B-ARMTD

$$
\nabla_{KK}^2 L = 2(\text{diag}(S))^2 + \alpha I_{n \times n}
$$

$$
\nabla_{K\rho}^2 L = -2 \cdot \text{diag}(S)
$$

### Simplifying the QP Objective

Let $H = \nabla_{KK}^2 L$. Then:

$$
f = \nabla_{K\rho}^2 L \cdot d = \begin{pmatrix} -2 \cdot \text{diag}(S) \cdot d \\ 0 \end{pmatrix} = \begin{pmatrix} S_1 \\ 0 \end{pmatrix}
$$

The QP objective becomes:
$$
\text{QP Objective} = \frac{1}{2} (z_1^T, z_2^T) \begin{pmatrix} H_{11} & H_{12} \\ H_{21} & H_{22} \end{pmatrix} \begin{pmatrix} z_1 \\ z_2 \end{pmatrix} + (z_1^T, z_2^T) (-2 \cdot \text{diag}(S)) d
$$

$$
= \frac{1}{2} z_1^T H_{11} z_1 - z_1^T (2 \cdot \text{diag}(S) \cdot d)
$$

$$
= z_1^T \left( \frac{1}{2} H_{11} z_1 - (2 \cdot \text{diag}(S) \cdot d) \right)
$$

Taking the gradient of the objective:
$$
H_{11} z_1 - (2 \cdot \text{diag}(S) \cdot d) = 0
$$

$$
\Rightarrow z_1 = H_{11}^{-1} (2 \cdot \text{diag}(S) \cdot d)
$$

> **Note:** $z_1$ is not super well-defined without additional constraints.

---

## Solving the QP

The QP becomes:

$$
\text{QP}(d): \quad \min_{z} z_1^T \left( \frac{1}{2} H_{11} z_1 - (2 \cdot \text{diag}(S) \cdot d) \right)
$$

subject to:
$$
\begin{aligned}
J_x g_i(\bar{p}, \bar{x}) z &\leq 0 & \forall i \in \mathcal{A}^0 & \quad \text{(weakly active)} \\
J_x g_i(\bar{p}, \bar{x}) z &= 0 & \forall i \in \mathcal{A}^+ & \quad \text{(strongly active)}
\end{aligned}
$$

The Jacobian $J_x g_i(\bar{p}, \bar{x})$ has the form:
- For weakly active: $(0, A_\lambda) \begin{pmatrix} z_1 \\ z_2 \end{pmatrix}$ gives $A_\lambda z_2 \leq 0$
- For strongly active: $(A_K, 0) \begin{pmatrix} z_1 \\ z_2 \end{pmatrix}$ gives $A_K z_1 = 0$

So $z_2$ only needs to satisfy $A_\lambda z_2 \leq 0$, which is true if $z_2 = 0$.

Since $H_{11}$ is diagonal, the solution to the QP $z^*$ is given by:

$$
z_j^* = \begin{cases}
\displaystyle\frac{(2 \cdot \text{diag}(S) \cdot d)_j}{(H_{11})_{j,j}} & \text{if all constraints on } K_j \text{ are inactive} \\[2ex]
0 & \text{if any constraint on } K_j \text{ is active}
\end{cases}
$$

---

## Extended Formulation with Additional Constraints

For a more complete problem with velocity, position, and torque constraints:

$$
\textbf{(B)} \quad \min_{K, \lambda} f(K)
$$

subject to:

**Obstacle Avoidance:**
$$
\begin{aligned}
-(A_i \cdot FO_i(K) - b_i)^T \lambda_i &\leq 0 & i = 1, \ldots, N \\
\|A_i^T \lambda_i\|_* - 1 &\leq 0 & i = 1, \ldots, N \\
-\lambda_i &\leq 0 & i = 1, \ldots, N
\end{aligned}
$$

**Acceleration:**
$$
\begin{aligned}
\ell_j^K - K_j &\leq 0 & j = 1, \ldots, n \\
K_j - u_j^K &\leq 0 & j = 1, \ldots, n
\end{aligned}
$$

**Velocity:**
$$
\begin{aligned}
\ell_j^v - g_j(K_j) &\leq 0 & j = 1, \ldots, n \\
g_j(u_j) - K_j^v &\leq 0 & j = 1, \ldots, n
\end{aligned}
$$

**Position:**
$$
\begin{aligned}
\ell_j^p - g_j(K_j) &\leq 0 & j = 1, \ldots, n \\
g_j(K_j) - K_j^p &\leq 0 & j = 1, \ldots, n
\end{aligned}
$$

**Torque:**
$$
\begin{aligned}
\tau_j - u_j^\tau &\leq 0 & j = 1, \ldots, n \\
\ell_j^\tau - \tau_j &\leq 0 & j = 1, \ldots, n
\end{aligned}
$$

> **Note:** We don't satisfy the LICQ assumptions for these additional constraints.

---

## To Do

1. Grab gradients of strongly active constraints
2. Change heuristic and make sure I can grab weakly active constraints
3. Implement QP using Gurobi
4. Set up learning problem to never violate the constraints

---

## QP Sensitivity Analysis Framework

For direction $\bar{x}, \bar{y}$ is KKT:

$$
\text{QP}(g): \quad \min_w L(\bar{p}, \bar{x}, \bar{y}) + \langle \nabla_x L(\bar{p}, \bar{x}, \bar{y}), w \rangle + \frac{1}{2} \langle w, \nabla_{xx}^2 L(\bar{p}, \bar{x}, \bar{y}) w \rangle + \langle w, \nabla_{xp}^2 L(\bar{p}, \bar{x}, \bar{y}) g \rangle
$$

subject to:
$$
\begin{aligned}
g_i(\bar{p}, \bar{x}) + \langle \nabla_x g_i(\bar{p}, \bar{x}), w \rangle + \langle \nabla_p g_i(\bar{p}, \bar{x}), g \rangle &= 0 & \forall i \in \mathcal{A}^+ & \quad \text{(strongly active)} \\
g_i(\bar{p}, \bar{x}) + \langle \nabla_x g_i(\bar{p}, \bar{x}), w \rangle + \langle \nabla_p g_i(\bar{p}, \bar{x}), g \rangle &\leq 0 & \forall i \in \mathcal{A}^0 & \quad \text{(weakly active)} \\
g_i(\bar{p}, \bar{x}) + \langle \nabla_x g_i(\bar{p}, \bar{x}), w \rangle + \langle \nabla_p g_i(\bar{p}, \bar{x}), g \rangle &\text{ free} & \forall i \in \mathcal{A}^- \\
h_i(\bar{p}, \bar{x}) + \langle \nabla_x h_i(\bar{p}, \bar{x}), w \rangle + \langle \nabla_p h_i(\bar{p}, \bar{x}), g \rangle &= 0 & \forall i \in \mathcal{H}
\end{aligned}
$$

> **Note:** The scalar term doesn't affect optimization. $g_i(w)$, $h_i(w)$ and $u_i$ are zero for ARMTD.

---

## References

- Holmes et al. - ARMTD
- Borrelli et al. - Smooth reformulation of obstacle avoidance constraints
- Dontchev - Variational analysis results
