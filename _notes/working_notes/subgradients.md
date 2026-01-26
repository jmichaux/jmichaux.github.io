---
title: "Subgradients of Convex Functions"
subtitle: "Technical Notes"
geometry: margin=1in
fontsize: 11pt
header-includes:
  - \usepackage{amsmath}
  - \usepackage{amssymb}
  - \usepackage{amsthm}
  - \newtheorem{definition}{Definition}[section]
  - \newtheorem{proposition}{Proposition}[section]
  - \newtheorem{theorem}{Theorem}[section]
  - \newtheorem{lemma}{Lemma}[section]
  - \newtheorem{corollary}{Corollary}[section]
  - \newtheorem{remark}{Remark}[section]
  - \newtheorem{example}{Example}[section]
---

# Part I: Subgradients of Convex Functions

## 1. Introduction and Definition

For a convex function $f: \mathbb{R} \to \mathbb{R}$, the graph $y = f(x)$ lies above any tangent line. At a point $\bar{x}$, a line through $(\bar{x}, f(\bar{x}))$ with slope $v$ that lies below the graph is called a **subtangent line**:

$$y = f(\bar{x}) + v(x - \bar{x})$$

This subtangent line satisfies:

$$f(\bar{x}) + v(x - \bar{x}) \leq f(x) \quad \forall x \in \mathbb{R}$$

The collection of all slopes of subtangent lines at $\bar{x}$ is called the **subdifferential** of $f$ at $\bar{x}$.

**Definition 1.1 (Subgradient and Subdifferential).** Let $f: \mathbb{R}^n \to (-\infty, \infty]$ be a convex function and let $\bar{x} \in \text{dom}(f)$ (i.e., $f(\bar{x}) < \infty$). An element $v \in \mathbb{R}^n$ is called a **subgradient** of $f$ at $\bar{x}$ if

$$\langle v, x - \bar{x} \rangle \leq f(x) - f(\bar{x})$$

for all $x \in \mathbb{R}^n$. The collection of all subgradients of $f$ at $\bar{x}$ is called the **subdifferential** of $f$ at $\bar{x}$, denoted by $\partial f(\bar{x})$.

*Remark.* The subdifferential is a set! Any vector $v \in \mathbb{R}^n$ that satisfies the subgradient inequality is called a subgradient of $f$ at $\bar{x}$.

## 2. Examples

**Example 2.1 (Absolute Value Function).** Consider the function $f(x) = |x|$ for $x \in \mathbb{R}$.

If we look at all the slopes at $x = 0$, they form the interval $[-1, 1]$. Thus:

$$\partial f(0) = [-1, 1]$$

*Proof.* We prove this by showing both inclusions.

**(⇒)** Fix any $v \in \partial f(0)$. Then by definition we have:

$$v(x - 0) \leq f(x) - f(0) \quad \forall x \in \mathbb{R}$$

$$\Rightarrow vx \leq |x| \quad \forall x \in \mathbb{R}$$

For $x = 1$: $v \cdot 1 \leq 1$, so $v \leq 1$.

For $x = -1$: $v(-1) = -v \leq |-1| = 1$, so $v \geq -1$.

Thus $v \in [-1, 1]$, and $\partial f(0) \subseteq [-1, 1]$.

**(⇐)** Fix any $v \in [-1, 1]$. Then $|v| \leq 1$. For any $x \in \mathbb{R}$:

$$v(x - 0) = vx \leq |v||x| = |v| \cdot |x| \leq |x| - |0| = f(x) - f(0)$$

Therefore $v \in \partial f(0)$. So $[-1, 1] \subseteq \partial f(0)$.

Thus, we have proved $\partial f(0) = [-1, 1]$. ∎

**Example 2.2 (Euclidean Norm).** Consider the function $f(x) = \|x\|$ for $x \in \mathbb{R}^n$.

*Recall:* $\|x\| = \sqrt{\langle x, x \rangle} = \sqrt{x_1^2 + \cdots + x_n^2}$ where $x = (x_1, \ldots, x_n)$.

*Note:* This function is not differentiable at the origin.

Then:

$$\partial f(0) = B := \{v \in \mathbb{R}^n \mid \|v\| \leq 1\}$$

(the closed unit ball).

*Proof.*

**(⇒)** Fix any $v \in \partial f(0)$. Then by definition:

$$\langle v, x - 0 \rangle = \langle v, x \rangle \leq f(x) - f(0) = \|x\|$$

for all $x \in \mathbb{R}^n$. Since this is true for all $x \in \mathbb{R}^n$, it is true for $x = 0$, $x = \hat{e}_i$ (standard basis vectors), and $x = v$.

For $x = v$: $\langle v, v \rangle \leq \|v\|$

$$\Rightarrow \|v\|^2 \leq \|v\|$$
$$\Rightarrow \|v\| \leq 1 \quad \text{or} \quad v \in B$$

Therefore $\partial f(0) \subseteq B$.

**(⇐)** Fix any $v \in \mathbb{R}^n$ such that $\|v\| \leq 1$. Then for any $x \in \mathbb{R}^n$:

$$\langle v, x - 0 \rangle = \langle v, x \rangle \leq \|v\| \|x\| \leq \|x\| = f(x) - f(0)$$

(by Cauchy-Schwarz inequality).

Since this is true for all $x \in \mathbb{R}^n$, $v \in \partial f(0)$. Therefore $B \subseteq \partial f(0)$.

Thus, we have proved $\partial f(0) = B$. ∎

---

# Part II: Differentiability and Subgradients

## 3. Review of Differentiable Functions

**Definition 3.1 (Differentiability).** Let $f: \mathbb{R}^n \to \mathbb{R}$ be a real-valued function. We say that $f$ is **differentiable** at $\bar{x}$ if there exists a $v \in \mathbb{R}^n$ such that

$$\lim_{x \to \bar{x}} \frac{f(x) - f(\bar{x}) - \langle v, x - \bar{x} \rangle}{\|x - \bar{x}\|} = 0$$

We denote $v = \nabla f(\bar{x})$ and call it the **gradient** of $f$ at $\bar{x}$.

For differentiable convex functions, the tangent line at any point lies below the graph of the function. In other words, the tangent line lower bounds convex functions:

$$f(x) \geq f(\bar{x}) + \langle \nabla f(\bar{x}), x - \bar{x} \rangle \quad \forall x \in \mathbb{R}^n$$

## 4. Gradient as Subgradient

**Proposition 4.1.** Let $f: \mathbb{R}^n \to \mathbb{R}$ be a convex function. If $f$ is differentiable at $\bar{x}$, then

$$\langle \nabla f(\bar{x}), x - \bar{x} \rangle \leq f(x) - f(\bar{x})$$

for all $x \in \mathbb{R}^n$.

*Proof.* Can be done directly using convexity and the definition of differentiability. ∎

**Proposition 4.2.** Let $f: \mathbb{R}^n \to \mathbb{R}$ be a convex function. If $f$ is differentiable at $\bar{x}$, then

$$\partial f(\bar{x}) = \{\nabla f(\bar{x})\}$$

*Proof.* We prove both inclusions.

**Lemma.** Suppose that $\langle v, h \rangle \leq \varepsilon \|h\|$ whenever $\|h\| < \delta$, where $\varepsilon, \delta > 0$. Then $\|v\| \leq \varepsilon$.

*Proof of Lemma.* Let $h = \frac{\delta}{2} \cdot \frac{v}{\|v\|}$ (assuming $v \neq 0$). Then:

$$\left\langle v, \frac{\delta}{2} \cdot \frac{v}{\|v\|} \right\rangle \leq \varepsilon \left\| \frac{\delta}{2} \cdot \frac{v}{\|v\|} \right\| = \varepsilon \cdot \frac{\delta}{2}$$

Then $\left\langle v, \frac{v}{\|v\|} \right\rangle \leq \varepsilon$. Dividing both sides by $\frac{\delta}{2}$:

$$\|v\| \leq \varepsilon \quad \blacksquare$$

**(⇒)** For any $v \in \mathbb{R}^n$, $\langle \nabla f(\bar{x}), x - \bar{x} \rangle \leq f(x) - f(\bar{x})$. By definition, $\nabla f(\bar{x}) \in \partial f(\bar{x})$.

**(⇐)** Fix any $v \in \partial f(\bar{x})$. Then by definition:

$$\langle v, x - \bar{x} \rangle \leq f(x) - f(\bar{x}) \quad \forall x \in \mathbb{R}^n$$

Since $f$ is differentiable at $\bar{x}$:

$$\lim_{x \to \bar{x}} \frac{f(x) - f(\bar{x}) - \langle v, x - \bar{x} \rangle}{\|x - \bar{x}\|} = 0$$

Thus, for all $\varepsilon > 0$, there exists $\delta > 0$ such that:

$$\left| \frac{f(x) - f(\bar{x}) - \langle \nabla f(\bar{x}), x - \bar{x} \rangle}{\|x - \bar{x}\|} \right| < \varepsilon$$

whenever $\|x - \bar{x}\| < \delta$ (and $x \neq \bar{x}$).

Then:

$$f(x) - f(\bar{x}) - \langle \nabla f(\bar{x}), x - \bar{x} \rangle < \varepsilon \|x - \bar{x}\|$$

whenever $\|x - \bar{x}\| < \delta$.

So:

$$f(x) - f(\bar{x}) \leq \langle \nabla f(\bar{x}), x - \bar{x} \rangle + \varepsilon \|x - \bar{x}\|$$

Then, by definition of subgradient:

$$\langle v, x - \bar{x} \rangle \leq \langle \nabla f(\bar{x}), x - \bar{x} \rangle + \varepsilon \|x - \bar{x}\|$$
$$\Rightarrow \langle v - \nabla f(\bar{x}), x - \bar{x} \rangle \leq \varepsilon \|x - \bar{x}\|$$

Then, it follows from the Lemma that:

$$\|v - \nabla f(\bar{x})\| \leq \varepsilon$$

Since $\varepsilon > 0$ is arbitrary, $v = \nabla f(\bar{x})$.

Therefore $\partial f(\bar{x}) \subseteq \{\nabla f(\bar{x})\}$.

Thus, $\partial f(\bar{x}) = \{\nabla f(\bar{x})\}$. ∎

*Remark.* Prove for the 1D case as an exercise.

---

# Part III: Indicator Functions and Normal Cones

## 5. Indicator Functions

**Definition 5.1 (Indicator Function).** This is an extended real-valued function. Let $\Omega$ be a nonempty subset of $\mathbb{R}^n$. The **indicator function** associated with $\Omega$ is defined by:

$$\delta_\Omega(x) = \begin{cases} 0 & \text{if } x \in \Omega \\ \infty & \text{if } x \notin \Omega \end{cases}$$

*Properties:*

$$\text{dom}(\delta_\Omega) = \Omega$$

$$\text{epi}(\delta_\Omega) = \Omega \times [0, \infty)$$

**Proposition 5.2.** For any $\bar{x} \in \Omega \cap \text{dom}(f)$:

$$\partial \delta_\Omega(\bar{x}) = N(\bar{x}; \Omega)$$

where $N(\bar{x}; \Omega)$ denotes the normal cone to $\Omega$ at $\bar{x}$.

*Proof.* Fix any $v \in \partial \delta_\Omega(\bar{x})$. Then:

$$\langle v, x - \bar{x} \rangle \leq \delta_\Omega(x) - \delta_\Omega(\bar{x}) \quad \forall x \in \mathbb{R}^n$$
$$\Rightarrow \langle v, x - \bar{x} \rangle \leq \delta_\Omega(x) \quad \forall x \in \mathbb{R}^n$$

Since this is true for all $x \in \mathbb{R}^n$, this is true for all $x \in \Omega$. Thus for any $x \in \Omega$ we have:

$$\langle v, x - \bar{x} \rangle \leq \delta_\Omega(x) = 0$$

By definition, $v \in N(\bar{x}; \Omega)$. Therefore $\partial \delta_\Omega(\bar{x}) \subseteq N(\bar{x}; \Omega)$.

To prove the opposite inclusion, fix $v \in B$. That is, $\|v\| \leq 1$. Fix any $x \in \mathbb{R}^n$:

$$\langle v, x - 0 \rangle = \langle v, x \rangle \leq \|v\| \|x\| \leq \|x\| = f(x) - f(0)$$

(by Cauchy-Schwarz inequality).

Therefore $B \subseteq \partial f(0)$.

In conclusion, $\partial f(0) = B$. ∎

---

# Part IV: Geometric Characterization of Subdifferentials

## 6. Subdifferentials and Normal Cones to Epigraphs

**Proposition 6.1.** Let $f: \mathbb{R}^n \to (-\infty, \infty]$ be a convex function and let $\bar{x} \in \text{dom}(f)$ (i.e., $f(\bar{x}) < \infty$). Then:

$$\partial f(\bar{x}) = \{v \in \mathbb{R}^n \mid (v, -1) \in N((\bar{x}, f(\bar{x})); \text{epi}(f))\}$$

*Remark.* Why the $-1$? This comes from the geometry of the epigraph and the definition of the normal cone.

*Proof.*

**(⇐)** Fix any $v \in \mathbb{R}^n$ such that $(v, -1) \in N((\bar{x}, f(\bar{x})); \text{epi}(f))$.

Then, by definition:

$$\langle (v, -1), (x, \lambda) - (\bar{x}, f(\bar{x})) \rangle \leq 0 \quad \forall (x, \lambda) \in \text{epi}(f)$$

Note that this follows from the definition of normal cone at $(\bar{x}, f(\bar{x}))$ to the epigraph of $f$.

Fix any $x \in \text{dom}(f)$. Then $(x, f(x)) \in \text{epi}(f)$. Thus by the above:

$$\langle (v, -1), (x, f(x)) - (\bar{x}, f(\bar{x})) \rangle \leq 0$$

So:

$$\langle v, x - \bar{x} \rangle + (-1)(f(x) - f(\bar{x})) \leq 0 \quad \forall x \in \text{dom}(f)$$
$$\Rightarrow \langle v, x - \bar{x} \rangle \leq f(x) - f(\bar{x}) \quad \forall x \in \text{dom}(f)$$

Then:

$$\langle v, x - \bar{x} \rangle \leq f(x) - f(\bar{x}) \quad \forall x \in \mathbb{R}^n$$

This is true because if $x \notin \text{dom}(f)$, then $f(x) = \infty$.

Therefore by definition, $v \in \partial f(\bar{x})$.

And:

$$\{v \in \mathbb{R}^n \mid (v, -1) \in N((\bar{x}, f(\bar{x})); \text{epi}(f))\} \subseteq \partial f(\bar{x})$$

**(⇒)** Fix any $v \in \partial f(\bar{x})$ and show that $(v, -1) \in N((\bar{x}, f(\bar{x})); \text{epi}(f))$.

Fix any $(x, \lambda) \in \text{epi}(f)$. Then $\lambda \geq f(x)$ by definition of the epigraph of $f$. It follows that:

$$\langle (v, -1), (x, \lambda) - (\bar{x}, f(\bar{x})) \rangle$$
$$= \langle v, x - \bar{x} \rangle + (-1)(\lambda - f(\bar{x}))$$
$$= \langle v, x - \bar{x} \rangle - (\lambda - f(\bar{x}))$$
$$\leq \langle v, x - \bar{x} \rangle - (f(x) - f(\bar{x}))$$

Since $v \in \partial f(\bar{x})$:

$$\langle v, x - \bar{x} \rangle \leq f(x) - f(\bar{x})$$

Thus:

$$\langle v, x - \bar{x} \rangle - (f(x) - f(\bar{x})) \leq 0$$

Therefore:

$$\langle (v, -1), (x, \lambda) - (\bar{x}, f(\bar{x})) \rangle \leq 0 \quad \forall (x, \lambda) \in \text{epi}(f)$$

Therefore, by definition:

$$(v, -1) \in N((\bar{x}, f(\bar{x})); \text{epi}(f))$$

∎

---

# Part V: Subdifferential Sum Rule

## 7. Motivation

Let $f: \mathbb{R}^n \to (-\infty, \infty]$ be a convex function. The subdifferential of $f$ at $\bar{x} \in \text{dom}(f)$ is defined by:

$$\partial f(\bar{x}) = \{v \in \mathbb{R}^n \mid \langle v, x - \bar{x} \rangle \leq f(x) - f(\bar{x}) \quad \forall x \in \mathbb{R}^n\}$$

Any vector $v \in \mathbb{R}^n$ that satisfies this inequality is called a subgradient of $f$ at $\bar{x}$.

**Question:** Can we have $\partial(f + g)(\bar{x}) = \partial f(\bar{x}) + \partial g(\bar{x})$?

Yes, under certain conditions. We will develop a geometric approach in order to prove the subdifferential sum rule.

*Recall:* If we have $\Omega_1, \Omega_2$, then:

$$N(\bar{x}; \Omega_1 \cap \Omega_2) = N(\bar{x}; \Omega_1) + N(\bar{x}; \Omega_2)$$

under appropriate constraint qualification conditions.

## 8. Connection to Normal Cones

The key insight is that subdifferentials can be characterized in terms of normal cones to epigraphs (Proposition 6.1). This allows us to use the calculus rules for normal cones to derive calculus rules for subdifferentials.

**Theorem 8.1 (Subdifferential Sum Rule).** Let $f, g: \mathbb{R}^n \to (-\infty, \infty]$ be convex functions and let $\bar{x} \in \text{dom}(f) \cap \text{dom}(g)$. Under appropriate constraint qualification conditions (such as $\text{ri}(\text{dom}(f)) \cap \text{ri}(\text{dom}(g)) \neq \emptyset$):

$$\partial(f + g)(\bar{x}) = \partial f(\bar{x}) + \partial g(\bar{x})$$

*Proof sketch.* The proof uses the geometric characterization of subdifferentials via normal cones to epigraphs, combined with the sum rule for normal cones of intersecting convex sets. The constraint qualification ensures that the sum rule for normal cones applies. ∎

---

# Summary

**Key Results:**

1. **Subgradient Definition:** $v \in \partial f(\bar{x})$ iff $\langle v, x - \bar{x} \rangle \leq f(x) - f(\bar{x})$ for all $x \in \mathbb{R}^n$.

2. **Examples:**
   - For $f(x) = |x|$: $\partial f(0) = [-1, 1]$
   - For $f(x) = \|x\|$: $\partial f(0) = B$ (closed unit ball)

3. **Differentiable Case:** If $f$ is convex and differentiable at $\bar{x}$, then $\partial f(\bar{x}) = \{\nabla f(\bar{x})\}$.

4. **Indicator Functions:** $\partial \delta_\Omega(\bar{x}) = N(\bar{x}; \Omega)$

5. **Geometric Characterization:** $\partial f(\bar{x}) = \{v \in \mathbb{R}^n \mid (v, -1) \in N((\bar{x}, f(\bar{x})); \text{epi}(f))\}$

6. **Sum Rule:** Under constraint qualification, $\partial(f + g)(\bar{x}) = \partial f(\bar{x}) + \partial g(\bar{x})$
