---
title: "Convex Sets and Convex Functions"
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
  - \newtheorem{corollary}{Corollary}[section]
  - \newtheorem{example}{Example}[section]
---

# Part I: Convex Sets

## 1. Definitions

**Definition 1.1 (Convex Set).** A subset Ω ⊆ ℝⁿ is **convex** if

$$\lambda a + (1 - \lambda)b \in \Omega \quad \text{whenever } a, b \in \Omega \text{ and } \lambda \in [0, 1].$$

**Definition 1.2 (Convex Combination).** Let $v_1, \ldots, v_m \in \mathbb{R}^n$ and $\lambda_1, \ldots, \lambda_m \in \mathbb{R}$ with $\sum_{i=1}^m \lambda_i = 1$. Then

$$x = \sum_{i=1}^{m} \lambda_i v_i$$

is called a **convex combination** of $v_1, \ldots, v_m$. A subset Ω is convex if and only if it contains all convex combinations of its elements.

**Proposition 1.3 (Characterization of Convex Sets).** A subset Ω of ℝⁿ is convex if and only if it contains all convex combinations of its elements.

*Proof.* (⇒) Assume Ω is convex. 

*Base case (n = 2):* $\lambda v_1 + (1 - \lambda)v_2 = \lambda_1 v_1 + \lambda_2 v_2 \in \Omega$ by definition.

*Inductive step:* Assume $\lambda_1 v_1 + \cdots + \lambda_k v_k \in \Omega$ for all $v_i \in \Omega$ and $\{\lambda_i\} \in \mathbb{R}$ where $\sum \lambda_i = 1$. Then for k + 1 terms:

$$\lambda_1 v_1 + \cdots + \lambda_k v_k + \lambda_{k+1} v_{k+1} = \sum_{i=1}^{k} \lambda_i \cdot v^* + \lambda_{k+1} v_{k+1} \in \Omega$$

where $v^* = \sum_{i=1}^{k} \frac{\lambda_i}{\sum_{j=1}^{k}\lambda_j} v_i \in \Omega$ by the induction hypothesis. 

(⇐) is obvious. ∎

## 2. Operations Preserving Convexity

**Proposition 1.4 (Cartesian Product).** The Cartesian product of convex sets is convex. That is, if $\Omega_1 \subseteq \mathbb{R}^m$ and $\Omega_2 \subseteq \mathbb{R}^n$ are convex, then $\Omega_1 \times \Omega_2 \subseteq \mathbb{R}^{m+n}$ is convex.

*Proof.* Let $x, y \in \Omega_1 \times \Omega_2$ where $x = (v_1, w_1)$ and $y = (v_2, w_2)$. Then for $\lambda \in (0, 1)$:

$$\lambda x + (1 - \lambda)y = \lambda(v_1, w_1) + (1 - \lambda)(v_2, w_2) = (\lambda v_1 + (1 - \lambda)v_2, \lambda w_1 + (1 - \lambda)w_2) = (v^*, w^*)$$

Since $\Omega_1$ and $\Omega_2$ are convex, $v^* \in \Omega_1$ and $w^* \in \Omega_2$, so $(v^*, w^*) \in \Omega_1 \times \Omega_2$. ∎

**Definition 1.5 (Affine Mapping).** A function $f: \mathbb{R}^m \to \mathbb{R}^n$ is an **affine mapping** if there exists $A \in \mathbb{R}^{n \times m}$ and $b \in \mathbb{R}^n$ such that $f(x) = Ax + b$.

**Proposition 1.6 (Convexity under Affine Transformations).** Convexity is preserved under affine transformations. If $f: \Omega \subseteq \mathbb{R}^m \to \mathbb{R}^n$ is an affine mapping, then:

(i) $f(\Omega)$ is convex

(ii) $f^{-1}(\Theta)$ is convex for any convex set Θ

*Proof.* Let $v, w \in f(\Omega)$. Then $v = f(x_1) = Ax_1 + b$ and $w = f(x_2) = Ax_2 + b$ for some $x_1, x_2 \in \Omega$. For $\lambda \in (0, 1)$:

$$\lambda v + (1 - \lambda)w = \lambda(Ax_1 + b) + (1 - \lambda)(Ax_2 + b) = A(\lambda x_1 + (1 - \lambda)x_2) + b = f(\lambda x_1 + (1 - \lambda)x_2)$$

By convexity of Ω, $\lambda x_1 + (1 - \lambda)x_2 \in \Omega$, so $\lambda v + (1 - \lambda)w \in f(\Omega)$. The proof for $f^{-1}(\Theta)$ is similar. ∎

**Proposition 1.7 (Intersection).** The intersection of convex sets is convex. If $\{\Omega_\alpha\}_{\alpha \in I}$ is a collection of convex sets, then $\bigcap_{\alpha \in I} \Omega_\alpha$ is convex.

*Proof.* Let $x, y \in \bigcap_{\alpha \in I} \Omega_\alpha$. Then for all $\lambda \in [0, 1]$, $v = \lambda x + (1 - \lambda)y \in \Omega_\alpha$ for each $\alpha \in I$ (since each $\Omega_\alpha$ is convex). Therefore $v \in \bigcap_\alpha \Omega_\alpha$. ∎

## 3. Convex Hull

**Definition 1.8 (Convex Hull).** The **convex hull** of a set Ω is defined as:

$$\text{co } \Omega := \bigcap\{C \mid C \text{ is convex and } \Omega \subseteq C\}$$

That is, co Ω is the intersection of all convex sets containing Ω.

**Proposition 1.9.** The convex hull of Ω is the smallest convex set containing Ω. In other words, $\text{co } \Omega \cap C = \text{co } \Omega$ for any convex set C containing Ω.

*Proof.* 

(i) The convex hull is convex (as an intersection of convex sets). 

(ii) Let C be any convex set containing Ω. By definition, $\text{co } \Omega \cap C = \text{co } \Omega$, hence $\text{co } \Omega \subseteq C$. ∎

**Proposition 1.10 (Representation of Convex Hull).** For any subset $\Omega \subseteq \mathbb{R}^n$, the convex hull has the representation:

$$\text{co } \Omega = \left\{\sum_{i=1}^{n} \lambda_i v_i \mid \sum_{i=1}^{n} \lambda_i = 1, \lambda_i \geq 0, v_i \in \Omega, n \in \mathbb{N}\right\}$$

In other words, the convex hull of Ω is the set of all convex combinations of the elements of Ω.

## 4. Topological Properties of Convex Sets

**Definition 1.11 (Interior Point).** Let Ω be a subset of ℝⁿ. A point $\omega \in \Omega$ is called an **interior point** of Ω if there exists $r > 0$ such that $B_r(\omega) \subseteq \Omega$, where $B_r(\omega)$ denotes the open ball of radius r centered at ω.

**Definition 1.12 (Interior and Closure).** The set of all interior points of Ω is called the **interior** of Ω and is denoted by $\text{int}(\Omega)$. The **closure** of Ω is the intersection of all closed sets of ℝⁿ that contain Ω, denoted $\overline{\Omega}$.

*Recall:* $b \in \overline{\Omega}$ iff $\forall r > 0$, $B_r(b) \cap \Omega \neq \emptyset$, which is equivalent to $\forall r > 0$, $\exists x \in \Omega$ s.t. $x \in B_r(b)$.

**Proposition 1.13 (Convexity of Interior and Closure).** The interior $\text{int}(\Omega)$ and the closure $\overline{\Omega}$ of a convex set $\Omega \subseteq \mathbb{R}^n$ are convex.

*Proof.* 

**(Closure)** Let $a, b \in \overline{\Omega}$ and $\lambda \in (0, 1)$. We want to show $\lambda a + (1 - \lambda)b \in \overline{\Omega}$. Since a, b are limit points, there exist sequences $\{a_i\} \subset \Omega \to a$ and $\{b_i\} \subset \Omega \to b$. Then $\lambda a_i + (1 - \lambda)b_i \in \Omega$ by convexity of Ω, and $\lambda a_i + (1 - \lambda)b_i \to \lambda a + (1 - \lambda)b$. Hence $\lambda a + (1 - \lambda)b \in \overline{\Omega}$.

**(Interior)** Take $a, b \in \text{int}(\Omega)$ and $\lambda \in (0, 1)$. There exists an open set V containing a such that $V \subseteq \Omega$. Then $\lambda a + (1 - \lambda)b \in \lambda V + (1 - \lambda)b$, which is open (since $\lambda[V + \frac{(1-\lambda)}{\lambda}b]$ is open in ℝⁿ). Moreover, $\lambda V + (1 - \lambda)b \subseteq \lambda\Omega + (1 - \lambda)\Omega = \Omega$. Thus $\lambda a + (1 - \lambda)b \in \text{int}(\Omega)$. ∎

**Definition 1.14 (Line Segment).** For $a, b \in \mathbb{R}^n$, define the line segment:

$$[a, b] = \{ta + (1 - t)b : 0 < t \leq 1\}$$

**Proposition 1.15 (Interior-Closure Line Segments).** Let $\Omega \subseteq \mathbb{R}^n$ be convex. If $a \in \text{int}(\Omega)$ and $b \in \overline{\Omega}$, then $[a, b) \subseteq \text{int}(\Omega)$.

*Proof.* Fix $a \in \text{int}(\Omega)$ and $b \in \overline{\Omega}$, with $0 < \lambda \leq 1$. Define $x_\lambda = \lambda a + (1 - \lambda)b$. For any $\varepsilon > 0$, $b \in \Omega + \varepsilon B$ (by definition of closure), where B is the open unit ball. Then:

$$x_\lambda + \varepsilon B \subseteq \lambda a + (1 - \lambda)(\Omega + \varepsilon B) + \varepsilon B = \lambda a + (1 - \lambda)\Omega + (2 - \lambda)\varepsilon B$$

Since $a \in \text{int}(\Omega)$, we can choose ε sufficiently small so that $a + \frac{(2-\lambda)}{\lambda}\varepsilon B \subseteq \Omega$. Then $x_\lambda + \varepsilon B \subseteq \lambda\left(\Omega + \frac{(1-\lambda)}{\lambda}\Omega\right) = \Omega$, proving $x_\lambda \in \text{int}(\Omega)$. ∎

**Proposition 1.16.** Let Ω be a convex subset of ℝⁿ with nonempty interior. Then:

(i) $\text{int}(\Omega) = \text{int}(\overline{\Omega})$

(ii) $\overline{\Omega} = \overline{\text{int}(\Omega)}$

*Proof.* 

**(i, ⇒)** Because $\text{int}(\Omega) \subseteq \Omega$ and $\text{int}(\Omega) \subseteq \overline{\Omega}$, we want to show that an element $b \in \overline{\Omega}$ is a limit point for $\text{int}(\Omega)$. In other words, $b \in \overline{\text{int}(\Omega)}$. Let $a \in \Omega$ and $b \in \overline{\Omega}$. Then let $V = \{\lambda_k a + (1 - \lambda_k)b \mid a \in \Omega, b \in \overline{\Omega}, \lambda_k = \frac{1}{k} \text{ for } k \in \mathbb{N}\}$.

We know from Proposition 1.15 that V is in the interior of Ω. In fact, the elements of V converge to b. In other words, $b$ is in the closure of $\text{int}(\Omega)$. Thus $\overline{\Omega} \subseteq \overline{\text{int}(\Omega)}$, and we are done.

**(ii)** If $a \in \text{int}(\Omega)$, then $\exists \varepsilon > 0$ s.t. $B_\varepsilon(a) \subseteq \text{int}(\Omega)$. If $b \in \overline{\Omega}$ and $\forall \varepsilon > 0$, $\exists a \in \Omega$ s.t. $a \in N_\varepsilon(b)$.

If $b \in \text{int}(\overline{\Omega})$, we can choose small $\varepsilon > 0$ such that $c = b + \varepsilon(b - a) \in \overline{\Omega}$. Then $b = \frac{\varepsilon}{1+\varepsilon}a + \frac{1}{1+\varepsilon}c$, i.e., b is a convex combination of a and c. Since $a \in \text{int}(\Omega)$ and $c \in \overline{\Omega}$, by previous proposition, $b \in \text{int}(\Omega)$. ∎

---

# Part II: Convex Functions

## 5. Definition and Basic Properties

**Definition 2.1 (Convex Function).** Let $f: \mathbb{R}^n \to \overline{\mathbb{R}} := (-\infty, \infty]$. We say that f is **convex** if

$$f(\lambda x_1 + (1 - \lambda)x_2) \leq \lambda f(x_1) + (1 - \lambda)f(x_2)$$

for all $x_1, x_2 \in \mathbb{R}^n$ and $\lambda \in (0, 1)$.

**Definition 2.2 (Domain).** The **domain** of a function $f: \mathbb{R}^n \to \overline{\mathbb{R}}$ is given by:

$$\text{dom}(f) := \{x \in \mathbb{R}^n \mid f(x) < \infty\}$$

**Definition 2.3 (Epigraph).** The **epigraph** of f is:

$$\text{epi}(f) = \{(x, \lambda) \in \mathbb{R}^n \times \mathbb{R} \mid \lambda \geq f(x)\}$$

**Example.** Let $f: \mathbb{R} \to (-\infty, \infty]$ be given by $f(x) = 0$ if $|x| \leq 1$ and $f(x) = \infty$ otherwise. Then $\text{dom}(f) = [-1, 1]$ and $\text{epi}(f) = [-1, 1] \times [0, \infty)$.

**Proposition 2.4 (Domain Convexity).** Let $f: \mathbb{R}^n \to \overline{\mathbb{R}}$ be a function.

(i) If f is convex, then $\text{dom}(f)$ is a convex set in ℝⁿ.

(ii) f is convex iff $f(\lambda x_1 + (1 - \lambda)x_2) \leq \lambda f(x_1) + (1 - \lambda)f(x_2)$ for all $x_1, x_2 \in \text{dom}(f)$ and $\lambda \in (0, 1)$.

*Proof.* (i) Suppose f is convex. Fix $x_1, x_2 \in \text{dom}(f)$ and $\lambda \in (0, 1)$. Since $x_1, x_2 \in \text{dom}(f)$, we have $f(x_1) < \infty$ and $f(x_2) < \infty$. Then $f(\lambda x_1 + (1 - \lambda)x_2) \leq \lambda f(x_1) + (1 - \lambda)f(x_2) < \infty$, so $\lambda x_1 + (1 - \lambda)x_2 \in \text{dom}(f)$. ∎

## 6. Geometric Characterization

**Proposition 2.5 (Epigraph Characterization).** Let $f: \mathbb{R}^n \to \overline{\mathbb{R}}$. Then f is convex if and only if $\text{epi}(f)$ is a convex set in $\mathbb{R}^{n+1}$.

*Proof.* 

**(⇒)** Suppose f is convex. Let us show $\text{epi}(f) \subseteq \mathbb{R}^{n+1}$ is convex using convexity of sets. Fix $(x_1, y_1), (x_2, y_2) \in \text{epi}(f)$ and $\lambda \in (0, 1)$. 

By definition of $\text{epi}(f)$, we have $f(x_1) \leq y_1$ and $f(x_2) \leq y_2$. By the definition of convexity of f:

$$f(\lambda x_1 + (1 - \lambda)x_2) \leq \lambda f(x_1) + (1 - \lambda)f(x_2) \leq \lambda y_1 + (1 - \lambda)y_2$$

Since f is convex, $\lambda x_1 + (1 - \lambda)x_2 \in \text{dom}(f)$. Furthermore, $\lambda y_1 + (1 - \lambda)y_2 \geq f(\lambda x_1 + (1 - \lambda)x_2)$ implies that:

$$(\lambda x_1 + (1 - \lambda)x_2, \lambda y_1 + (1 - \lambda)y_2) = \lambda(x_1, y_1) + (1 - \lambda)(x_2, y_2) \in \text{epi}(f)$$

Hence $\text{epi}(f)$ is convex.

**(⇐)** Suppose $\text{epi}(f)$ is convex. For $x_1, x_2 \in \text{dom}(f)$ and $\lambda \in (0, 1)$, we have $(x_1, f(x_1)), (x_2, f(x_2)) \in \text{epi}(f)$. By convexity of $\text{epi}(f)$:

$$(\lambda x_1 + (1-\lambda)x_2, \lambda f(x_1) + (1-\lambda)f(x_2)) \in \text{epi}(f)$$

Therefore $f(\lambda x_1 + (1-\lambda)x_2) \leq \lambda f(x_1) + (1-\lambda)f(x_2)$. ∎

## 7. Operations Preserving Convexity

**Proposition 2.6.** Let $f, f_1, \ldots, f_m: \mathbb{R}^n \to \overline{\mathbb{R}}$ be convex for $i = 1, \ldots, m$. Then the following functions are convex:

(i) $\lambda f$ where $\lambda > 0$ is constant

(ii) $\sum_{i=1}^{m} f_i$

(iii) $\max\{f_1, \ldots, f_m\}$

*Proof of (iii).* Suppose $f_1$ and $f_2$ are convex. Let $g(x) := \max\{f_1(x), f_2(x)\}$. Fix $x, y \in \mathbb{R}^n$ and $\lambda \in (0, 1)$. Then:

$$g(\lambda x + (1 - \lambda)y) = \max\{f_1(\lambda x + (1 - \lambda)y), f_2(\lambda x + (1 - \lambda)y)\}$$
$$\leq \max\{\lambda f_1(x) + (1 - \lambda)f_1(y), \lambda f_2(x) + (1 - \lambda)f_2(y)\}$$
$$\leq \lambda \max\{f_1(x), f_2(x)\} + (1 - \lambda) \max\{f_1(y), f_2(y)\}$$
$$= \lambda g(x) + (1 - \lambda)g(y)$$

(This also follows from the epigraph argument.) ∎

**Proposition.** The composition of convex functions is convex under certain conditions.

## 8. Jensen's Inequality

**Theorem 2.7 (Jensen's Inequality).** Let $f: \mathbb{R}^n \to \overline{\mathbb{R}}$ be convex. Then f is convex if and only if for all $\lambda_i \geq 0$ ($i = 1, \ldots, m$) and elements $x_i \in \mathbb{R}^n$ ($i = 1, \ldots, m$) with $\sum_{i=1}^{m} \lambda_i = 1$ ($m \in \mathbb{N}$), we have:

$$f\left(\sum_{i=1}^{m} \lambda_i x_i\right) = f(\lambda_1 x_1 + \cdots + \lambda_m x_m) \leq \lambda_1 f(x_1) + \cdots + \lambda_m f(x_m) = \sum_{i=1}^{m} \lambda_i f(x_i)$$

*Proof.* By induction, similar to proving sets are convex iff they contain every convex combination of their elements. The (⇐) direction is obvious. (Can also use the epigraph argument since $\text{epi}(f)$ is convex.) ∎

## 9. Properties of Convex Functions on Intervals

**Proposition 2.8 (Three-Slope Inequality).** Let $I \subseteq \mathbb{R}$ be an interval and let $f: I \to \mathbb{R}$ be a convex function. Then for all $a, b \in I$ and $a < x < b$, we have:

$$\frac{f(x) - f(a)}{x - a} \leq \frac{f(b) - f(a)}{b - a} \leq \frac{f(b) - f(x)}{b - x}$$

*Proof.* Fix $a, b \in I$ and $x \in (a, b)$. Take $\lambda = \frac{b - x}{b - a}$. Then $x = \lambda a + (1 - \lambda)b$, and by convexity, $f(x) \leq \lambda f(a) + (1 - \lambda)f(b)$. Rearranging gives the inequalities. ∎

## 10. Differentiable Convex Functions

**Theorem 2.9 (First Derivative Test).** Let $I \subseteq \mathbb{R}$ be an open interval and let $f: I \to \mathbb{R}$ be a differentiable function. Then f is convex iff $f'$ is non-decreasing on I.

*Proof.* 

**(⇒)** We prove by contradiction. Suppose $f'$ is not non-decreasing on I. Then there exists some interval $[a, b] \subseteq I$ where f is decreasing. Since f is continuous and differentiable, we can find $x_1 \in (a, b)$ such that:

$$f'(x_1) = \frac{f(b) - f(a)}{b - a}$$

by the Mean Value Theorem. Similarly, we can find $x_2 \in (a, x_1)$ with:

$$f'(x_2) = \frac{f(x_1) - f(a)}{x_1 - a}$$

By assumption, $f'(x_2) > f'(x_1)$. But by the previous proposition:

$$f'(x_2) = \frac{f(x_1) - f(a)}{x_1 - a} \leq \frac{f(b) - f(a)}{b - a} = f'(x_1)$$

a contradiction.

**(⇐)** Ideas: (i) $x_1 < c_1 < x_t < c_2 < x_2$, (ii) $x_t = \frac{x_t - x_1}{x_2 - x_1}x_2 + \frac{x_2 - x_t}{x_2 - x_1}x_1$. Use the Mean Value Theorem. ∎

**Corollary 2.10 (Second Derivative Test).** Let I be an open interval of ℝ and let $f: I \to \mathbb{R}$ be a twice differentiable function. Then f is convex on I iff $f''(x) \geq 0$ for all $x \in I$.

*Proof.* $f''(x) = \lim_{h \to 0} \frac{f'(x + h) - f'(x)}{h} \geq 0$ by Theorem 2.9 since $f'$ is non-decreasing. ∎

### Examples

(a) $f(x) = e^{ax}$, $x \in \mathbb{R}$: $f'(x) = ae^{ax}$, $f''(x) = a^2 e^{ax} \geq 0$. So f is convex by Corollary 2.10.

(b) $f(x) = -\ln(x)$, $x \in (0, \infty)$: $f'(x) = -\frac{1}{x}$, $f''(x) = \frac{1}{x^2} \geq 0$. So f is convex.

(c) $f(x) = x^p$, $x \in (0, \infty)$, $p \geq 1$: $f'(x) = px^{p-1}$, $f''(x) = p(p-1)x^{p-2} \geq 0$ when $p \geq 1$. So f is convex.

---

# Part III: Distance Functions and Projections

## 11. Distance Functions

**Definition 3.1 (Distance Function).** Let Ω be a nonempty subset of ℝⁿ. For any $x \in \mathbb{R}^n$, define:

$$d(x; \Omega) = \inf\{\|x - y\| \mid y \in \Omega\}$$

The function $f(x) = d(x; \Omega)$ is called the **distance function** associated with Ω.

**Proposition 3.2.** Let Ω be a nonempty subset of ℝⁿ. Then:

(i) $d(x; \Omega) = 0$ iff $x \in \overline{\Omega}$. In particular, if Ω is closed, then $d(x; \Omega) = 0$ iff $x \in \Omega$.

(ii) $|d(x; \Omega) - d(y; \Omega)| \leq \|x - y\|$ for all $x, y \in \mathbb{R}^n$ (Lipschitz continuity with constant 1).

*Proof.* 

**(i, ⇒)** Suppose $d(x; \Omega) = \inf\{\|x - y\| \mid y \in \Omega\} = 0$. Then for any $k \in \mathbb{N}$:

$$0 \leq \|x - y_k\| < \inf\{\|x - y\| \mid y \in \Omega\} + \frac{1}{k} = \frac{1}{k}$$

So $y_k \to x$ as $k \to \infty$. Since $\{y_k\} \subset \Omega$, $x \in \overline{\Omega}$.

**(i, ⇐)** Suppose $x \in \overline{\Omega}$. Then there exists a sequence $\{y_k\} \to x$ as $k \to \infty$. Then:

$$0 \leq d(x; \Omega) \leq \inf\{\|x - y_k\| \mid y_k \in \Omega\} \leq \|x - y_k\| < \varepsilon$$

for all $\varepsilon > 0$. Thus $d(x; \Omega) = 0$.

**(ii)** Fix any $x, y \in \mathbb{R}^n$. For any $u \in \Omega$:

$$d(x; \Omega) \leq \|x - u\| \leq \|x - y\| + \|y - u\|$$

Then:

$$d(x; \Omega) \leq \|x - y\| + \inf\{\|y - u\| \mid u \in \Omega\} = \|x - y\| + d(y; \Omega)$$

So $d(x; \Omega) - d(y; \Omega) \leq \|x - y\|$. Similarly, $d(y; \Omega) - d(x; \Omega) \leq \|x - y\|$. Therefore $|d(x; \Omega) - d(y; \Omega)| \leq \|x - y\|$. ∎

## 12. Euclidean Projection

**Definition 3.3 (Euclidean Projection).** Let $\Omega \subseteq \mathbb{R}^n$. For any $x \in \mathbb{R}^n$, the **Euclidean projection** from x to Ω is defined by:

$$\pi(x; \Omega) = \{y \in \Omega \mid \|x - y\| = d(x; \Omega)\}$$

**Example.** Let $\Omega = B(a; r) \subseteq \mathbb{R}^n$ (closed ball of radius r centered at a). For any $x \in \mathbb{R}^n$:

$$d(x; \Omega) = \max\{\|x - a\| - r, 0\}$$

and $\pi(x; \Omega) = \{x\}$ if $\|x - a\| \leq r$, otherwise $\pi(x; \Omega) = \{a + r\frac{x - a}{\|x - a\|}\}$. The projection is always a singleton.

**Example.** Let $\Omega \subset \mathbb{R}^n$ be given by $\Omega = \{u = (u_1, \ldots, u_n) \in \mathbb{R}^n \mid |u_i| \leq 1\}$ (the hypercube). Then:

$$\pi(x; \Omega) = \{u\}$$

where $u = (u_1, \ldots, u_n)$ is given by:

$$u_i = \begin{cases} x_i & \text{if } |x_i| \leq 1 \\ 1 & \text{if } x_i > 1 \\ -1 & \text{if } x_i < -1 \end{cases}$$

**Proposition 3.4 (Existence of Projection).** Let $\Omega \subseteq \mathbb{R}^n$ be a nonempty closed set. Then $\pi(x; \Omega) \neq \emptyset$ for all $x \in \mathbb{R}^n$.

*Proof.* Want to prove $\pi(x; \Omega)$ is not empty. This is an existence proof, so we consider limits, least upper bounds.

Since $d(x; \Omega) = \inf\{\|x - y\| \mid y \in \Omega\}$, we look at $x \notin \overline{\Omega}$ (which means $\exists \delta > 0$ such that $N_\delta(x) \cap \overline{\Omega} = \emptyset$).

$d(x; \Omega)$ is the greatest lower bound, meaning for all $y \in \Omega$, $d(x; \Omega) \leq \|x - y\|$. In particular:

$$d(x; \Omega) \leq \|x - y_k\| \leq d(x; \Omega) + \frac{1}{k}$$

for some sequence $\{y_k\} \subset \Omega$. So $\|y_k\| \leq \|x\| + d(x; \Omega) + 1$ for all $k \in \mathbb{N}$. The sequence $\{y_k\}$ is bounded, hence contains a convergent subsequence $\{y_{k_j}\} \to \bar{y}$.

Then $\|x - y_{k_j}\| \to d(x; \Omega)$ as $k_j \to \infty$, so $0 \leq \|x - \bar{y}\| - d(x; \Omega) \leq 0$. Thus $\|x - \bar{y}\| = d(x; \Omega)$, which means $\bar{y} \in \pi(x; \Omega)$. Since Ω is closed and $y_{k_j} \to \bar{y}$, we have $\bar{y} \in \Omega$. ∎

**Proposition 3.5 (Uniqueness of Projection).** Let $\Omega \subseteq \mathbb{R}^n$ be closed and convex. Then for all $x \in \mathbb{R}^n$, $\pi(x; \Omega)$ is a singleton.

*Proof.* By contradiction. Suppose there exist $\omega_1, \omega_2 \in \Omega$ with $\omega_1 \neq \omega_2$ such that $\|x - \omega_1\| = d(x; \Omega) = \|x - \omega_2\|$.

By the Parallelogram Equality:

$$2\|x - \omega_1\|^2 + \|x - \omega_2\|^2 = \frac{1}{2}\left[\|2x - (\omega_1 + \omega_2)\|^2 + \|\omega_1 - \omega_2\|^2\right]$$

This simplifies to:

$$\left\|x - \frac{\omega_1 + \omega_2}{2}\right\|^2 = \|x - \omega_1\|^2 - \frac{\|\omega_1 - \omega_2\|^2}{4} = d(x; \Omega)^2 - \frac{\|\omega_1 - \omega_2\|^2}{4} < d(x; \Omega)^2$$

But this is a contradiction since $\frac{\omega_1 + \omega_2}{2} \in \Omega$ by convexity, yet its distance to x is less than $d(x; \Omega)$. ∎

**Proposition 3.6 (Characterization of Projection).** Let Ω be a nonempty convex subset of ℝⁿ and let $\bar{x} \in \mathbb{R}^n$, $\bar{\omega} \in \overline{\Omega}$. Then $\bar{\omega} \in \pi(\bar{x}; \Omega)$ iff $\langle \bar{x} - \bar{\omega}, \omega - \bar{\omega} \rangle \leq 0$ for all $\omega \in \Omega$.

*Proof.* For $\bar{\omega} \in \pi(\bar{x}; \Omega)$ and $\omega \in \Omega$, since $\lambda\bar{\omega} + (1 - \lambda)\omega \in \Omega$ by convexity (for $\lambda > 0$):

$$\|\bar{x} - \bar{\omega}\|^2 \leq \|\bar{x} - (\lambda\bar{\omega} + (1 - \lambda)\omega)\|^2 = \|\bar{x} - \bar{\omega} - \lambda(\bar{\omega} - \omega)\|^2$$

Expanding:

$$= ((\bar{x} - \bar{\omega}) - \lambda(\omega - \bar{\omega}))^T((\bar{x} - \bar{\omega}) - \lambda(\omega - \bar{\omega}))$$
$$= \|\bar{x} - \bar{\omega}\|^2 - 2\lambda(\bar{x} - \bar{\omega})^T(\omega - \bar{\omega}) + \lambda^2\|\omega - \bar{\omega}\|^2$$

This gives $(\bar{x} - \bar{\omega})^T(\omega - \bar{\omega}) \leq \frac{\lambda}{2}\|\omega - \bar{\omega}\|^2$. Since this holds for all $\lambda > 0$, we get $\langle \bar{x} - \bar{\omega}, \omega - \bar{\omega} \rangle \leq 0$. ∎

**Proposition 3.7 (Convexity of Distance Function).** Let $\Omega \subseteq \mathbb{R}^n$ be nonempty and closed. Then $d(x; \Omega)$ is convex iff Ω is convex.

*Proof (⇐).* Assume Ω is convex. Fix any $x_1, x_2 \in \mathbb{R}^n$ and $\lambda \in (0, 1)$. We will show:

$$f(\lambda x_1 + (1 - \lambda)x_2) \leq \lambda f(x_1) + (1 - \lambda)f(x_2)$$

where $f(x) = d(x; \Omega)$. (The proof uses the characterization from Proposition 3.6 and properties of the projection onto convex sets.) ∎
