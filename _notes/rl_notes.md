# Reinforcement Learning Notes

## Table of Contents
1. [Expected Discounted Return](#1-expected-discounted-return)
2. [State Value Functions](#2-state-value-functions)
3. [Action Value Functions](#3-action-value-functions)
4. [Matrix-Vector Form of Bellman Equation](#4-matrix-vector-form-of-bellman-equation)
5. [Optimal State Values and Bellman Optimality Equation](#5-optimal-state-values-and-bellman-optimality-equation)
6. [Contraction Mapping Theorem](#6-contraction-mapping-theorem)
   - [Banach Spaces and the Bellman Operator](#65-banach-spaces-and-the-bellman-operator)
7. [Solving for Optimal Policies](#7-solving-for-optimal-policies)
8. [Dynamic Programming Algorithms](#8-dynamic-programming-algorithms)
   - [Value Iteration](#81-value-iteration)
   - [Policy Iteration](#82-policy-iteration)
   - [Truncated Policy Iteration](#83-truncated-policy-iteration)
9. [Monte Carlo Methods](#9-monte-carlo-methods)
10. [Temporal Difference Learning](#10-temporal-difference-learning)
11. [Policy Gradient Methods](#11-policy-gradient-methods)
    - [REINFORCE Algorithm](#111-reinforce-algorithm)

---

## 1. Expected Discounted Return

The **expected discounted return** $G_t$ represents the cumulative future reward from time $t$:

$$G_t = R_t + \gamma R_{t+1} + \gamma^2 R_{t+2} + \cdots = \sum_{k=0}^{\infty} \gamma^k R_{t+k+1}$$

This can be written recursively:

$$G_t = R_t + \gamma(R_{t+1} + \gamma R_{t+2} + \cdots) = R_t + \gamma G_{t+1}$$

where $\gamma \in [0,1)$ is the **discount factor**.

### Probability Decomposition Trick

For joint probabilities:

$$p(a,b,c) = p(a) \cdot p(b|a) \cdot p(c|a,b) = p(a) \cdot p(b,c|a)$$

---

## 2. State Value Functions

The **state value function** $V_\pi(s)$ is the expected return starting from state $s$ and following policy $\pi$:

$$V_\pi(s) = \mathbb{E}[G_t | S_t = s]$$

### Bellman Equation for State Values

$$V_\pi(s) = \mathbb{E}[R_t + \gamma G_{t+1} | S_t = s] = \mathbb{E}[R_t | S_t = s] + \gamma \cdot \mathbb{E}[G_{t+1} | S_t = s]$$

**Expanding the immediate reward term:**

$$\mathbb{E}[R_t | S_t = s] = \sum_r r \cdot p(r|s) = \sum_a \pi(a|s) \cdot \sum_r p(r|s,a) \cdot r$$

**Expanding the future reward term:**

Using the Markov property:

$$\mathbb{E}[G_{t+1} | S_t = s] = \sum_{s'} V_\pi(s') \cdot p(s'|s) = \sum_a \pi(a|s) \sum_{s'} p(s'|s,a) \cdot V_\pi(s')$$

### Complete Bellman Equation

Combining both terms:

$$V_\pi(s) = \sum_a \pi(a|s) \cdot \sum_r p(r|s,a) \cdot r + \gamma \sum_a \pi(a|s) \sum_{s'} p(s'|s,a) \cdot V_\pi(s')$$

$$\boxed{V_\pi(s) = \sum_a \pi(a|s) \sum_{s'} \sum_r p(s',r|s,a) \left[ r + \gamma V_\pi(s') \right]}$$

The inner sum $\sum_{s'} \sum_r p(s',r|s,a)[r + \gamma V_\pi(s')]$ is the **action value** $Q_\pi(s,a)$.

### Simplified Form (when reward depends only on $s'$)

If $p(r(s')|s,a) = p(s'|s,a)$:

$$V_\pi(s) = \sum_a \pi(a|s) \cdot \sum_{s'} p(s'|s,a) \left[ r(s') + \gamma V_\pi(s') \right]$$

---

## 3. Action Value Functions

The **action value function** (Q-function) is the expected return starting from state $s$, taking action $a$, then following policy $\pi$:

$$Q_\pi(s,a) = \mathbb{E}[G_t | S_t = s, A_t = a]$$

### Relationship Between $V_\pi$ and $Q_\pi$

$$V_\pi(s) = \mathbb{E}[G_t | S_t = s] = \sum_a \mathbb{E}[G_t | S_t = s, A_t = a] \cdot \pi(a|s) = \sum_a \pi(a|s) \cdot Q_\pi(s,a)$$

### Bellman Equation for Action Values

$$Q_\pi(s,a) = \sum_r p(r|s,a) \cdot r + \gamma \sum_{s'} p(s'|s,a) \cdot V_\pi(s')$$

Substituting $V_\pi(s') = \sum_{a'} \pi(a'|s') \cdot Q_\pi(s',a')$:

$$Q_\pi(s,a) = \sum_{s'} \sum_r p(s',r|s,a) \left[ r + \gamma V_\pi(s') \right]$$

$$Q_\pi(s,a) = \sum_{s'} \sum_r p(s',r|s,a) \left[ r + \gamma \sum_{a'} \pi(a'|s') \cdot Q_\pi(s',a') \right]$$

---

## 4. Matrix-Vector Form of Bellman Equation

For a finite state space, we can express the Bellman equation in matrix form.

### Setup

Let states be indexed $s_i$ with $i = 1, \ldots, n = |S|$. Define:

- $\mathbf{v}_\pi = [V_\pi(s_1), \ldots, V_\pi(s_n)]^T$ — state value vector
- $\mathbf{r}_\pi = [r_\pi(s_1), \ldots, r_\pi(s_n)]^T$ — expected immediate reward vector
- $\mathbf{P}_\pi \in \mathbb{R}^{n \times n}$ — transition matrix where $[\mathbf{P}_\pi]_{ij} = P_\pi(s_j | s_i)$

The transition probability under policy $\pi$:

$$P_\pi(s'|s) = \sum_a \pi(a|s) \cdot p(s'|s,a)$$

### Matrix Bellman Equation

$$\mathbf{v}_\pi = \mathbf{r}_\pi + \gamma \mathbf{P}_\pi \mathbf{v}_\pi$$

where:
- $\mathbf{r}_\pi$ is **known** (from the environment model)
- $\mathbf{P}_\pi$ is **known** (from the environment model)
- $\mathbf{v}_\pi$ is **unknown**

### Closed-Form Solution

Rearranging:

$$\mathbf{v}_\pi - \gamma \mathbf{P}_\pi \mathbf{v}_\pi = \mathbf{r}_\pi$$
$$(\mathbf{I} - \gamma \mathbf{P}_\pi) \mathbf{v}_\pi = \mathbf{r}_\pi$$
$$\mathbf{v}_\pi = (\mathbf{I} - \gamma \mathbf{P}_\pi)^{-1} \mathbf{r}_\pi$$

**Note:** $(\mathbf{I} - \gamma \mathbf{P}_\pi)$ is invertible when $0 \leq \gamma < 1$.

### Iterative Solution

The closed-form leads to an iterative algorithm:

$$\mathbf{v}_{k+1} = \mathbf{r}_\pi + \gamma \mathbf{P}_\pi \mathbf{v}_k$$

**Theorem:** $\mathbf{v}_k \to \mathbf{v}_\pi = (\mathbf{I} - \gamma \mathbf{P}_\pi)^{-1} \mathbf{r}_\pi$ as $k \to \infty$

**Proof sketch:** Let $\delta_k = \mathbf{v}_k - \mathbf{v}_\pi$. Then:

$$\delta_{k+1} = \gamma \mathbf{P}_\pi \delta_k = \gamma^2 \mathbf{P}_\pi^2 \delta_{k-1} = \cdots = \gamma^{k+1} \mathbf{P}_\pi^{k+1} \delta_0$$

Since $0 < \gamma < 1$ and $\|\mathbf{P}_\pi^k\| \leq 1$ (as $\mathbf{P}_\pi$ is a stochastic matrix), we have $\delta_k \to 0$ as $k \to \infty$.

---

## 5. Optimal State Values and Bellman Optimality Equation

**Goal of RL:** Find optimal policies.

### Optimal State Value

$$V^*(s) = \max_{\pi(s) \in \Pi(s)} \sum_a \pi(a|s) \left( \sum_r p(r|s,a) \cdot r + \gamma \sum_{s'} p(s'|s,a) \cdot V_\pi(s') \right)$$

### Bellman Optimality Equation (BOE)

$$V^*(s) = \max_{\pi(s) \in \Pi(s)} \sum_a \pi(a|s) \cdot Q^*(s,a)$$

**Key Questions:**
1. Does this equation have a solution?
2. Is the solution unique?
3. How do we solve it?
4. How is the solution related to optimal policies?

### Key Insight

$$\sum_a \pi(a|s) \cdot Q(s,a) \leq \sum_a \pi(a|s) \cdot \max_a Q(s,a) = \max_a Q(s,a)$$

The maximum is achieved when $\pi(a|s) = \begin{cases} 1 & a = a^* \\ 0 & a \neq a^* \end{cases}$

where $a^* = \arg\max_a Q(s,a)$.

**The optimal policy selects the action with the greatest $Q(s,a)$.**

### Matrix Form of BOE

$$\mathbf{v} = \max_\pi \left( \mathbf{r}_\pi + \gamma \mathbf{P}_\pi \mathbf{v} \right) \iff \mathbf{v} = f(\mathbf{v})$$

---

## 6. Contraction Mapping Theorem

### Definition

A function $f$ is a **contraction mapping** if there exists $\gamma \in (0,1)$ such that:

$$\|f(x_1) - f(x_2)\| \leq \gamma \|x_1 - x_2\| \quad \text{for all } x_1, x_2 \in \mathbb{R}^d$$

### Theorem 3.1: Properties of Contraction Mappings

If $f(x) = x$ and $f$ is a contraction mapping, then:

1. **Existence:** There exists a fixed point $x^*$ such that $f(x^*) = x^*$
2. **Uniqueness:** $x^*$ is unique
3. **Algorithm:** The iterative process $x_{k+1} = f(x_k)$ for $k = 0, 1, 2, \ldots$ converges: $x_k \to x^*$ exponentially fast as $k \to \infty$

### Proof Sketch

**Convergence:** We want to show that $\{x_k\}$ is a Cauchy sequence.

$$\|x_{k+1} - x_k\| = \|f(x_k) - f(x_{k-1})\| \leq \gamma \|x_k - x_{k-1}\| \leq \cdots \leq \gamma^k \|x_1 - x_0\|$$

For $m > n$:

$$\|x_m - x_n\| \leq \sum_{i=n}^{m-1} \|x_{i+1} - x_i\| \leq \frac{\gamma^n}{1-\gamma} \|x_1 - x_0\| < \varepsilon$$

for sufficiently large $n$. Thus $\{x_k\}$ is Cauchy and converges to some $x^*$.

**Fixed point:** $\|f(x^*) - x^*\| = \lim_{k \to \infty} \|x_{k+1} - x_k\| = 0$

**Uniqueness:** If $x^*$ and $x'$ are both fixed points:

$$\|x^* - x'\| = \|f(x^*) - f(x')\| \leq \gamma \|x^* - x'\| \implies \|x^* - x'\| = 0$$

since $0 < \gamma < 1$.

### Theorem 3.2: BOE is a Contraction

The function $f(\mathbf{v}) = \max_\pi (\mathbf{r}_\pi + \gamma \mathbf{P}_\pi \mathbf{v})$ is a contraction mapping.

For any $\mathbf{v}_1, \mathbf{v}_2 \in \mathbb{R}^{|S|}$:

$$\|f(\mathbf{v}_1) - f(\mathbf{v}_2)\|_\infty \leq \gamma \|\mathbf{v}_1 - \mathbf{v}_2\|_\infty$$

---

## 6.5 Banach Spaces and the Bellman Operator

### Normed Vector Spaces

For a finite MDP with state space $S$, action space $A$, transition probabilities $p(s'|s,a)$, expected rewards $\mathbb{E}[r|s,a,s']$, and discount factor $\gamma$, we can think of the value function as a vector in $\mathbb{R}^{|S|}$.

**Definition (Norm):** We define a norm on the space of value functions:

$$\|V\| = \sup_{s \in S} |V(s)|$$

**Properties of norms:**
- $\|x\| = 0 \iff x = 0$
- $\|\alpha x\| = |\alpha| \cdot \|x\|$
- $\|x + y\| \leq \|x\| + \|y\|$ (triangle inequality)

### Cauchy Sequences and Complete Spaces

**Definition (Cauchy Sequence):** A sequence $\{x_n\}$ is a **Cauchy sequence** if:

$$\forall \varepsilon > 0, \exists N \in \mathbb{N} \text{ s.t. } \|x_m - x_n\| < \varepsilon \quad \forall m, n > N$$

**Definition (Complete/Banach Space):** If every Cauchy sequence in a normed vector space converges to a point in that space, we call the space **complete** or a **Banach space**.

The space $V$ of all value functions $V: S \to \mathbb{R}$ with the sup norm is a complete normed vector space (Banach space).

### The Bellman Operator

**Definition:** The **Bellman operator** $L_\pi: V \to V$ is defined as:

$$L_\pi(V) = \mathbf{r}_\pi + \gamma \mathbf{P}_\pi V$$

where $V$ is the space of all value functions.

**Key Properties:**
- $L_\pi$ is a **linear operator**
- $\mathbf{r}_\pi \in \mathbb{R}^{|S|}$ is the expected immediate reward vector
- $\mathbf{P}_\pi \in \mathbb{R}^{|S| \times |S|}$ is a **stochastic matrix** (each row is a probability distribution)

**Stochastic Matrix Properties:**
- Each row sums to 1
- All elements are non-negative: $0 \leq [\mathbf{P}_\pi]_{ij} \leq 1$
- The largest eigenvalue of a stochastic matrix is 1

### The Bellman Equation as a Fixed Point

The Bellman equation $L_\pi V^\pi = V^\pi$ tells us that $V^\pi$ is a **fixed point** of $L_\pi$.

### Banach Fixed-Point Theorem

**Theorem:** Suppose $U$ is a Banach space and $T: U \to U$ is a contraction mapping. Then:
1. There exists a unique $V^* \in U$ such that $TV^* = V^*$
2. For arbitrary $V^0 \in U$, the sequence $\{V^n\}$ defined by $V^{n+1} = TV^n = T^{n+1}V^0$ converges to $V^*$

**Properties to Verify:**
1. $TV^* = V^*$ for some $V^* \in U$ (existence of fixed point)
2. For some arbitrary $V^0 \in U$, the sequence $V^{n+1} = TV^n$ converges to $V^*$ (convergence)

### Proving $L_\pi$ is a Contraction

**Theorem:** $L_\pi$ is a contraction mapping with contraction factor $\gamma$.

**Proof:**

We need to show: $\|L_\pi u - L_\pi v\| \leq \gamma \|u - v\|$ for any $u, v \in V$ and $0 \leq \gamma < 1$.

Let $u, v \in V$. Then:

$$L_\pi u(s) = r_\pi(s) + \sum_{j \in S} \gamma P_\pi(j|s) \cdot u(j)$$

$$L_\pi v(s) = r_\pi(s) + \sum_{j \in S} \gamma P_\pi(j|s) \cdot v(j)$$

**Case 1:** Assume $L_\pi v(s) \geq L_\pi u(s)$

$$0 \leq L_\pi v(s) - L_\pi u(s) = \sum_{j \in S} \gamma P_\pi(j|s) [v(j) - u(j)]$$

$$\leq \gamma \|v - u\| \sum_{j \in S} P_\pi(j|s) = \gamma \|v - u\|$$

(since $\sum_j P_\pi(j|s) = 1$ for a stochastic matrix)

**Case 2:** By symmetry, if $L_\pi u(s) > L_\pi v(s)$:

$$|L_\pi v(s) - L_\pi u(s)| \leq \gamma \|v - u\| \quad \forall s$$

Taking the supremum over all states:

$$\|L_\pi v - L_\pi u\| = \sup_s |L_\pi v(s) - L_\pi u(s)| \leq \gamma \|v - u\|$$

Therefore, $L_\pi$ is a contraction mapping. $\square$

### Proof of Convergence (Banach Fixed-Point Theorem)

**Goal:** Show that $\{V^n\}$ is a Cauchy sequence.

$$\|V^{n+m} - V^n\| \leq \|V^{n+m} - V^{n+\frac{m}{2}}\| + \|V^{n+\frac{m}{2}} - V^n\|$$

By the triangle inequality and contraction property:

$$\|V^{n+m} - V^n\| \leq \sum_{k=0}^{m-1} \|V^{n+k+1} - V^{n+k}\|$$

$$= \sum_{k=0}^{m-1} \|T^{n+k}V^1 - T^{n+k}V^0\|$$

$$\leq \sum_{k=0}^{m-1} \gamma^{n+k} \|V^1 - V^0\|$$

$$= \gamma^n \|V^1 - V^0\| \sum_{k=0}^{m-1} \gamma^k$$

$$= \gamma^n \|V^1 - V^0\| \cdot \frac{1 - \gamma^m}{1 - \gamma}$$

$$\leq \frac{\gamma^n}{1-\gamma} \|V^1 - V^0\| \to 0 \text{ as } m, n \to \infty$$

Therefore $\{V^n\}$ is a Cauchy sequence. Since we're in a Banach space, it converges to some $V^*$.

**Proving $V^*$ is a fixed point:**

$$0 \leq \|TV^* - V^*\| \leq \|TV^* - V^n\| + \|V^n - V^*\|$$
$$= \|TV^* - TV^{n-1}\| + \|V^n - V^*\|$$
$$\leq \gamma \|V^* - V^{n-1}\| + \|V^n - V^*\| \to 0$$

Thus $V^*$ is a fixed point.

**Proving uniqueness:**

Suppose $V^*$ and $u^*$ are both fixed points:

$$0 \leq \|V^* - u^*\| = \|TV^* - Tu^*\| \leq \gamma \|V^* - u^*\|$$

Since $\gamma \neq 1$, we have $V^* = u^*$. $\square$

### The Optimal Bellman Operator

For the optimal value function, define $L^*: V \to V$ as:

$$L^* V^* = \max_a \left\{ \mathbb{E}[r|s,a] + \gamma \sum_j p(j|s,a) V^*(j) \right\}$$

**Claim:** $L^*$ is also a contraction.

**Proof sketch:** Let $a^*(s) \in \arg\max_a \{\mathbb{E}[r|s,a] + \gamma \sum_j p(j|s,a) V(j)\}$

$$0 \leq L^* V(s) - L^* u(s)$$

$$\leq \mathbb{E}[r|s,a^*] + \gamma \sum_j p(j|s,a^*) V(j) - \mathbb{E}[r|s,a^*] - \gamma \sum_j p(j|s,a^*) u(j)$$

$$= \gamma \sum_j p(j|s,a^*)[V(j) - u(j)]$$

$$\leq \gamma \|V - u\|$$

Therefore $\|L^* V - L^* u\| \leq \gamma \|V - u\|$, so $L^*$ is a contraction. $\square$

where $\|\cdot\|_\infty$ is the max norm.

---

## 7. Solving for Optimal Policies

### Theorem 3.3

For the BOE $\mathbf{v} = f(\mathbf{v}) = \max_\pi (\mathbf{r}_\pi + \gamma \mathbf{P}_\pi \mathbf{v})$:

- There is always a **unique solution** $\mathbf{v}^*$
- It can be solved iteratively: $\mathbf{v}_{k+1} = \max_\pi (\mathbf{r}_\pi + \gamma \mathbf{P}_\pi \mathbf{v}_k)$
- The sequence $\{\mathbf{v}_k\}$ converges exponentially fast to $\mathbf{v}^*$ for any initial $\mathbf{v}_0$

### Solving for $\pi^*$

$$\pi^* = \arg\max_\pi (\mathbf{r}_\pi + \gamma \mathbf{P}_\pi \mathbf{v}^*)$$

### Theorem 3.4: Optimality of $\mathbf{v}^*$

The solution $\mathbf{v}^*$ is the optimal state value, and $\pi^*$ is an optimal policy:

$$\mathbf{v}^* = V_{\pi^*} \geq V_\pi \quad \text{(element-wise, for any policy } \pi \text{)}$$

### Theorem 3.5: Greedy Optimal Policy

For any $s \in S$, the deterministic greedy policy:

$$\pi^*(a|s) = \begin{cases} 1 & a = a^*(s) \\ 0 & a \neq a^*(s) \end{cases}$$

is an optimal policy, where:

$$a^*(s) = \arg\max_a Q^*(s,a)$$

and

$$Q^*(s,a) = \sum_r p(r|s,a) \cdot r + \gamma \sum_{s'} p(s'|s,a) \cdot V^*(s')$$

---

## 8. Dynamic Programming Algorithms

Three main algorithms for finding optimal policies:
1. **Value Iteration**
2. **Policy Iteration**
3. **Truncated Policy Iteration**

### 8.1 Value Iteration

A method for computing the optimal state value and optimal policy by applying the contraction mapping theorem to the Bellman optimality equation.

**Key Properties (from contraction mapping theorem):**
- $\mathbf{v}^*$ exists
- $\mathbf{v}^*$ is unique
- $\mathbf{v}^k \to \mathbf{v}^*$ exponentially fast

**Algorithm Steps:**

1. **Policy Update:**
   $$\pi_{k+1} = \arg\max_\pi (\mathbf{r}_\pi + \gamma \mathbf{P}_\pi \mathbf{v}_k)$$

2. **Value Update:**
   $$\mathbf{v}_{k+1} = \mathbf{r}_{\pi_{k+1}} + \gamma \mathbf{P}_{\pi_{k+1}} \mathbf{v}_k$$

**Element-wise Form:**

Policy update (greedy policy):
$$\pi_{k+1}(s) = \arg\max_\pi \sum_a \pi(a|s) \left( \sum_r p(r|s,a) \cdot r + \gamma \sum_{s'} p(s'|s,a) \cdot V_k(s') \right)$$

Value update:
$$V_{k+1}(s) = \sum_a \pi_{k+1}(a|s) \cdot Q_k(s,a) = \max_a Q_k(s,a)$$

where:
$$Q_k(s,a) = \sum_r p(r|s,a) \cdot r + \gamma \sum_{s'} p(s'|s,a) \cdot V_k(s')$$

**Algorithm Pseudocode:**

```
Initialize: p(r|s,a), p(s'|s,a), v_0, ε
k = 0
diff = large value

while diff > ε:
    for every state s ∈ S:
        for every action a ∈ A:
            Q_k(s,a) = Σ_r p(r|s,a)·r + γ Σ_{s'} p(s'|s,a)·V_k(s')
        
        a*_k(s) = argmax_a Q_k(s,a)
        
        # Policy update
        π_{k+1}(a|s) = 1 if a = a*_k, else 0
        
        # Value update
        V_{k+1}(s) = max_a Q_k(s,a)
    
    diff = ||V_{k+1} - V_k||
    k = k + 1
```

**Note:** $V_k$ is **not** a state value and not guaranteed to solve the Bellman equation for any policy.

---

### 8.2 Policy Iteration

Unlike value iteration, policy iteration does **not** directly solve the Bellman optimality equation.

**Two Steps:**

1. **Policy Evaluation:** Solve $V_{\pi_k} = \mathbf{r}_{\pi_k} + \gamma \mathbf{P}_{\pi_k} V_{\pi_k}$ (Bellman equation)

2. **Policy Improvement:** $\pi_{k+1} = \arg\max_\pi (\mathbf{r}_\pi + \gamma \mathbf{P}_\pi V_{\pi_k})$

**Note:** $\mathbf{r}_{\pi_k}$ and $\mathbf{P}_{\pi_k}$ are obtained from the system model.

#### How to Solve $V_{\pi_k}$?

**Method 1 (Direct):** $V_{\pi_k} = (\mathbf{I} - \gamma \mathbf{P}_{\pi_k})^{-1} \mathbf{r}_{\pi_k}$ — inefficient for large state spaces

**Method 2 (Iterative):**
$$V_{\pi_k}^{(j+1)} = \mathbf{r}_{\pi_k} + \gamma \mathbf{P}_{\pi_k} V_{\pi_k}^{(j)}$$

where $V_{\pi_k}^{(j)}$ is the $j$-th estimate of $V_{\pi_k}$ under policy $\pi_k$.

Terminate when $\|V_{\pi_k}^{(j+1)} - V_{\pi_k}^{(j)}\| \leq \text{threshold}$.

#### Lemma 4.1: Policy Improvement

If $\pi_{k+1} = \arg\max_\pi (\mathbf{r}_\pi + \gamma \mathbf{P}_\pi V_{\pi_k})$, then $V_{\pi_{k+1}} \geq V_{\pi_k}$.

**Proof:**

Since $V_{\pi_{k+1}}$ and $V_{\pi_k}$ are state values, they satisfy the Bellman equations:
- $V_{\pi_{k+1}} = \mathbf{r}_{\pi_{k+1}} + \gamma \mathbf{P}_{\pi_{k+1}} V_{\pi_{k+1}}$
- $V_{\pi_k} = \mathbf{r}_{\pi_k} + \gamma \mathbf{P}_{\pi_k} V_{\pi_k}$

Because $\pi_{k+1} = \arg\max_\pi (\mathbf{r}_\pi + \gamma \mathbf{P}_\pi V_{\pi_k})$:

$$\mathbf{r}_{\pi_{k+1}} + \gamma \mathbf{P}_{\pi_{k+1}} V_{\pi_k} \geq \mathbf{r}_{\pi_k} + \gamma \mathbf{P}_{\pi_k} V_{\pi_k}$$

Then:

$$V_{\pi_k} - V_{\pi_{k+1}} = (\mathbf{r}_{\pi_k} + \gamma \mathbf{P}_{\pi_k} V_{\pi_k}) - (\mathbf{r}_{\pi_{k+1}} + \gamma \mathbf{P}_{\pi_{k+1}} V_{\pi_{k+1}})$$
$$\leq \gamma \mathbf{P}_{\pi_{k+1}} (V_{\pi_k} - V_{\pi_{k+1}})$$
$$\leq \gamma^n \mathbf{P}_{\pi_{k+1}}^n (V_{\pi_k} - V_{\pi_{k+1}})$$
$$\leq \lim_{n \to \infty} \gamma^n \mathbf{P}_{\pi_{k+1}}^n (V_{\pi_k} - V_{\pi_{k+1}}) = 0$$

Since $0 < \gamma < 1$ and $\mathbf{P}_{\pi_{k+1}}$ is stochastic, we have $V_{\pi_{k+1}} \geq V_{\pi_k}$.

#### Theorem 4.1: Convergence of Policy Iteration

The state value sequence $\{V_{\pi_k}\}_{k=0}^\infty$ generated by policy iteration converges to the optimal state value $\mathbf{v}^*$. As a result, $\{\pi_k\}_{k=0}^\infty$ converges to an optimal policy.

---

### 8.3 Truncated Policy Iteration

**Value iteration and policy iteration are extreme forms of truncated policy iteration.**

| Algorithm | Policy Evaluation Steps |
|-----------|------------------------|
| Value Iteration | 1 step ($j=1$) |
| Truncated Policy Iteration | $j$ steps |
| Policy Iteration | $\infty$ steps (until convergence) |

**Truncated Policy Iteration:**

$$V_{\pi_i}^{(j)} = \mathbf{r}_{\pi_i} + \gamma \mathbf{P}_{\pi_i} V_{\pi_i}^{(j-1)}$$

with $V_{\pi_i}^{(0)} = V_{\pi_{i-1}}$ (the previous policy's value).

#### Proposition 4.1: Value Improvement

Consider the policy evaluation step: $V_{\pi_k}^{(j+1)} = \mathbf{r}_{\pi_k} + \gamma \mathbf{P}_{\pi_k} V_{\pi_k}^{(j)}$

If $V_{\pi_k}^{(0)} = V_{\pi_{k-1}}$, then:

$$V_{\pi_k}^{(j+1)} \geq V_{\pi_k}^{(j)}$$

**Proof:**

$$V_{\pi_k}^{(j+1)} - V_{\pi_k}^{(j)} = \gamma \mathbf{P}_{\pi_k} (V_{\pi_k}^{(j)} - V_{\pi_k}^{(j-1)})$$
$$= \gamma^j \mathbf{P}_{\pi_k}^j (V_{\pi_k}^{(1)} - V_{\pi_k}^{(0)}) \geq 0$$

The last inequality follows from:
$$V_{\pi_k}^{(1)} = \mathbf{r}_{\pi_k} + \gamma \mathbf{P}_{\pi_k} V_{\pi_{k-1}} \geq \mathbf{r}_{\pi_{k-1}} + \gamma \mathbf{P}_{\pi_{k-1}} V_{\pi_{k-1}} = V_{\pi_{k-1}} = V_{\pi_k}^{(0)}$$

---

## 9. Monte Carlo Methods

**Key Idea:** Convert from model-based to model-free learning.

### Model-Free Prediction

**What if we don't have environment dynamics?**

- **Monte Carlo:** Estimates from entire trajectory
- **Temporal Difference:** One step look-ahead
- **TD(λ):** Unifies Monte Carlo and TD

### From Model-Based to Model-Free

In the model-based setting:
$$\pi_{k+1}(s) = \arg\max_\pi \sum_a \pi(a|s) \left( \sum_r p(r|s,a) \cdot r + \gamma \sum_{s'} p(s'|s,a) \cdot V_{\pi_k}(s') \right)$$

$$= \arg\max_\pi \sum_a \pi(a|s) \cdot Q_{\pi_k}(s,a)$$

The action value:
$$Q_{\pi_k}(s,a) = \sum_r p(r|s,a) \cdot r + \gamma \sum_{s'} p(s'|s,a) \cdot V_{\pi_k}(s')$$

can be rewritten as:
$$Q_{\pi_k}(s,a) = \mathbb{E}_{\pi_k}[G_t | S_t = s, A_t = a]$$

where $V_{\pi_k}(s') = \sum_{a'} \pi_k(a'|s') \cdot Q_{\pi_k}(s', a')$.

### MC Methods: Key Properties

- Learn directly from experience
- Good for **episodic tasks** (need episodes to terminate)
- Take sample returns to estimate value functions

### Monte Carlo Policy Evaluation

**Goal:** Learn $V_\pi$ from episodes of experience under policy $\pi$:

$$S_1, A_1, R_1, S_2, A_2, R_3, \ldots, S_K \sim \pi$$

**Recall - Return:**

$$G_t = R_{t+1} + \gamma R_{t+2} + \cdots + \gamma^{T-1} R_T$$

**Value Function:**

$$V_\pi(s) = \mathbb{E}_\pi[G_t | S_t = s]$$

MC policy evaluation uses **empirical mean** instead of expected return:
- Collect many samples starting from each state
- Average the returns

### First-Visit vs Every-Visit MC

**How do we get the mean for all states from trajectories?**

Consider an episode (trajectory):

```
S* ──────► S ──────► ...
     ╲
      ╲──► S ──► ... 
```

**Two approaches:**

| Method | Description |
|--------|-------------|
| **First-Visit MC** | Use only the **first** return from each state in an episode |
| **Every-Visit MC** | Use **all** returns from each state in an episode |

Both converge: $V(s) \to V_\pi(s)$ as $N(s) \to \infty$

### MC Algorithm

**Data structures:**
- $N$: array of length $|S|$ (visit counts)
- $V$: array of length $|S|$ (value estimates)

**For each state $S_t$ in episode with return $G_t$:**
1. $N(S_t) \leftarrow N(S_t) + 1$
2. $V(S_t) \leftarrow V(S_t) + \frac{1}{N(S_t)}(G_t - V(S_t))$

### Incremental Mean Update

The update $V(S_t) \leftarrow V(S_t) + \frac{1}{N(S_t)}(G_t - V(S_t))$ comes from the **incremental mean formula**:

$$\mu_k = \frac{1}{k} \sum_{j=1}^{k} x_j$$

$$= \frac{1}{k} \left( x_k + \sum_{j=1}^{k-1} x_j \right)$$

$$= \frac{1}{k} \left( x_k + (k-1) \mu_{k-1} \right)$$

$$= \mu_{k-1} + \frac{1}{k}(x_k - \mu_{k-1})$$

The term $(x_k - \mu_{k-1})$ corrects the old mean.

### Incremental MC for Non-Stationary Problems

For non-stationary problems, use a **constant step-size** $\alpha$:

$$V(S_t) \leftarrow V(S_t) + \alpha (G_t - V(S_t))$$

This forgets old data, adapting to changing environments.

### Monte Carlo Estimation Summary

Instead of computing expectations exactly (which requires the model), we **estimate** them from samples:

$$Q_{\pi_k}(s,a) \approx \frac{1}{n} \sum_{i=1}^{n} G_{\pi_k}^{(i)}(s,a) \quad \text{(Return estimate)}$$

where $G_{\pi_k}^{(i)}(s,a)$ is the return from the $i$-th episode starting from state $s$ with action $a$.

---

## 10. Temporal Difference Learning

**Key Properties:**
- Learns from episodes (like MC)
- Model-free
- Can learn from **incomplete episodes**
- Updates towards a **guess** (bootstrapping)

### TD vs MC

| Feature | MC | TD |
|---------|-----|-----|
| Updates | After episode ends | After each step |
| Requires | Complete episodes | Incomplete sequences OK |
| Works for | Episodic (terminating) tasks | Continuing (non-terminating) tasks |

### Goal: Learn $V_\pi$ (Policy Evaluation)

TD updates toward the **correct value function** $V_\pi$.

### Review of Returns

$$G_t = R_{t+1} + \gamma R_{t+2} + \gamma^2 R_{t+3} + \cdots = \sum_{k=0}^{\infty} \gamma^k R_{t+k+1}$$

$$= R_{t+1} + \gamma(R_{t+2} + \gamma R_{t+3} + \cdots) = R_{t+1} + \gamma G_{t+1}$$

### State Value as Expectation

$$V_\pi(s) = \mathbb{E}_\pi[G_{t+1} | S_t = s]$$
$$= \mathbb{E}_\pi[R_{t+1} + \gamma G_{t+1} | S_t = s]$$
$$= \mathbb{E}_\pi[R_{t+1} | S_t = s] + \gamma \mathbb{E}_\pi[G_{t+1} | S_t = s]$$

### Key TD Identity

$$V_\pi(s) = \mathbb{E}_\pi[R_{t+1} | S_t = s] + \gamma \mathbb{E}_\pi[V_\pi(S_{t+1}) | S_t = s]$$

$$= \mathbb{E}_\pi[R_{t+1} + \gamma V_\pi(S_{t+1}) | S_t = s]$$

### Incremental Every-Visit MC

**Update $V(s)$ after episode toward actual return $G_t$:**

$$V(S_t) \leftarrow V(S_t) + \alpha (G_t - V(S_t))$$

### TD(0) Algorithm (Simplest TD Algorithm)

**Update $V(S_t)$ toward estimated return $R_{t+1} + \gamma V(S_{t+1})$:**

$$\boxed{V(S_t) \leftarrow V(S_t) + \alpha \left( R_{t+1} + \gamma V(S_{t+1}) - V(S_t) \right)}$$

This is like the Bellman equation, but **biased** (uses current estimate $V(S_{t+1})$).

### TD Target and TD Error

**TD Target:**
$$R_{t+1} + \gamma V(S_{t+1})$$

This is the estimated return using one-step look-ahead.

**TD Error:**
$$\delta_t = R_{t+1} + \gamma V(S_{t+1}) - V(S_t)$$

The TD error measures the difference between the estimated return and the current value estimate.

### Advantages of TD over MC

| Advantage | Explanation |
|-----------|-------------|
| **Online learning** | TD updates after each step; MC waits until episode ends |
| **Works with incomplete sequences** | TD can learn from partial episodes |
| **Works for continuing tasks** | TD handles non-terminating environments; MC requires episodes to end |
| **Lower variance** | TD targets have lower variance (but may be biased) |

### Comparison Summary

| Method | Update Target | When to Update |
|--------|---------------|----------------|
| MC | Actual return $G_t$ | After episode ends |
| TD(0) | $R_{t+1} + \gamma V(S_{t+1})$ | After each step |

### Derivation Details

Using probability decomposition:
$$p(g|s) = \sum_r p(g, s'|s)$$

$$p(g, s', s) = p(s) \cdot p(g, s'|s) = p(s, s') \cdot p(g|s, s') = p(s) \cdot p(s'|s) \cdot p(g|s, s')$$

The expectation of the return given current state:

$$\mathbb{E}[G_{t+1} | S_t = s] = \sum_a p(a|s) \sum_r p(r|s,a) \cdot r + \gamma \sum_{s'} V_\pi(s') \cdot p(s'|s)$$

After expanding and rearranging using the Markov property:

$$V_\pi(s) = \sum_a \pi(a|s) \left[ \sum_r p(r|s,a) \cdot r + \gamma \sum_{s'} V_\pi(s') \cdot p(s'|s,a) \right]$$

$$= \sum_a \pi(a|s) \cdot Q_\pi(s,a)$$

---

## 11. Policy Gradient Methods

The methods covered so far (DP, MC, TD) are **value-based**: they first estimate value functions and then derive policies from them. **Policy gradient methods** take a different approach by directly optimizing the policy itself.

### 11.1 REINFORCE Algorithm

#### Motivation

In deriving the **REINFORCE** algorithm, we seek the optimal policy $\pi^{*}$ that will maximize the total expected reward:

$$\pi^{\ast} = \underset{\pi}{\operatorname{argmax}} \mathbb{E}_{\tau \sim p_{\theta}(\tau)}[R(\tau)]$$

where

$$\tau = (s_1, a_1, ..., s_T, a_T)$$

$$R(\tau) = \sum_{t=1}^{T}r(s_t, a_t)$$

$$p_{\theta}(\tau) = p_{\theta}(s_1, a_1, ..., s_T, a_T)$$

The **trajectory** $\tau$ is a sequence of states and actions experienced by the agent, $R(\tau)$ is the **return**, and $p_{\theta}(\tau)$ is the probability of observing that particular sequence of states and actions. It is important to note that $p_{\theta}(\tau)$ is a function of both the environment transition dynamics and the policy $\pi$.

#### On-Policy Derivation

Since the policy $\pi$ is parameterized by $\theta$, the above is equivalent to finding the optimal set of parameters:

$$\theta^{\ast} = \underset{\theta}{\operatorname{argmax}} \mathbb{E}_{\tau \sim p_{\theta}(\tau)}[R(\tau)]$$

Thus, we can define our objective $J(\theta)$ to be the total expected reward:

$$J(\theta) = \mathbb{E}_{\tau \sim p_{\theta}(\tau)}[R(\tau)]$$

One way to optimize this objective is to take the derivative and then use gradient ascent. The calculation of the gradient goes as follows:

$$\nabla J(\theta) =  \nabla \mathbb{E}_{\tau \sim p_{\theta}(\tau)}[R(\tau)]$$
$$= \nabla \int  p_{\theta}(\tau) R(\tau) \mathrm{d\tau}$$
$$= \int \nabla  p_{\theta}(\tau) R(\tau) \mathrm{d\tau}$$
$$= \int p_{\theta}(\tau) \nabla \log p_{\theta}(\tau) R(\tau) \mathrm{d\tau}$$
$$= \mathbb{E}_{\tau \sim p_{\theta}(\tau)}[\nabla \log p_{\theta}(\tau) R(\tau)]$$

Now that we have an expression for the policy gradient, it is easy to see that this quantity can be approximated by sampling trajectories from $p_{\theta}(\tau)$:

$$\nabla J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N}[\nabla \log p_{\theta}(\tau) R(\tau)]$$

#### Expressing the Gradient in Terms of the Policy

So how do we get the policy gradient approximation in terms of the parameterized policy $\pi_{\theta}$? Let's start by expanding $p_{\theta}(\tau)$:

$$p_{\theta}(\tau) = p_{\theta}(s_1, a_1, ..., s_T, a_T)$$
$$= p(s_1) \cdot \pi_{\theta}(a_1|s_1) \cdot p(s_2 | s_1, a_1) \cdots p(s_{T+1} | s_T, a_T)$$
$$= p(s_1) \prod_{t=1}^{T} \pi_{\theta}(a_t|s_t) p(s_{t+1} | s_t, a_t)$$

Now, taking the logarithm of both sides we get:

$$\log p_{\theta}(\tau) = \log p(s_1) + \sum_{t=1}^{T} \log \pi_{\theta}(a_t|s_t) +  \sum_{t=1}^{T} \log p(s_{t+1} | s_t, a_t)$$

And differentiating with respect to $\theta$:

$$\nabla \log p_{\theta}(\tau) = \displaystyle\sum_{t=1}^{T} \nabla \log\pi_{\theta}(a_t|s_t)$$

#### Final Policy Gradient Expression

Combining the equations above, we get:

$$\nabla J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N}[\nabla \log p_{\theta}(\tau) R(\tau)]$$
$$= \frac{1}{N}\sum_{i=1}^{N} \left(\sum_{t=1}^{T} \nabla \log\pi_{\theta}(a_t|s_t)\right)R(\tau)$$
$$= \frac{1}{N}\sum_{i=1}^{N} \left(\sum_{t=1}^{T} \nabla \log\pi_{\theta}(a_t|s_t)\right) \left(\sum_{t=1}^{T} r(s_t, a_t)\right)$$

#### REINFORCE Algorithm

```
1. For k = 1, 2, ... do
2.     Sample a set of trajectories {τ⁽ⁱ⁾} from π_θ(aₜ | sₜ)
3.     Approximate the policy gradient:
       ∇J(θ) ≈ (1/N) Σᵢ (Σₜ ∇log π_θ(aₜ|sₜ)) (Σₜ r(sₜ, aₜ))
4.     Update the parameters: θ ← θ + α∇J(θ)
```

#### Key Questions for Further Study

1. **At its core, REINFORCE computes an approximate gradient of the reward with respect to the parameters. Why can't we just use the familiar stochastic gradient descent?**

2. **Does the REINFORCE gradient estimator resemble maximum likelihood estimation (MLE)? Why or why not?**

3. **In its original formulation, REINFORCE is an on-policy algorithm. Why? Can we make REINFORCE work off-policy as well?**

4. **Do policy gradient methods work for discrete and continuous action spaces? If not, why not?**

---

## Summary of Key Equations

| Concept | Equation |
|---------|----------|
| State Value | $V_\pi(s) = \sum_a \pi(a\|s) \sum_{s',r} p(s',r\|s,a)[r + \gamma V_\pi(s')]$ |
| Action Value | $Q_\pi(s,a) = \sum_{s',r} p(s',r\|s,a)[r + \gamma V_\pi(s')]$ |
| Bellman Operator | $L_\pi(V) = \mathbf{r}_\pi + \gamma \mathbf{P}_\pi V$ |
| Bellman Optimality | $V^*(s) = \max_a Q^*(s,a)$ |
| Contraction Property | $\|L_\pi u - L_\pi v\| \leq \gamma \|u - v\|$ |
| Value Iteration | $V_{k+1}(s) = \max_a Q_k(s,a)$ |
| Policy Iteration | $\pi_{k+1} = \arg\max_\pi (\mathbf{r}_\pi + \gamma \mathbf{P}_\pi V_{\pi_k})$ |
| MC Update | $V(S_t) \leftarrow V(S_t) + \alpha(G_t - V(S_t))$ |
| TD(0) Update | $V(S_t) \leftarrow V(S_t) + \alpha(R_{t+1} + \gamma V(S_{t+1}) - V(S_t))$ |
| TD Target | $R_{t+1} + \gamma V(S_{t+1})$ |
| TD Error | $\delta_t = R_{t+1} + \gamma V(S_{t+1}) - V(S_t)$ |
| Policy Gradient | $\nabla J(\theta) = \mathbb{E}_{\tau}[\nabla \log p_{\theta}(\tau) R(\tau)]$ |
| REINFORCE Update | $\theta \leftarrow \theta + \alpha \nabla J(\theta)$ |

---

## References

- MFRL (Mathematical Foundations of Reinforcement Learning) Course Notes
- Sutton & Barto: Reinforcement Learning: An Introduction
- David Silver: RL Course (Lectures 4-5: Model-Free Prediction)
- Policy Gradient Notes (jmichaux.github.io)
