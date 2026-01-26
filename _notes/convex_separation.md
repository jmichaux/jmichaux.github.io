---
title: "Convex Separation Theorems"
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

# Part I: Strict Separation of a Point from a Convex Set

This topic is important because separation theorems can be used to derive properties of subdifferential calculus.

## 1. Strict Separation

*Recall:* The projection $\bar{\omega} = \pi(\bar{x}; \Omega)$ is a singleton when Ω is closed and convex, and satisfies $\langle \bar{x} - \bar{\omega}, \omega - \bar{\omega} \rangle \leq 0$ for all $\omega \in \Omega$.

**Proposition 1.1 (Strict Separation of a Point).** Let Ω be a nonempty closed convex subset of ℝⁿ and let $\bar{x} \notin \Omega$. Then there exists $v \in \mathbb{R}^n$ (with $v \neq 0$) such that

$$\sup\{\langle v, x \rangle \mid x \in \Omega\} < \langle v, \bar{x} \rangle.$$

If this is true, we say that $\bar{x}$ and Ω are **strictly separated**.

*Proof.* Let $\bar{\omega} = \pi(\bar{x}; \Omega)$. Then $\langle \bar{x} - \bar{\omega}, \omega - \bar{\omega} \rangle \leq 0$ for all $\omega \in \Omega$.

Let $v = \bar{x} - \bar{\omega}$. Then

$$\langle v, \omega - \bar{\omega} \rangle \leq 0 \quad \forall \omega \in \Omega.$$

So

$$\langle v, \omega - \bar{\omega} \rangle = \langle v, \omega - (\bar{x} - v) \rangle = \langle v, \omega \rangle - \langle v, \bar{x} - v \rangle = \langle v, \omega \rangle - \langle v, \bar{x} \rangle + \langle v, v \rangle \leq 0.$$

Thus

$$\langle v, \omega \rangle + \|v\|^2 \leq \langle v, \bar{x} \rangle.$$

Therefore

$$\sup\{\langle v, \omega \rangle \mid \omega \in \Omega\} < \langle v, \bar{x} \rangle. \quad \blacksquare$$

*Note:* In the proof, $v \neq 0$ because $\bar{x} \notin \Omega$ while $\bar{\omega} \in \Omega$ (since Ω is closed).

## 2. Separating Hyperplanes

Based on the above proposition, we have:

$$\sup\{\langle v, \omega \rangle \mid \omega \in \Omega\} < \langle v, \bar{\omega} \rangle.$$

This means there exists $\alpha \in \mathbb{R}$ such that

$$\sup\{\langle v, \omega \rangle \mid \omega \in \Omega\} \leq \alpha < \langle v, \bar{\omega} \rangle.$$

**Definition 1.2 (Hyperplane and Half-Spaces).** We can define the following sets:

$$L = \{x \in \mathbb{R}^n \mid \langle v, x \rangle = \alpha\}$$

$$L^+ = \{x \in \mathbb{R}^n \mid \langle v, x \rangle > \alpha\}$$

$$L^- = \{x \in \mathbb{R}^n \mid \langle v, x \rangle \leq \alpha\}$$

Then we see $\Omega \subseteq L^-$ and $\bar{x} \in L^+$. So the hyperplane L separates $\bar{x}$ and Ω.

---

# Part II: Convex Separation Theorems for Two Convex Sets

## 3. Separation of Compact and Closed Convex Sets

**Theorem 2.1.** Let $\Omega_1 \subseteq \mathbb{R}^n$ be nonempty, compact, and convex. Let $\Omega_2 \subseteq \mathbb{R}^n$ be nonempty and closed. Then there exists $v \in \mathbb{R}^n$ such that

$$\sup\{\langle v, x \rangle \mid x \in \Omega_1\} < \inf\{\langle v, y \rangle \mid y \in \Omega_2\}.$$

**Lemma 2.2.** In the setting of Theorem 2.1,

$$\Omega := \Omega_1 - \Omega_2$$

is a closed subset of ℝⁿ.

(In other words, we want to show that for all $x \in \Omega_1$ and $y \in \Omega_2$, $\omega_0 = x - y$ satisfies $\omega_0 \in \Omega$.)

*Proof.* Let $\{w_k\}$ be a sequence in Ω that converges to $w_0$. We want to show $w_0 \in \Omega$. Then for all $k \in \mathbb{N}$, $w_k = x_k - y_k$. Since $\Omega_1$ is compact, the sequence $\{x_k\}$ contains a convergent subsequence $\{x_{k_\ell}\}$. Thus, we have $w_{k_\ell} = x_{k_\ell} - y_{k_\ell}$ and

$$y_{k_\ell} = x_{k_\ell} - w_{k_\ell} \to x_0 - w_0 \in \Omega_2$$

(because $\Omega_2$ is closed). Therefore $w_0 \in x_0 - \Omega_2 \subseteq \Omega_1 - \Omega_2$. Therefore Ω is closed. $\blacksquare$

*Question:* Under what conditions can $\Omega_1$ and $\Omega_2$ be separated by a hyperplane L?

**Theorem 2.3 (Strict Separation of Disjoint Convex Sets).** Let $\Omega_1$ be a nonempty compact convex set and let $\Omega_2$ be a nonempty closed convex set in ℝⁿ. Suppose that $\Omega_1 \cap \Omega_2 = \emptyset$. Then $\Omega_1$ and $\Omega_2$ can be separated strictly in the sense that there exists $v \in \mathbb{R}^n$ ($v \neq 0$) such that

$$\sup\{\langle v, x \rangle \mid x \in \Omega_1\} < \inf\{\langle v, y \rangle \mid y \in \Omega_2\}.$$

*Proof.* Define $\Omega := \Omega_1 - \Omega_2$. By the previous Lemma, Ω is nonempty and closed. Furthermore, because $\Omega_1 \cap \Omega_2 = \emptyset$, we have $0 \notin \Omega$.

By the previous Proposition (strict separation of a point), there exists $v \in \mathbb{R}^n$ ($v \neq 0$) such that

$$\alpha := \sup\{\langle v, \omega \rangle \mid \omega \in \Omega\} < \langle v, 0 \rangle = 0.$$

For any $x \in \Omega_1$ and $y \in \Omega_2$, we have $x - y \in \Omega$, so

$$\langle v, x - y \rangle \leq \alpha$$

$$\langle v, x \rangle \leq \alpha + \langle v, y \rangle.$$

This implies:

$$\sup\{\langle v, x \rangle \mid x \in \Omega_1\} \leq \alpha + \inf\{\langle v, y \rangle \mid y \in \Omega_2\}$$

$$\Rightarrow \sup\{\langle v, x \rangle \mid x \in \Omega_1\} < \inf\{\langle v, y \rangle \mid y \in \Omega_2\}. \quad \blacksquare$$

## 4. Geometric Interpretation

$$\sup\{\langle v, x \rangle \mid x \in \Omega_1\} < \gamma < \inf\{\langle v, y \rangle \mid y \in \Omega_2\}$$

Define:

$$L = \{x \in \mathbb{R}^n \mid \langle v, x \rangle = \gamma\}$$

$$L^+ = \{x \in \mathbb{R}^n \mid \langle v, x \rangle \geq \gamma\}$$

$$L^- = \{x \in \mathbb{R}^n \mid \langle v, x \rangle \leq \gamma\}$$

Then $\Omega_1 \subseteq L^-$ and $\Omega_2 \subseteq L^+$.

*Review:* Separating hyperplanes in Boyd et al. relate to $\{x \mid x \in \mathbb{R}^n\}$ and $\{a^T x = \gamma \mid x \in \mathbb{R}\}$.

---

# Part III: Proper Separation of Convex Sets

## 5. Separation Without Compactness

*Recall the property we proved:* Let Ω be a nonempty closed convex set in ℝⁿ and let $\bar{x} \notin \Omega$. Then there exists $v \in \mathbb{R}^n$ ($v \neq 0$) such that

$$\sup\{\langle v, x \rangle \mid x \in \Omega\} < \langle v, \bar{x} \rangle.$$

**Remark (Lemma 2.4).** Let Ω be a nonempty convex set and let $\bar{x} \notin \Omega$. Then there exists $v \in \mathbb{R}^n$ ($v \neq 0$) such that

$$\sup\{\langle v, x \rangle \mid x \in \Omega\} < \langle v, \bar{x} \rangle.$$

*Proof.* Since Ω is convex, $\bar{\Omega}$ is also convex. Furthermore, $\bar{\Omega}$ is nonempty, closed, and convex. Since $\bar{x} \notin \bar{\Omega}$, there exists $v \in \mathbb{R}^n$ ($v \neq 0$) such that

$$\sup\{\langle v, x \rangle \mid x \in \bar{\Omega}\} < \langle v, \bar{x} \rangle.$$

Since $\Omega \subseteq \bar{\Omega}$, we have:

$$\sup\{\langle v, x \rangle \mid x \in \Omega\} \leq \sup\{\langle v, x \rangle \mid x \in \bar{\Omega}\} < \langle v, \bar{x} \rangle. \quad \blacksquare$$

## 6. Separation Theorems for Subspaces

**Proposition 3.1.** Let L be a subspace of ℝⁿ and let Ω be a nonempty subset of L with $\bar{x} \notin \bar{\Omega}$ and $\bar{x} \in L$. Then there exists $v \in L$ ($v \neq 0$) such that

$$\sup\{\langle v, x \rangle \mid x \in \Omega\} < \langle v, \bar{x} \rangle.$$

## 7. Proper Separation

**Definition 3.2 (Proper Separation).** Let $\Omega_1, \Omega_2 \subseteq \mathbb{R}^n$ be nonempty and convex. We say that $\Omega_1$ and $\Omega_2$ can be **properly separated** if there exists $v \in \mathbb{R}^n$ ($v \neq 0$) such that

$$\langle v, x \rangle \leq \langle v, y \rangle \quad \forall x \in \Omega_1 \text{ and } \forall y \in \Omega_2 \quad (1)$$

and there exists $\bar{x} \in \Omega_1$ and $\bar{y} \in \Omega_2$ such that

$$\langle v, \bar{x} \rangle < \langle v, \bar{y} \rangle. \quad (2)$$

Note that (1) is equivalent to

$$\sup_{x \in \Omega_1} \langle v, x \rangle \leq \sup_{y \in \Omega_1} \langle v, y \rangle.$$

The second property (2) can be written as follows:

$$\inf_{x \in \Omega_1} \langle v, x \rangle < \sup_{y \in \Omega_2} \langle v, y \rangle.$$

**Example.** Consider $\Omega_1 = [a, b]$ and $\Omega_2$ = line containing $[a, b]$. What is v?

---

# Part IV: Relative Interior and Affine Sets

## 8. Affine Sets

**Definition 4.1 (Affine Set).** A subset $A \subseteq \mathbb{R}^n$ is called an **affine set** if

$$\lambda a + (1 - \lambda)b \in A$$

whenever $a, b \in A$ and $\lambda \in \mathbb{R}$.

*Geometrically:* A contains the line connecting any two points in A.

**Examples:**

- Any plane in ℝ³
- Any line in ℝ²

**Definition 4.2 (Affine Hull).** Let $\Omega \subseteq \mathbb{R}^n$ be an arbitrary set. The **affine set generated** by Ω is the intersection of all affine sets containing Ω. We call this set the **affine hull** of Ω:

$$\text{aff}(\Omega) = \bigcap\{A \mid A \text{ is affine}, \Omega \subseteq A\}$$

**Proposition 4.3 (Properties of Affine Sets).** Let $\Omega$ be an arbitrary set in ℝⁿ. Then we have the following:

(i) $\text{aff}(\Omega)$ is the smallest affine set that contains Ω.

(ii) $\text{aff}(\Omega) = \left\{\sum_{i=1}^{m} \lambda_i \omega_i \mid \omega_i \in \Omega, \sum_{i=1}^{m} \lambda_i = 1, m \in \mathbb{N}\right\}$

*Proof.* See book of Starck. $\blacksquare$

## 9. Relative Interior

**Definition 4.4 (Relative Interior).** Let Ω be a convex set in ℝⁿ. An element $\bar{x}$ is said to be in the **relative interior** of Ω, denoted by $\text{ri}(\Omega)$, if $\bar{x}$ is the interior of Ω relative to $\text{aff}(\Omega)$.

That means there exists $\delta > 0$ such that

$$B(\bar{x}; \delta) \cap \text{aff}(\Omega) \subseteq \Omega.$$

**Example.** Based on the above example, $\bar{x} \in \text{ri}(\Omega)$, but $a, b \notin \text{ri}(\Omega)$.

In other words, $\text{ri}(\Omega) = (a, b)$.

## 10. Properties of the Relative Interior

These properties are important for proving the proper separation property.

**Theorem 4.5 (Properties of Relative Interior).** Let Ω be a nonempty convex set in ℝⁿ. Then

(i) $\text{ri}(\Omega)$ is always nonempty. (Why?)

(ii) If $a \in \text{ri}(\Omega)$ and $b \in \bar{\Omega}$, then

$$\lambda a + (1 - \lambda)b \in \text{ri}(\Omega) \quad \text{if } 0 < \lambda \leq 1.$$

*Proof.* Omitted. $\blacksquare$

**Theorem 4.6 (Relative Interior of Minkowski Difference).** If $\Omega_1$ and $\Omega_2$ are nonempty convex sets in ℝⁿ, then

$$\text{ri}(\Omega_1 - \Omega_2) = \text{ri}(\Omega_1) - \text{ri}(\Omega_2).$$

*Review this.* Note that $\Omega_1 - \Omega_2 := \{x - y \mid x \in \Omega_1, y \in \Omega_2\}$. $\blacksquare$

---

# Part V: Remarks on Affine Sets

## 11. Properties of Affine Sets

**Remarks:**

**(i)** If $A \subseteq \mathbb{R}^n$ is affine and $0 \in A$, then A is a subspace of ℝⁿ.

*Proof Sketch:* The idea is we want to show:
- $\alpha \cdot 0 \in A$ (contains identity)
- Closed under scalar multiplication
- Closed under addition

Let $\alpha \in \mathbb{R}^n$, $r \in \mathbb{R}$. Then $r\alpha = r\alpha + (1 - r) \cdot 0$.

Let $x, y \in \mathbb{R}^n$. Then $x + y = \frac{1}{2}(2x + 2y) \in A$ (by affine scalar multiplication).

**(ii)** Any affine set $A \subseteq \mathbb{R}^n$ is a closed set. In particular, $\text{aff}(\Omega)$ is closed for any subset Ω of ℝⁿ.

*Proof Sketch:* Fix $x_0 \in A$ and let

$$L = A - x_0 = \{a - x_0 \mid a \in A\}.$$

L is an affine subspace of ℝⁿ.

$$A = L + x_0 \Rightarrow A \text{ is a subspace, and subspaces of ℝⁿ are closed. Why?}$$

*Property of separation:*

$$\langle v, x \rangle \leq 0 \text{ for all } x \in \Omega$$
$$\langle v, \bar{x} \rangle < 0 \text{ for some } \bar{x} \in \Omega$$

**(iii)** The sequence $\{x_k\}$, where $x_k = -\frac{1}{k}x_0$, belongs to $\text{aff}(\Omega) = L$.

$$0 \in \bar{\Omega}$$
$$\Omega \subseteq \text{aff}(\Omega)$$
$$\Rightarrow \bar{\Omega} \subseteq \overline{\text{aff}(\Omega)} = \text{aff}(\Omega)$$
$$\Rightarrow \text{aff}(\Omega) \text{ is a subspace of } \mathbb{R}^n.$$
$$x_k = -\frac{1}{k}x_0 \in L.$$

---

# Part VI: The Proper Separation Theorem

## 12. Main Lemmas

**Lemma 5.1.** Let Ω be a nonempty convex set in ℝⁿ such that $0 \in \bar{\Omega} \setminus \text{ri}(\Omega)$. Then there exists a sequence $\{x_k\}$ in ℝⁿ such that $x_k \notin \bar{\Omega}$ for all $k \in \mathbb{N}$ and $x_k \to 0$ as $k \to \infty$.

The proof is based on the properties of relative interiors of convex sets.

*Some ideas:*
- $\text{ri}(\Omega) \neq \emptyset$
- $\text{ri}(\Omega) \subseteq \Omega$
- For all $\varepsilon > 0$, $\exists x_\varepsilon \in B(0, \varepsilon) \cap \bar{\Omega}$ (since we can construct a sequence)
- $\exists x \in \text{ri}(\Omega) \cap \bar{\Omega}$
- $\varepsilon_1 = \|x_1 - 0\| = \|x_1\|$

*Proof.* Since Ω is nonempty, there exists $x_0 \in \text{ri}(\Omega)$. Then $-tx_0 \notin \bar{\Omega}$ for all $t > 0$. By contradiction, suppose that $-tx_0 \in \bar{\Omega}$ for some $t > 0$. Then

$$0 = \frac{t}{1+t}x_0 + \frac{1}{1+t}(-tx_0) \in \text{ri}(\Omega).$$

This is a contradiction to the assumption $0 \notin \text{ri}(\Omega)$.

Now, let $x_k = -\frac{1}{k}x_0$. By the above, $x_k \notin \bar{\Omega}$ for all $k$ (we verified this). Since $x_k \to 0$ as $k \to \infty$, we are done. $\blacksquare$

**Lemma 5.2.** Let Ω be a nonempty convex set in ℝⁿ. If $0 \notin \text{ri}(\Omega)$, then Ω and $\{0\}$ can be properly separated. That means, there exists an element $v \in \mathbb{R}^n$ such that

$$\sup\{\langle v, x \rangle \mid x \in \Omega\} \leq \langle v, 0 \rangle = 0$$

$$\inf\{\langle v, x \rangle \mid x \in \Omega\} < \langle v, 0 \rangle = 0.$$

*Don't understand this.* (Note from the original)

*Proof (Lemma 5.2).* We consider two cases.

**Case 1:** Assume $0 \notin \bar{\Omega}$.

In this case, we proved earlier that 0 and Ω can be strictly separated (first lecture).

This means there exists $v \in \mathbb{R}^n$ ($v \neq 0$) such that

$$\sup\{\langle v, x \rangle \mid x \in \Omega\} < \langle v, 0 \rangle = 0.$$

**Case 2:** Assume $0 \in \bar{\Omega}$.

In this case $0 \in \bar{\Omega} \setminus \text{ri}(\Omega)$.

By Lemma 5.1, there exists a sequence $\{x_k\} \subset L = \text{aff}(\Omega)$ such that $x_k \notin \bar{\Omega}$ for all $k \in \mathbb{N}$, and $x_k \to 0$ as $k \to \infty$.

Then, by the strict separation property of subspaces (L is a subspace), there exists $v_k \in L = \text{aff}(\Omega)$ ($v_k \neq 0$), and

$$\sup\{\langle v_k, x \rangle \mid x \in \Omega\} < \langle v_k, x_k \rangle.$$

In particular

$$\langle v_k, x \rangle < \langle v_k, x_k \rangle$$

for all $x \in \Omega$ and $k \in \mathbb{N}$.

Dividing both sides by the norm of $v_k$, we obtain:

$$\left\langle \frac{v_k}{\|v_k\|}, x \right\rangle < \left\langle \frac{v_k}{\|v_k\|}, x_k \right\rangle \quad (*)$$

Now let $w_k = \frac{v_k}{\|v_k\|}$. Then $\|w_k\| = 1$ and WLOG, we can assume that $w_k \to v$, where $\|v\| = 1$ (because $w_k$ is bounded and has a convergent subsequence $= w_k$).

By passing to a limit in $(*)$, we see that

$$\langle v, x \rangle \leq 0 \quad \text{for all } x \in \Omega$$

$$\left(|\langle w_k, x_k \rangle| \leq \|w_k\| \|x_k\| = \|x_k\| \to 0\right)$$

*Cauchy-Schwarz*

*Writing it out...*

$$\left|\left\langle \frac{v_k}{\|v_k\|}, x \right\rangle\right| = |\langle w_k, x \rangle| < |\langle w_k, x_k \rangle| \leq \|w_k\| \|x_k\| \leq \|x_k\|$$

Then, as $k \to \infty$ we have:

$$\forall \varepsilon > 0, \quad |\langle w_k, x \rangle| < \|x_k\|$$

$$\Rightarrow |\langle v, x \rangle| < 0$$

for all $x \in \Omega$.

It remains to show there exists $\bar{x} \in \Omega$ such that $\langle v, \bar{x} \rangle < 0$. (Why??)

Observe that $v \in L$ (because $\bar{E}_{w_k} \in L$, and L is a closed subspace of ℝⁿ).

By contradiction, suppose that $\langle v, x \rangle \geq 0$ for all $x \in \Omega$.

Then $\langle v, x \rangle = 0$ for all $x \in \Omega$. Since $v \in L = \text{aff}(\Omega)$, $v = \sum_i \lambda_i \omega_i$ where $\lambda_i \in \mathbb{R}$, $\sum_{i=1}^{n} \lambda_i = 1$, and $\omega_i \in \Omega$. Then $\|v\|^2 = \langle v, v \rangle > 0$.

$$\langle v, \sum \lambda_i \omega_i \rangle = \sum \lambda_i \langle v, \omega_i \rangle = 0.$$

This is a contradiction. $\blacksquare$

## 13. The Main Theorem

**Theorem 5.3 (Proper Separation Theorem).** Let $\Omega_1$ and $\Omega_2$ be two nonempty convex sets in ℝⁿ. If

$$\text{ri}(\Omega_1) \cap \text{ri}(\Omega_2) = \emptyset,$$

then $\Omega_1$ and $\Omega_2$ can be properly separated.

*Proof.* (Proof is mainly based on Lemma 5.2)

Define $\Omega := \Omega_1 - \Omega_2 = \{x - y \mid x \in \Omega_1, y \in \Omega_2\}$.

Then Ω is a nonempty convex set. (How do we know nonemptiness?)

Since $\text{ri}(\Omega_1) \cap \text{ri}(\Omega_2)$ can be properly separated, the set Ω and $\{0\}$ can be properly separated. By Lemma 5.2,

$$0 \notin \text{ri}(\Omega) = \text{ri}(\Omega_1) - \text{ri}(\Omega_2).$$

Since $\text{ri}(\Omega_1) \cap \text{ri}(\Omega_2) = \emptyset$, $0 \notin \text{ri}(\Omega) = \text{ri}(\Omega_1) - \text{ri}(\Omega_2)$.

By Lemma 5.1 (applied for Ω and 0), there exists $v \in \mathbb{R}^n$ ($v \neq 0$) such that

$$\langle v, z \rangle \leq 0 \text{ for all } z \in \Omega \quad (1)$$
$$\langle v, \bar{z} \rangle < 0 \text{ for some } \bar{z} \in \Omega \quad (2)$$

Fix any $x \in \Omega_1$ and $y \in \Omega_2$. Then $z = x - y \in \Omega_1 - \Omega_2 = \Omega$. Thus

$$\langle v, z \rangle \leq 0$$
$$\Rightarrow \langle v, x - y \rangle \leq 0$$
$$\Rightarrow \langle v, x \rangle \leq \langle v, y \rangle.$$

Since $\bar{z} \in \Omega$, $\bar{z} = \bar{x} - \bar{y}$ where $\bar{x} \in \Omega_1$, $\bar{y} \in \Omega_2$. Then

$$\langle v, \bar{z} \rangle = \langle v, \bar{x} \rangle - \langle v, \bar{y} \rangle < 0. \quad \#$$

It follows that

$$\langle v, \bar{x} \rangle < \langle v, \bar{y} \rangle.$$

Therefore $\Omega_1$ and $\Omega_2$ can be properly separated. $\blacksquare$

## 14. Additional Lemmas

In the following Lemma, we will prove the theorem for a special case: $\Omega_1$ is an arbitrary set and $\Omega_2$ is a singleton.

**Lemma 5.3 (Prolonging Lemma).** Let Ω be a nonempty convex set in ℝⁿ. Suppose that $\bar{x} \in \text{ri}(\Omega)$ and $\bar{y} \in \bar{\Omega}$. Then there exists $t > 0$ such that

$$\bar{x} + t(\bar{x} - \bar{y}) \in \Omega.$$

*Proof.* Since $\bar{x} \in \text{ri}(\Omega)$, there exists $\delta > 0$ such that

$$B(\bar{x}; \delta) \cap \text{aff}(\Omega) \subseteq \Omega.$$

We can choose $t > 0$ sufficiently small such that

$$\bar{x} + t(\bar{x} - \bar{y}) \in B(\bar{x}; \delta).$$

We have the following:

$$\bar{x} + t(\bar{x} - \bar{y}) = (1 + t)\bar{x} + (-t)\bar{y} \in \text{aff}(\Omega).$$

Therefore

$$\bar{x} + t(\bar{x} - \bar{y}) \in B(\bar{x}; \delta) \cap \text{aff}(\Omega) \subseteq \Omega. \quad \blacksquare$$

**Lemma 5.4.** Let Ω be a nonempty convex set in ℝⁿ. If Ω and $\{0\}$ can be properly separated, then

$$0 \notin \text{ri}(\Omega) \quad \left(\text{ri}(\{0\}) \cap \text{ri}(\Omega) = \emptyset\right).$$

*Proof.* By the definition, there exists $v \in \mathbb{R}^n$ ($v \neq 0$) such that

$$\langle v, x \rangle \leq \langle v, 0 \rangle = 0 \text{ for all } x \in \Omega,$$

and there exists $\bar{x} \in \Omega$ such that

$$\langle v, \bar{x} \rangle < 0.$$

We proceed by contradiction.

By contradiction, suppose that

$$0 \in \text{ri}(\Omega).$$

By Lemma 5.3, there exists $t > 0$ such that

$$0 + t(0 - \bar{x}) = -t\bar{x} \in \Omega.$$

Then $\langle v, -t\bar{x} \rangle \leq 0 \Rightarrow \langle v, \bar{x} \rangle \geq 0$.

But this is a contradiction. $\blacksquare$

## 15. The Converse

**Theorem 5.4 (Proper Separation - Converse).** Let $\Omega_1$ and $\Omega_2$ be nonempty convex sets in ℝⁿ. If $\Omega_1$ and $\Omega_2$ can be properly separated, then

$$\text{ri}(\Omega_1) \cap \text{ri}(\Omega_2) = \emptyset.$$

*Proof.* Define $\Omega := \Omega_1 - \Omega_2$.

Then Ω is a nonempty convex set. Since $\text{ri}(\Omega_1) \cap \text{ri}(\Omega_2)$ can be properly separated, the set Ω and $\{0\}$ can be properly separated. By Lemma 5.4,

$$0 \notin \text{ri}(\Omega) = \text{ri}(\Omega_1) - \text{ri}(\Omega_2).$$

Thus

$$\text{ri}(\Omega_1) \cap \text{ri}(\Omega_2) = \emptyset. \quad \blacksquare$$

---

# Summary

**Theorem (Proper Separation - Complete Statement).** Let $\Omega_1$ and $\Omega_2$ be two nonempty convex sets in ℝⁿ. Then $\Omega_1$ and $\Omega_2$ can be properly separated if and only if

$$\text{ri}(\Omega_1) \cap \text{ri}(\Omega_2) = \emptyset.$$

**Remark.** If $\Omega_1, \Omega_2 \in \mathbb{R}^n$ are nonempty convex sets such that $\Omega_1 \cap \Omega_2 = \emptyset$, then $\Omega_1$ and $\Omega_2$ can be properly separated.

*Proof idea:*

$$\text{ri}(\Omega_1) \subseteq \Omega_1$$
$$\text{ri}(\Omega_2) \subseteq \Omega_2$$
$$\Rightarrow \text{ri}(\Omega_1) \cap \text{ri}(\Omega_2) = \emptyset$$

**Example.** Consider $\Omega_1 = \{\bar{x}\}$ (singleton) and $\Omega_2 = \Omega$. Note that the proof proceeds in several steps.
