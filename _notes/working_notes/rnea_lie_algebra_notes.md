---
layout: page
title: "Recursive Newton-Euler Algorithm: Lie Algebra Formulation"
---

# Recursive Newton-Euler Algorithm: Lie Algebra Formulation

*Working notes on the spatial dynamics formulation of RNEA*

---

## 1. Fundamental Definitions

### 1.1 Reference Frames

**Space Frame** — A fixed inertial frame $\{0\}$ (often denoted $\{S\}$)
- This frame doesn't move; it's our "world" reference
- All quantities can be expressed relative to this frame

**Body Frame** — A frame $\{i\}$ fixed at the body's center of mass
- Moves with the rigid body
- Convenient for expressing inertia properties (which are constant in this frame)

### 1.2 Homogeneous Transformations

$$T = (R, p) \in SE(3) \text{ — A frame transformation}$$

where:
- $R \in SO(3)$ is a 3×3 rotation matrix
- $p \in \mathbb{R}^3$ is a position vector

**Configuration notation:** $T_{sb}(t)$ — The configuration of $\{b\}$ seen from $\{s\}$ at time $t$

*Example:* If $v_b$ is resolved in $\{b\}$, then $R_{sb} v_b$ is now resolved in $\{s\}$.

### 1.3 Inter-link Transformations

**$M_{0,i}$** — Transform $\{i\}$ into $\{0\}$ when robot is in home configuration ($\theta_i = 0$ for all $i = 1, \ldots, n$)

**$M_{i,i+1}$** — Transform $\{i+1\}$ into $\{i\}$ when robot is in home configuration ($\theta_i = 0$ for all $i = 1, \ldots, n$)

**Relationship:**
$$M_{i,i+1} = (M_{0,i})^{-1} M_{0,i+1}$$

*This is just the composition rule: to go from frame $i+1$ to frame $i$, first go to frame $0$, then to frame $i$.*

---

## 2. Screw Theory Fundamentals

### 2.1 Screw Axes

**$\mathcal{S}_i$** — Screw axis of joint $i$ in **space coordinates** (frame $\{0\}$)

**$\mathcal{A}_i$** — Screw axis of joint $i$ in **body coordinates** (frame $\{i\}$)

**Conversion between them:**
$$\mathcal{A}_i = [Ad_{M_{0,i}^{-1}}] \mathcal{S}_i$$

*Intuition:* The Adjoint transformation $Ad_{M^{-1}}$ transforms spatial velocities from one frame to another. Here it takes the screw expressed in the space frame and re-expresses it in the body frame.

### 2.2 Twists and Wrenches

**Twist (spatial motion vector):**
$$\mathcal{V} = \begin{pmatrix} \omega \\ v \end{pmatrix} \in \mathbb{R}^6$$

where:
- $\omega \in \mathbb{R}^3$ is the angular velocity
- $v \in \mathbb{R}^3$ is the linear velocity

*Note: The ordering $(\omega, v)$ vs $(v, \omega)$ varies by textbook. We use $(\omega, v)$ here.*

**Wrench (spatial force vector):**
$$\mathcal{F} = \begin{pmatrix} n \\ f \end{pmatrix} \in \mathbb{R}^6$$

where:
- $n \in \mathbb{R}^3$ is the moment/torque
- $f \in \mathbb{R}^3$ is the linear force

*These come from the dual space $T^* T^{-1}$ — wrenches are dual to twists.*

---

## 3. Key Operators (Lie Brackets)

### 3.1 The Adjoint Transformation

The Adjoint $[Ad_T]$ transforms screws/twists between space and body frames:

$$[Ad_T] = \begin{pmatrix} R & 0 \\ [p]R & R \end{pmatrix}$$

where $[p]$ is the skew-symmetric matrix form of the vector $p$:
$$[p] = \begin{pmatrix} 0 & -p_3 & p_2 \\ p_3 & 0 & -p_1 \\ -p_2 & p_1 & 0 \end{pmatrix}$$

*Origin:* This comes from $\dot{T} T^{-1}$ — the body velocity.

**Property:** $[Ad_T]^{-1} = [Ad_{T^{-1}}]$

### 3.2 The Spatial Velocity Cross-Product (ad operator)

This is the **Lie bracket** for spatial velocities:

$$[ad_\mathcal{V}] = \begin{pmatrix} [\omega] & 0 \\ [v] & [\omega] \end{pmatrix}$$

*This operator appears when differentiating twists.*

### 3.3 The Spatial Force Cross-Product

For wrenches, the relevant operator is the transpose:

$$[ad_\mathcal{V}]^T = \begin{pmatrix} -[\omega] & -[v] \\ 0 & -[\omega] \end{pmatrix}$$

**Computing $ad_\mathcal{V}^T(\mathcal{F})$:**

$$ad_\mathcal{V}^T(\mathcal{F}) = [ad_\mathcal{V}]^T \mathcal{F} = \begin{pmatrix} [\omega] & 0 \\ [v] & [\omega] \end{pmatrix}^T \begin{pmatrix} n \\ f \end{pmatrix} = \begin{pmatrix} -[\omega] & -[v] \\ 0 & -[\omega] \end{pmatrix} \begin{pmatrix} n \\ f \end{pmatrix} = \begin{pmatrix} -[\omega]n - [v]f \\ -[\omega]f \end{pmatrix}$$

*This is the "co-adjoint" action — how forces transform under the Lie algebra action.*

---

## 4. The RNEA Algorithm (Compact Form)

### Inputs
- $\theta, \dot{\theta}, \ddot{\theta}$ — joint positions, velocities, accelerations
- $\mathcal{G}_i$ — spatial inertia matrix for link $i$
- $M_{0,i}$ — home configuration transforms
- $\mathcal{S}_i$ — screw axes in space frame

### Forward Iteration: $i = 1$ to $n_\theta$

**Step 1:** Compute body-frame screw axis
$$\mathcal{A}_i = [Ad_{M_{0,i}^{-1}}] \mathcal{S}_i$$

**Step 2:** Compute transform from link $i$ to link $i-1$
$$T_{i,i+1} = \exp(-[\mathcal{A}_i]\theta_i) M_{i,i+1}$$

*Note: $T_{i,i+1}$ expresses $\{i+1\}$ in $\{i\}$. The negative sign in the exponential handles the direction convention.*

**Step 3:** Propagate velocity
$$\mathcal{V}_i = Ad_{T_{i,i+1}}(\mathcal{V}_{i-1}) + \mathcal{A}_i \dot{\theta}_i$$

*Explanation: The velocity of link $i$ equals:*
- *The velocity of link $i-1$ transformed to frame $i$*
- *Plus the velocity contribution from joint $i$*

**Step 4:** Propagate acceleration
$$\dot{\mathcal{V}}_i = Ad_{T_{i,i+1}}(\dot{\mathcal{V}}_{i-1}) + ad_{\mathcal{V}_i}(\mathcal{A}_i)\dot{\theta}_i + \mathcal{A}_i \ddot{\theta}_i$$

*Explanation:*
- *First term: Acceleration from parent link*
- *Second term: Coriolis/centripetal effects (velocity-dependent)*
- *Third term: Direct joint acceleration contribution*

### Backward Iteration: $i = n_\theta$ to $1$

**Step 5:** Compute wrench on link $i$
$$\mathcal{F}_i = Ad_{T_{i+1,i}}^T(\mathcal{F}_{i+1}) + \mathcal{G}_i \dot{\mathcal{V}}_i - ad_{\mathcal{V}_i}^T(\mathcal{G}_i \mathcal{V}_i)$$

*Explanation:*
- *First term: Wrench transmitted from child link*
- *Second term: Inertial forces from acceleration*
- *Third term: Velocity-dependent (Coriolis) forces*

**Step 6:** Extract joint torque
$$\tau_i = \mathcal{F}_i^T \mathcal{A}_i$$

*This projects the wrench onto the joint axis to get the scalar torque.*

---

## 5. Expanding the Algorithm (Matrix Form)

### 5.1 The Transform Matrix

$$T_{i,i+1} = \begin{pmatrix} R_{i,i+1} & p_{i,i+1} \\ 0 & 1 \end{pmatrix}$$

### 5.2 The Screw Axis

For a revolute joint with axis $z_i$ and offset $b_i$:
$$\mathcal{A}_i = \begin{pmatrix} z_i \\ b_i \end{pmatrix}$$

*For a revolute joint, $z_i$ is typically a unit vector along the joint axis, and $b_i = -z_i \times r$ where $r$ is the position of a point on the axis.*

### 5.3 Velocity Propagation Expanded

$$\mathcal{V}_i = Ad_{T_{i,i+1}}(\mathcal{V}_{i-1}) + \mathcal{A}_i \dot{\theta}_i$$

Expanding with the Adjoint:
$$= \begin{pmatrix} R_{i,i+1} & 0 \\ [p_{i,i+1}]R_{i,i+1} & R_{i,i+1} \end{pmatrix} \begin{pmatrix} \omega_{i-1} \\ v_{i-1} \end{pmatrix} + \dot{\theta}_i \begin{pmatrix} z_i \\ b_i \end{pmatrix}$$

This gives us the **component equations:**

$$\boxed{\omega_i = R_{i,i+1} \omega_{i-1} + \dot{\theta}_i z_i}$$

$$\boxed{v_i = [p_{i,i+1}] R_{i,i+1} \omega_{i-1} + R_{i,i+1} v_{i-1} + \dot{\theta}_i b_i}$$

**Notation:** We often write $R_i \equiv R_{i,i+1}$ for brevity. Note that $R_i^{-1} = R_i^T = R_{i+1,i}$.

### 5.4 Acceleration Propagation Expanded

$$\dot{\mathcal{V}}_i = Ad_{T_{i,i+1}}(\dot{\mathcal{V}}_{i-1}) + ad_{\mathcal{V}_i}(\mathcal{A}_i)\dot{\theta}_i + \mathcal{A}_i \ddot{\theta}_i$$

The middle term expands as:
$$ad_{\mathcal{V}_i}(\mathcal{A}_i)\dot{\theta}_i = \begin{pmatrix} [\omega_i] & 0 \\ [v_i] & [\omega_i] \end{pmatrix} \begin{pmatrix} z_i \\ b_i \end{pmatrix} \dot{\theta}_i = \dot{\theta}_i \begin{pmatrix} [\omega_i]z_i \\ [v_i]z_i + [\omega_i]b_i \end{pmatrix}$$

Full expansion:
$$\begin{pmatrix} \dot{\omega}_i \\ \dot{v}_i \end{pmatrix} = \begin{pmatrix} R_{i,i+1} & 0 \\ [p_{i,i+1}]R_{i,i+1} & R_{i,i+1} \end{pmatrix} \begin{pmatrix} \dot{\omega}_{i-1} \\ \dot{v}_{i-1} \end{pmatrix} + \dot{\theta}_i \begin{pmatrix} [\omega_i]z_i \\ [v_i]z_i + [\omega_i]b_i \end{pmatrix} + \ddot{\theta}_i \begin{pmatrix} z_i \\ b_i \end{pmatrix}$$

**Component equations:**

$$\boxed{\dot{\omega}_i = R_{i,i+1} \dot{\omega}_{i-1} + \dot{\theta}_i [\omega_i] z_i + \ddot{\theta}_i z_i}$$

$$\boxed{\dot{v}_i = [p_{i,i+1}]R_{i,i+1} \dot{\omega}_{i-1} + R_{i,i+1} \dot{v}_{i-1} + \dot{\theta}_i ([v_i]z_i + [\omega_i]b_i) + \ddot{\theta}_i b_i}$$

---

## 6. Backward Iteration Expanded

### 6.1 The Spatial Inertia Matrix

$$\mathcal{G}_i = \begin{pmatrix} I_i & 0 \\ 0 & m_i I_3 \end{pmatrix}$$

where:
- $I_i$ is the 3×3 rotational inertia (about CoM, in body frame)
- $m_i$ is the mass
- $I_3$ is the 3×3 identity matrix

*Note: This assumes the body frame is at the center of mass. If not, there are off-diagonal coupling terms.*

### 6.2 Wrench Equation Expanded

$$\mathcal{F}_i = Ad_{T_{i+1,i}}^T(\mathcal{F}_{i+1}) + \mathcal{G}_i \dot{\mathcal{V}}_i - ad_{\mathcal{V}_i}^T(\mathcal{G}_i \mathcal{V}_i)$$

**Term 1: Force from child link**
$$Ad_{T_{i+1,i}}^T(\mathcal{F}_{i+1}) = \begin{pmatrix} R_{i+1,i} & [p_{i+1,i}]R_{i+1,i} \\ 0 & R_{i+1,i} \end{pmatrix} \begin{pmatrix} n_{i+1} \\ f_{i+1} \end{pmatrix} = \begin{pmatrix} R_{i+1,i} n_{i+1} + [p_{i+1,i}]R_{i+1,i} f_{i+1} \\ R_{i+1,i} f_{i+1} \end{pmatrix}$$

**Term 2: Inertial forces**
$$\mathcal{G}_i \dot{\mathcal{V}}_i = \begin{pmatrix} I_i & 0 \\ 0 & m_i I_3 \end{pmatrix} \begin{pmatrix} \dot{\omega}_i \\ \dot{v}_i \end{pmatrix} = \begin{pmatrix} I_i \dot{\omega}_i \\ m_i \dot{v}_i \end{pmatrix}$$

**Term 3: Velocity-dependent forces**

First compute $\mathcal{G}_i \mathcal{V}_i$:
$$\mathcal{G}_i \mathcal{V}_i = \begin{pmatrix} I_i \omega_i \\ m_i v_i \end{pmatrix}$$

Then apply $ad_{\mathcal{V}_i}^T$:
$$ad_{\mathcal{V}_i}^T(\mathcal{G}_i \mathcal{V}_i) = \begin{pmatrix} -[\omega_i] & -[v_i] \\ 0 & -[\omega_i] \end{pmatrix} \begin{pmatrix} I_i \omega_i \\ m_i v_i \end{pmatrix} = \begin{pmatrix} -[\omega_i] I_i \omega_i - m_i [v_i] v_i \\ -m_i [\omega_i] v_i \end{pmatrix}$$

*Note: $[v_i]v_i = v_i \times v_i = 0$, so this simplifies!*

**Final component equations:**

$$\boxed{n_i = R_{i+1,i} n_{i+1} + [p_{i+1,i}]R_{i+1,i} f_{i+1} + I_i \dot{\omega}_i + [\omega_i] I_i \omega_i + m_i [v_i] v_i}$$

Since $[v_i]v_i = 0$:
$$\boxed{n_i = R_{i+1,i} n_{i+1} + [p_{i+1,i}]R_{i+1,i} f_{i+1} + I_i \dot{\omega}_i + [\omega_i] I_i \omega_i}$$

$$\boxed{f_i = R_{i+1,i} f_{i+1} + m_i \dot{v}_i + m_i [\omega_i] v_i}$$

*Physical interpretation of the $f_i$ equation:*
- $R_{i+1,i} f_{i+1}$: Force transmitted from child
- $m_i \dot{v}_i$: Inertial force (F = ma)
- $m_i [\omega_i] v_i = m_i (\omega_i \times v_i)$: Coriolis force

---

## 7. Summary: Forward and Backward Iteration

### Forward Iteration (compute velocities and accelerations)

$$\omega_i = R_{i,i+1} \omega_{i-1} + \dot{\theta}_i z_i$$

$$v_i = [p_{i,i+1}] R_{i,i+1} \omega_{i-1} + R_{i,i+1} v_{i-1} + \dot{\theta}_i b_i$$

$$\dot{\omega}_i = R_{i,i+1} \dot{\omega}_{i-1} + \dot{\theta}_i [\omega_i] z_i + \ddot{\theta}_i z_i$$

$$\dot{v}_i = [p_{i,i+1}]R_{i,i+1} \dot{\omega}_{i-1} + R_{i,i+1} \dot{v}_{i-1} + \dot{\theta}_i ([v_i]z_i + [\omega_i]b_i) + \ddot{\theta}_i b_i$$

### Backward Iteration (compute forces and torques)

$$n_i = R_{i+1,i} n_{i+1} + [p_{i+1,i}]R_{i+1,i} f_{i+1} + I_i \dot{\omega}_i + [\omega_i] I_i \omega_i + m_i [v_i] v_i$$

$$f_i = R_{i+1,i} f_{i+1} + m_i \dot{v}_i + m_i [\omega_i] v_i$$

### Joint Torque

$$\tau_i = \mathcal{F}_i^T \mathcal{A}_i = n_i^T z_i + f_i^T b_i$$

---

## 8. Example: 2-Link Robot

Let's expand everything for a 2-link planar robot with base frame $\{0\}$.

### 8.1 Forward Iteration

**Link 1 ($i = 1$):**

$$\omega_1 = R_{10} \omega_0 + \dot{\theta}_1 z_1$$

$$v_1 = [p_{10}] R_{10} \omega_0 + R_{10} v_0 + \dot{\theta}_1 b_1$$

$$\dot{\omega}_1 = R_{10} \dot{\omega}_0 + \dot{\theta}_1 [\omega_1] z_1 + \ddot{\theta}_1 z_1$$

$$\dot{v}_1 = [p_{10}] R_{10} \dot{\omega}_0 + R_{10} \dot{v}_0 + \dot{\theta}_1 ([v_1] z_1 + [\omega_1] b_1) + \ddot{\theta}_1 b_1$$

**Link 2 ($i = 2$):**

$$\omega_2 = R_{21} \omega_1 + \dot{\theta}_2 z_2$$

$$v_2 = [p_{21}] R_{21} \omega_1 + R_{21} v_1 + \dot{\theta}_2 b_2$$

$$\dot{\omega}_2 = R_{21} \dot{\omega}_1 + \dot{\theta}_2 [\omega_2] z_2 + \ddot{\theta}_2 z_2$$

$$\dot{v}_2 = [p_{21}] R_{21} \dot{\omega}_1 + R_{21} \dot{v}_1 + \dot{\theta}_2 ([v_2] z_2 + [\omega_2] b_2) + \ddot{\theta}_2 b_2$$

### 8.2 Backward Iteration

**Link 2 ($i = 2$):** (assuming $f_3 = n_3 = 0$, no external load)

$$n_2 = R_{32} n_3 + [p_{32}] R_{32} f_3 + I_2 \dot{\omega}_2 + [\omega_2] I_2 \omega_2 + m_2 [v_2] v_2$$

$$f_2 = R_{32} f_3 + m_2 \dot{v}_2 + m_2 [\omega_2] v_2$$

**Link 1 ($i = 1$):**

$$n_1 = R_{21} n_2 + [p_{21}] R_{21} f_2 + I_1 \dot{\omega}_1 + [\omega_1] I_1 \omega_1 + m_1 [v_1] v_1$$

$$f_1 = R_{21} f_2 + m_1 \dot{v}_1 + m_1 [\omega_1] v_1$$

**Joint Torques:**

$$\tau_2 = n_2^T z_2 + f_2^T b_2$$

$$\tau_1 = n_1^T z_1 + f_1^T b_1$$

### 8.3 Simplifications: Zero Terms

**For a fixed-base robot, the base has:**
- $\omega_0 = 0$ (no angular velocity)
- $\dot{\omega}_0 = 0$ (no angular acceleration)
- $v_0 = 0$ (no linear velocity of base)
- But: $\dot{v}_0 = -g$ if we want gravity! (trick to include gravity)

**For an end-effector with no external load:**
- $n_3 = 0$
- $f_3 = 0$

**Cross products with parallel vectors vanish:**
- $z_i \times z_i = 0$
- $b_i \times z_i = 0$ if $b_i \parallel z_i$
- $z_i \times b_i = 0$ if $b_i \parallel z_i$

---

## 9. Identifying Velocity-Quadratic Terms

The equations of motion have the form:
$$\tau = M(\theta)\ddot{\theta} + C(\theta, \dot{\theta})\dot{\theta} + g(\theta)$$

where:
- $M(\theta)\ddot{\theta}$: Inertia terms (linear in $\ddot{\theta}$)
- $C(\theta, \dot{\theta})\dot{\theta}$: Coriolis/centripetal (quadratic in $\dot{\theta}$)
- $g(\theta)$: Gravity (constant in $\dot{\theta}$)

### 9.1 Tracking Terms Through the Algorithm

Let's mark each term by its order in velocity:
- **First appearance**: Terms linear in $\dot{\theta}$
- **Second or later**: Terms quadratic in $\dot{\theta}$

**Link 1 kinematics (all linear in $\dot{\theta}$):**

| Variable | Expression | Order in $\dot{\theta}$ |
|----------|------------|------------------------|
| $\omega_1$ | $\dot{\theta}_1 z_1$ | 1 |
| $v_1$ | $\dot{\theta}_1 b_1$ | 1 |
| $\dot{\omega}_1$ | $0$ (if $\ddot{\theta}_1 = 0$) | — |
| $\dot{v}_1$ | $0$ (if $\ddot{\theta}_1 = 0$) | — |

**Link 2 kinematics:**

| Variable | Expression | Order in $\dot{\theta}$ |
|----------|------------|------------------------|
| $\omega_2$ | $R_{21}\omega_1 + \dot{\theta}_2 z_2$ | 1 |
| $v_2$ | $[p_{21}]R_{21}\omega_1 + R_{21}v_1 + \dot{\theta}_2 b_2$ | 1 |
| $\dot{\omega}_2$ | $0$ (if $\ddot{\theta} = 0$) | — |
| $\dot{v}_2$ | $\dot{\theta}_2([v_2]z_2 + [\omega_2]b_2)$ | **2** |

*Key insight:* $\dot{v}_2$ contains $\dot{\theta}_2 \cdot [\omega_2]b_2$ where $\omega_2 \propto \dot{\theta}$, giving a $\dot{\theta}^2$ term!

### 9.2 Quadratic Terms in $\dot{v}_2$

$$\dot{v}_2 = \dot{\theta}_2 ([v_2] z_2 + [\omega_2] b_2)$$

Since $\omega_2 = R_{21}\omega_1 + \dot{\theta}_2 z_2$ and $v_2 \propto \dot{\theta}$, we get:
$$\dot{v}_2 = \dot{\theta}_2 ([\text{linear in } \dot{\theta}] z_2 + [\text{linear in } \dot{\theta}] b_2)$$

This is **quadratic in velocities** — these are the **Coriolis/centripetal terms**.

---

## 10. Detailed Calculations

### 10.1 Calculating $n_2$

$$n_2 = \omega_2 \times I_2 \omega_2$$

Expanding $\omega_2 = R_{21}\omega_1 + \dot{\theta}_2 z_2$:

$$n_2 = (R_{21}\omega_1 + \dot{\theta}_2 z_2) \times I_2(R_{21}\omega_1 + \dot{\theta}_2 z_2)$$

$$= R_{21}\omega_1 \times I_2 R_{21}\omega_1 + R_{21}\omega_1 \times \dot{\theta}_2 z_2 + \dot{\theta}_2 z_2 \times I_2 R_{21}\omega_1 + \dot{\theta}_2^2 (z_2 \times z_2)$$

Since $z_2 \times z_2 = 0$:

$$= \dot{\theta}_1^2 (R_{21} z_1 \times I_2 R_{21} z_1) + \dot{\theta}_1 \dot{\theta}_2 (R_{21} z_1 \times z_2) + \dot{\theta}_1 \dot{\theta}_2 (z_2 \times I_2 R_{21} z_1)$$

*All terms are quadratic in $\dot{\theta}$!*

### 10.2 Calculating $f_2$

$$f_2 = m_2(\dot{v}_2 + \omega_2 \times v_2)$$

**Part ①: $\dot{v}_2$**

Starting with:
$$\dot{v}_2 = \dot{\theta}_2(v_2 \times z_2 + \omega_2 \times b_2)$$

Expanding each piece...

**$v_2 \times z_2$:**
$$v_2 = [p_{21}]R_{21}\omega_1 + R_{21}v_1 + \dot{\theta}_2 b_2$$
$$v_2 \times z_2 = ([p_{21}]R_{21}\omega_1 + R_{21}v_1 + \dot{\theta}_2 b_2) \times z_2$$

Using $\omega_1 = \dot{\theta}_1 z_1$ and $v_1 = \dot{\theta}_1 b_1$:
$$= \dot{\theta}_1 \dot{\theta}_2 (p_{21} \times R_{21} z_1) \times z_2 + \dot{\theta}_1 \dot{\theta}_2 (R_{21} z_1 \times z_2) + \dot{\theta}_2^2 (b_2 \times z_2)$$

**$\omega_2 \times b_2$:**
$$\omega_2 \times b_2 = (R_{21}\omega_1 + \dot{\theta}_2 z_2) \times b_2 = \dot{\theta}_1 R_{21}z_1 \times b_2 + \dot{\theta}_2 z_2 \times b_2$$

**Part ②: $\omega_2 \times v_2$**

$$\omega_2 \times v_2 = (R_{21}\omega_1 + \dot{\theta}_2 z_2) \times (p_{21} \times R_{21}\omega_1 + R_{21}v_1 + \dot{\theta}_2 b_2)$$

Expanding:
$$= R_{21}\omega_1 \times (p_{21} \times R_{21}\omega_1) + R_{21}(\omega_1 \times v_1) + R_{21}\omega_1 \times \dot{\theta}_2 b_2$$
$$+ \dot{\theta}_2 z_2 \times (p_{21} \times R_{21}\omega_1) + \dot{\theta}_2 z_2 \times R_{21}v_1 + \dot{\theta}_2^2 z_2 \times b_2$$

Collecting by powers of $\dot{\theta}$:

| Power | Terms |
|-------|-------|
| $\dot{\theta}_1^2$ | $R_{21}z_1 \times (p_{21} \times R_{21}z_1)$, $R_{21}(z_1 \times b_1)$ |
| $\dot{\theta}_1 \dot{\theta}_2$ | $R_{21}z_1 \times b_2$, $z_2 \times (p_{21} \times R_{21}z_1)$, $z_2 \times R_{21}b_1$ |
| $\dot{\theta}_2^2$ | $z_2 \times b_2$ |

### 10.3 Calculating $\tau_2$

$$\tau_2 = n_2^T z_2 + f_2^T b_2$$

Substituting our expressions and collecting terms...

$$\tau_2 = z_2^T \big[ \dot{\theta}_1^2 (R_{21}z_1 \times I_2 R_{21}z_1) + \dot{\theta}_1 \dot{\theta}_2 (R_{21}z_1 \times z_2) + \dot{\theta}_1 \dot{\theta}_2 (z_2 \times I_2 R_{21}z_1) \big]$$

$$+ m_2 \dot{\theta}_1^2 b_2^T \big[ R_{21}z_1 \times (p_{21} \times R_{21}z_1) \big] + m_2 \dot{\theta}_1^2 b_2^T (p_{21} \times R_{21}z_1) + m_2 \dot{\theta}_1 \dot{\theta}_2 b_2^T R_{21}(z_1 \times b_1) + \ldots$$

The result can be organized as:
$$\tau_2 = \underbrace{[\cdots]}_{\text{col 1}} \dot{\theta}_1 + \underbrace{[\cdots]}_{\text{col 2}} \dot{\theta}_2$$

where the coefficients depend on $\dot{\theta}_1$ and $\dot{\theta}_2$ (giving the Coriolis matrix structure).

### 10.4 Calculating $\tau_1$

First compute $n_1$ and $f_1$:

$$n_1 = R_{21} n_2 + [p_{21}] R_{21} f_2 + [\omega_1] I_1 \omega_1$$

$$f_1 = R_{21} f_2 + m_1 [\omega_1] v_1$$

Then:
$$\tau_1 = n_1^T z_1 + f_1^T b_1$$

### 10.5 Pre-canceling Terms

Some simplifications before the full expansion:

$$\tau_1 = z_1^T n_1 + b_1^T f_1$$

$$= z_1^T \big( R_{21} n_2 + [p_{21}] R_{21} f_2 + [\omega_1] I_1 \omega_1 \big) + b_1^T \big( R_{21} f_2 + m_1 [\omega_1] v_1 \big)$$

**Note:** $[\omega_1] I_1 \omega_1 = \omega_1 \times I_1 \omega_1$

Since $\omega_1 = \dot{\theta}_1 z_1$:
- $z_1^T (\omega_1 \times I_1 \omega_1) = z_1^T (z_1 \times \cdot) = 0$ (perpendicular!)

Similarly:
- $z_1 \perp \omega_1 \times I_1 \omega_1$ so this term vanishes
- $b_1^T (m_1 \omega_1 \times v_1) = m_1 b_1^T (\omega_1 \times v_1)$

If $v_1 = \dot{\theta}_1 b_1$, then $\omega_1 \times v_1 = \dot{\theta}_1 z_1 \times \dot{\theta}_1 b_1 = \dot{\theta}_1^2 (z_1 \times b_1)$

For a revolute joint: often $z_1 \times b_1 \perp b_1$, so this may also simplify.

---

## 11. Structure of the Equations of Motion

The final result has the form:

$$\tau = M(\theta)\ddot{\theta} + C(\theta, \dot{\theta})\dot{\theta} + g(\theta)$$

where:
- $M(\theta)$ is the **mass matrix** (symmetric, positive definite)
- $C(\theta, \dot{\theta})$ is the **Coriolis matrix** (encodes $\dot{M} = C + C^T$ property)
- $g(\theta)$ is the **gravity vector**

### Key Properties

1. **$M$ is symmetric:** $M = M^T$

2. **$\dot{M} - 2C$ is skew-symmetric** (for certain definitions of $C$)

3. **Passivity:** $\dot{\theta}^T (\dot{M} - 2C) \dot{\theta} = 0$

4. **Energy:** $E = \frac{1}{2} \dot{\theta}^T M \dot{\theta} + V(\theta)$

---

## 12. References

- Murray, Li, Sastry — *A Mathematical Introduction to Robotic Manipulation* (1994)
- Lynch & Park — *Modern Robotics* (2017)
- Featherstone — *Rigid Body Dynamics Algorithms* (2008)
- Siciliano et al. — *Robotics: Modelling, Planning and Control* (2009)

---

## Appendix: Useful Identities

### Skew-Symmetric Matrix Properties

$$[a]b = a \times b = -b \times a = -[b]a$$

$$[a][b] = ba^T - (a^T b)I$$

$$[Ra] = R[a]R^T$$

$$a^T [b] c = c^T [a] b = b^T [c] a$$ (cyclic property of scalar triple product)

### Adjoint Properties

$$Ad_{T_1 T_2} = Ad_{T_1} Ad_{T_2}$$

$$Ad_T^{-1} = Ad_{T^{-1}}$$

$$(Ad_T)^T = Ad_{T^{-1}}^T \neq Ad_T^{-T}$$ (be careful!)

### Derivative of Adjoint

$$\frac{d}{dt} Ad_T = Ad_T \cdot ad_{\mathcal{V}^b}$$

where $\mathcal{V}^b = T^{-1}\dot{T}$ is the body velocity.
