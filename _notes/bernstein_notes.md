# Rigid Body Dynamics: Bernstein's Physical Vector Formalism

*Working notes on coordinate-free rigid body mechanics*

---

## Overview & Resources

**Resources:**
- Geometry, Kinematics, Statics, and Dynamics - Bernstein
- Murray, Li, Sastry — A Mathematical Introduction to Robotic Manipulation
- Modern Robotics — Lynch & Park

**Questions for Prof. Bernstein:**
1. Derive Spatial RNEA using the Bernstein formalism
2. Why not derive some properties: $\vec{I}$, $\vec{R}_{ora}$, $\vec{S}_{ora}$, $\vec{J}_{pre}$?
3. How to deal with contact?
4. Physical matrix is second order tensor

**Open Questions:**
1. How to derive impact constraints?
2. Plot Fabrics and PBDS pos/vel/acc
3. How to design reachable sets for contact?
4. How to derive grasping constraints?
5. How to derive constraints for walking?
6. Can I use these 3D tools algebraically for multiple projects?
7. Is geometric algebra the tool to link dynamics and vision?

---

## Notation Conventions

| Symbol | Meaning |
|--------|---------|
| $\vec{x}$ | Physical vector (coordinate-free) |
| $\vec{x}\|_A$ | Vector resolved in frame $F_A$ (column vector) |
| $F_A = [\hat{i}_A \quad \hat{j}_A \quad \hat{k}_A]$ | Frame (matrix of basis vectors) |
| $\mathfrak{F}_A = F_A^T$ | Co-frame (rows are basis vectors) |
| $\mathfrak{F}_A' = \mathfrak{F}_A^{-1} = F_A$ | Inverse co-frame |
| $\vec{R}_{B/A}$ | Physical rotation matrix from $A$ to $B$ |
| $O_{B/A}$ | Orientation matrix (resolved rotation) |
| $\vec{\Omega}_{B/A}$ | Physical angular velocity matrix |
| $\vec{\omega}_{B/A}$ | Physical angular velocity vector |

---

## Cheat Sheet: Key Identities

### Fundamental Relations

$$\vec{I} = F_A \mathfrak{F}_A' = F_B \mathfrak{F}_B'$$

$$\vec{R}_{B/A} = F_B \mathfrak{F}_A' \quad \Rightarrow \quad \vec{R}_{B/A} F_A = F_B \mathfrak{F}_A' F_A = F_B$$

$$\vec{R}_{A/B} = F_A \mathfrak{F}_B'$$

### Orientation Matrices

$$O_{A/B} = \vec{R}_{B/A}\big|_B = \mathfrak{F}_B' \vec{R}_{B/A} F_B = \mathfrak{F}_A' \vec{R}_{B/A} F_A = \mathfrak{F}_A' F_B$$

$$O_{B/A} = \vec{R}_{A/B}\big|_B = \mathfrak{F}_B' \vec{R}_{A/B} F_B = \mathfrak{F}_A' \vec{R}_{A/B} F_A = \mathfrak{F}_B' F_A$$

**Key relation:** $O_{B/A} \mathfrak{F}_A = \mathfrak{F}_B$

### Resolving and Unresolving

**To resolve** (get components in a frame):
$$F_B\big|_A = \mathfrak{F}_A' F_B$$

$$\vec{M}\big|_A = \mathfrak{F}_A' \vec{M} F_A$$

**To unresolve** (go from components back to physical):
$$\vec{x} = F_A \left(\vec{x}\big|_A\right)$$

$$\vec{M} = F_A \left(\vec{M}\big|_A\right) \mathfrak{F}_A'$$

### Resolving in Different Frames

$$\vec{x}\big|_B = O_{B/A} \vec{x}\big|_A = R_{A/B} \vec{x}\big|_A$$

$$\vec{M}\big|_B = O_{B/A} \vec{M}\big|_A O_{A/B}$$

This is a **similarity transform**!

---

## 2.8 Physical Rotation Matrices

### Physical Identity Matrix

**Definition:**
$$\vec{I} \triangleq \hat{i}_A \hat{i}_A' + \hat{j}_A \hat{j}_A' + \hat{k}_A \hat{k}_A'$$

**Fact 2.8.1:**
- $\vec{I}\vec{x} = \vec{x}$
- $\vec{x}'\vec{I} = \vec{x}'$
- $\vec{I}\big|_B = I_3$ (identity matrix in any frame)

**Proof:**
$$\vec{I}\vec{x} = (\hat{i}_A \hat{i}_A' + \hat{j}_A \hat{j}_A' + \hat{k}_A \hat{k}_A')(x_1 \hat{i}_A + x_2 \hat{j}_A + x_3 \hat{k}_A)$$
$$= x_1 \hat{i}_A + x_2 \hat{j}_A + x_3 \hat{k}_A$$

Note: $\hat{i}_B \hat{i}_B'$ is a physical matrix. By 2.6.15: $\hat{i}_B \hat{i}_B' = \hat{i}_0\big|_B \hat{i}_0\big|_B^T = [e_1 \quad 0 \quad 0]$

Thus $\vec{I}\big|_B = [e_1 \quad e_2 \quad e_3] = I_3$

### Physical Rotation Matrix

**Definition:**
$$\vec{R}_{B/A} \triangleq \hat{i}_B \hat{i}_A' + \hat{j}_B \hat{j}_A' + \hat{k}_B \hat{k}_A' = [\hat{i}_B \quad \hat{j}_B \quad \hat{k}_B] \begin{bmatrix} \hat{i}_A' \\ \hat{j}_A' \\ \hat{k}_A' \end{bmatrix} = F_B \mathfrak{F}_A'$$

**Key Properties:**
- $\vec{R}_{A/A} = F_A \mathfrak{F}_A' = \vec{I}$
- $\vec{R}_{B/A}$ rotates $F_A$ to $F_B$
- Note: $\mathfrak{F}_A = F_A^T$, $\mathfrak{F}_A' = F_A^{-1}$

**Fact 2.8.2:**
- $\hat{i}_B = \vec{R}_{B/A} \hat{i}_A$
- $\hat{j}_B = \vec{R}_{B/A} \hat{j}_A$
- $\hat{k}_B = \vec{R}_{B/A} \hat{k}_A$

**Proof:**
$$\vec{R}_{B/A} \hat{i}_A = F_B \mathfrak{F}_A' \hat{i}_A = [\hat{i}_B \quad \hat{j}_B \quad \hat{k}_B] \begin{bmatrix} \hat{i}_A' \\ \hat{j}_A' \\ \hat{k}_A' \end{bmatrix} \hat{i}_A = [\hat{i}_B \quad \hat{j}_B \quad \hat{k}_B] \begin{bmatrix} 1 \\ 0 \\ 0 \end{bmatrix} = \hat{i}_B$$

**More Properties:**
- $\vec{R}_{A/B}' = (\hat{i}_A \hat{i}_B' + \hat{j}_A \hat{j}_B' + \hat{k}_A \hat{k}_B')' = \hat{i}_B \hat{i}_A' + \hat{j}_B \hat{j}_A' + \hat{k}_B \hat{k}_A' = \vec{R}_{B/A}$
- $\vec{R}_{B/A} \vec{R}_{A/B} = F_B \mathfrak{F}_A' F_A \mathfrak{F}_B' = F_B I_3 \mathfrak{F}_B' = \vec{I}$
- $\vec{R}_{A/B} \vec{R}_{A/B} = \vec{I} \Rightarrow \vec{R}_{A/B}^{-1} = \vec{R}_{B/A}$
- $\vec{R}_{B/A} F_A = [\vec{R}_{B/A} \hat{i}_A \quad \vec{R}_{B/A} \hat{j}_A \quad \vec{R}_{B/A} \hat{k}_A] = [\hat{i}_B \quad \hat{j}_B \quad \hat{k}_B] = F_B$

---

## 2.9 Physical Cross Product Matrix

### Cross Product

For $\vec{x}\big|_A \times \vec{y}\big|_A = (\vec{x} \times \vec{y})\big|_A$:

$$= (x_1 \hat{i}_A + x_2 \hat{j}_A + x_3 \hat{k}_A) \times (y_1 \hat{i}_A + y_2 \hat{j}_A + y_3 \hat{k}_A)\big|_A$$

$$= \begin{pmatrix} x_2 y_3 - x_3 y_2 \\ -(x_1 y_3 - x_3 y_1) \\ x_1 y_2 - x_2 y_1 \end{pmatrix} = \begin{pmatrix} 0 & -x_3 & x_2 \\ x_3 & 0 & -x_1 \\ -x_2 & x_1 & 0 \end{pmatrix} \begin{pmatrix} y_1 \\ y_2 \\ y_3 \end{pmatrix} = \vec{x}\big|_A^\times \vec{y}\big|_A$$

### Physical Cross Product Matrix

**Definition:** $\vec{M} \triangleq \vec{x}^\times$

**Fact 2.9.1:**

$$\vec{x}^\times\big|_A = \vec{x}\big|_A^\times = \begin{pmatrix} 0 & -\hat{k}_A \cdot \vec{x} & \hat{j}_A \cdot \vec{x} \\ \hat{k}_A \cdot \vec{x} & 0 & -\hat{i}_A \cdot \vec{x} \\ -\hat{j}_A \cdot \vec{x} & \hat{i}_A \cdot \vec{x} & 0 \end{pmatrix}$$

**Alternate form:**
$$\vec{x}^\times = (\hat{i}_A \cdot \vec{x})(\hat{k}_A \hat{j}_A' - \hat{j}_A \hat{k}_A') + (\hat{j}_A \cdot \vec{x})(\hat{i}_A \hat{k}_A' - \hat{k}_A \hat{i}_A') + (\hat{k}_A \cdot \vec{x})(\hat{j}_A \hat{i}_A' - \hat{i}_A \hat{j}_A')$$

**Proof (steps):**
1. Compute $\vec{x}\big|_A \times \vec{y}\big|_A$
2. Get expression for $\vec{x}\big|_A^\times$
3. Rewrite components of $\vec{x}\big|_A^\times$

### Fact 2.9.2: Properties of $\vec{x}^\times$

- $\vec{x}^{\times'} = -\vec{x}^\times$ (skew-symmetric)
- $\vec{x}^\times \vec{x} = 0$
- $\vec{x}' \vec{x}^\times = 0$
- $\vec{x}^{\times 2} = \vec{x}\vec{x}' - |\vec{x}|^2 \vec{I}$

**Proof of $\vec{x}^{\times 2} = \vec{x}\vec{x}' - |\vec{x}|^2 \vec{I}$:**

$$\left(\vec{x}\vec{x}' - |\vec{x}|^2 \vec{I}\right)\big|_A = \vec{x}\big|_A \vec{x}\big|_A^T - |\vec{x}|^2 I_3$$

$$= \begin{pmatrix} x_1^2 & x_1 x_2 & x_1 x_3 \\ x_2 x_1 & x_2^2 & x_2 x_3 \\ x_1 x_3 & x_2 x_3 & x_3^2 \end{pmatrix} - \begin{pmatrix} |\vec{x}|^2 & 0 & 0 \\ 0 & |\vec{x}|^2 & 0 \\ 0 & 0 & |\vec{x}|^2 \end{pmatrix}$$

$$= \begin{pmatrix} -(x_2^2 + x_3^2) & x_1 x_2 & x_1 x_3 \\ x_2 x_1 & -(x_1^2 + x_3^2) & x_2 x_3 \\ x_1 x_3 & x_2 x_3 & -(x_1^2 + x_2^2) \end{pmatrix}$$

And:
$$\vec{x}^{\times 2}\big|_A = \begin{pmatrix} 0 & -x_3 & x_2 \\ x_3 & 0 & -x_1 \\ -x_2 & x_1 & 0 \end{pmatrix}^2 = \begin{pmatrix} -(x_2^2 + x_3^2) & x_1 x_2 & x_1 x_3 \\ x_1 x_2 & -(x_1^2 + x_3^2) & x_2 x_3 \\ x_1 x_3 & x_2 x_3 & -(x_1^2 + x_2^2) \end{pmatrix}$$

**Key point:** $[\omega]^2 = \omega \omega^T - |\omega|^2 I$

### Fact 2.9.3

If $\vec{M}$ is skew symmetric, then there exists a physical vector $\vec{x}$ such that $\vec{M} = \vec{x}^\times$.

**Proof:** Let $F_A$ be a frame and suppose $\vec{M}$ is skew symmetric. Recall:
$$\vec{x}^\times\big|_A = \begin{pmatrix} 0 & -\hat{k}_A \cdot \vec{x} & \hat{j}_A \cdot \vec{x} \\ \hat{k}_A \cdot \vec{x} & 0 & -\hat{i}_A \cdot \vec{x} \\ -\hat{j}_A \cdot \vec{x} & \hat{i}_A \cdot \vec{x} & 0 \end{pmatrix}$$

If $\exists \vec{x}$ s.t. $\vec{M} = \vec{x}^\times$, then we'd also have:
$$\vec{x}^\times\big|_A = \begin{pmatrix} 0 & M(1,2) & M(1,3) \\ -M(1,2) & 0 & M(2,3) \\ -M(1,3) & -M(2,3) & 0 \end{pmatrix} = \vec{M}\big|_A$$

Thus, defining $\vec{x} = -M(2,3) \hat{i}_A + M(1,3) \hat{j}_A - M(1,2) \hat{k}_A$ shows that such an $\vec{x}$ exists and satisfies $\vec{M} = \vec{x}^\times$.

---

## 2.10 Rotation and Orientation Matrices

### Fact 2.10.1

$$\vec{R}_{B/A}\big|_A = \vec{R}_{B/A}\big|_B = \begin{pmatrix} \hat{i}_A' \hat{i}_B & \hat{i}_A' \hat{j}_B & \hat{i}_A' \hat{k}_B \\ \hat{j}_A' \hat{i}_B & \hat{j}_A' \hat{j}_B & \hat{j}_A' \hat{k}_B \\ \hat{k}_A' \hat{i}_B & \hat{k}_A' \hat{j}_B & \hat{k}_A' \hat{k}_B \end{pmatrix} = [\hat{i}_B\big|_A \quad \hat{j}_B\big|_A \quad \hat{k}_B\big|_A] = F_B\big|_A$$

**Proof:**
- $\vec{R}_{B/A}\big|_B = \mathfrak{F}_B' \vec{R}_{B/A} F_B = \mathfrak{F}_B' F_B \mathfrak{F}_A' F_B = \mathfrak{F}_A' F_B = F_B\big|_A$
  - (resolve in $F_B$, rewrite $\vec{R}_{B/A}$, use $\mathfrak{F}_B' F_B = I_3$)
- $\vec{R}_{B/A}\big|_A = \mathfrak{F}_A' \vec{R}_{B/A} F_A = \mathfrak{F}_A' F_B \mathfrak{F}_A' F_A = \mathfrak{F}_A' F_B = F_B\big|_A$
  - (resolve in $F_A$, rewrite $\vec{R}_{B/A}$, use $\mathfrak{F}_A' F_A = I_3$)

Also: $\vec{R}_{B/A}\big|_B = R_{B/A} = O_{A/B} = F_B\big|_A$

### Fact 2.10.5

$$\begin{pmatrix} \hat{i}_B \\ \hat{j}_B \\ \hat{k}_B \end{pmatrix} = O_{B/A} \begin{pmatrix} \hat{i}_A \\ \hat{j}_A \\ \hat{k}_A \end{pmatrix} \quad \Leftrightarrow \quad \boxed{\mathfrak{F}_B = O_{B/A} \mathfrak{F}_A}$$

**Proof:**
$$O_{B/A} \begin{pmatrix} \hat{i}_A \\ \hat{j}_A \\ \hat{k}_A \end{pmatrix} = \mathfrak{F}_B' F_A \begin{pmatrix} \hat{i}_A \\ \hat{j}_A \\ \hat{k}_A \end{pmatrix} = \begin{pmatrix} \hat{i}_B' \hat{i}_A & \hat{j}_A' \hat{i}_B & \hat{k}_A' \hat{i}_B \\ \hat{i}_A' \hat{j}_B & \hat{j}_A' \hat{j}_B & \hat{k}_A' \hat{j}_B \\ \hat{i}_A' \hat{k}_B & \hat{j}_A' \hat{k}_B & \hat{k}_A' \hat{k}_B \end{pmatrix} \begin{pmatrix} \hat{i}_A \\ \hat{j}_A \\ \hat{k}_A \end{pmatrix}$$

$$= \begin{pmatrix} (\hat{i}_A \hat{i}_A' + \hat{j}_A \hat{j}_A' + \hat{k}_A \hat{k}_A') \hat{i}_B \\ (\hat{i}_A \hat{i}_A' + \hat{j}_A \hat{j}_A' + \hat{k}_A \hat{k}_A') \hat{j}_B \\ (\hat{i}_A \hat{i}_A' + \hat{j}_A \hat{j}_A' + \hat{k}_A \hat{k}_A') \hat{k}_B \end{pmatrix} = \begin{pmatrix} \vec{I} \hat{i}_B \\ \vec{I} \hat{j}_B \\ \vec{I} \hat{k}_B \end{pmatrix} = \begin{pmatrix} \hat{i}_B \\ \hat{j}_B \\ \hat{k}_B \end{pmatrix} = \mathfrak{F}_B$$

### Fact 2.10.7

$$\vec{x}\big|_B = O_{B/A} \vec{x}\big|_A = R_{A/B} \vec{x}\big|_A$$

**Proof:**
$$\vec{x}\big|_B = I_3 \vec{x}\big|_B = \mathfrak{F}_B' F_B \vec{x}\big|_B = \mathfrak{F}_B' F_A \vec{x}\big|_A = \mathfrak{F}_A' \vec{R}_{A/B} F_A \vec{x}\big|_A = O_{B/A} \vec{x}\big|_A = R_{A/B} \vec{x}\big|_A$$

**Note:** $F_A \vec{x}\big|_A = \vec{x} = F_B \vec{x}\big|_B$ (conceptual)

### Fact 2.10.11

$$\vec{M}\big|_B = O_{B/A} \vec{M}\big|_A O_{A/B}$$

**Proof:**
$$O_{B/A} \vec{M}\big|_A O_{A/B} = \mathfrak{F}_B' F_A \mathfrak{F}_A' \vec{M} F_A \mathfrak{F}_A' F_B$$
$$= \mathfrak{F}_B' \vec{I} \vec{M} \vec{I} F_B$$
$$= \mathfrak{F}_B' \vec{M} F_B$$
$$= \vec{M}\big|_B$$

---

## 4.1 Frame Derivatives

### Basic Notation

- $F_A$ — a frame
- $\vec{x} = x_1 \hat{i}_A + x_2 \hat{j}_A + x_3 \hat{k}_A$ — a physical vector
- $\vec{x}\big|_A = \begin{pmatrix} x_1 \\ x_2 \\ x_3 \end{pmatrix}$

### Time Derivative of a Physical Vector

$$\dot{\vec{x}} = \dot{x}_1 \hat{i}_A + \dot{x}_2 \hat{j}_A + \dot{x}_3 \hat{k}_A + x_1 \dot{\hat{i}}_A^{B\bullet} + x_2 \dot{\hat{j}}_A^{B\bullet} + x_3 \dot{\hat{k}}_A^{B\bullet}$$

The **A-dot derivative** (derivative with frame $F_A$ held fixed):
$$\overset{A\bullet}{\vec{x}} = \dot{x}_1 \hat{i}_A + \dot{x}_2 \hat{j}_A + \dot{x}_3 \hat{k}_A$$

$$\overset{A\bullet}{\vec{x}}\bigg|_A = \begin{pmatrix} \dot{x}_1 \\ \dot{x}_2 \\ \dot{x}_3 \end{pmatrix} = \dot{\vec{x}}\bigg|_A \quad \Rightarrow \quad \mathfrak{F}_A' \overset{A\bullet}{\vec{x}} = \dot{\vec{x}}\big|_A$$

### Velocity and Acceleration

- **Velocity:** $\vec{v}_{y/x|A} = \overset{A\bullet}{\vec{r}}_{y/x}$ — the velocity of $y$ relative to $x$ with respect to $F_A$
- **Acceleration:** $\vec{a}_{y/x|A} = \overset{A\bullet}{\vec{v}}_{y/x|A} = \overset{A\bullet\bullet}{\vec{r}}_{y/x}$

In components:
$$\vec{r}_{y/x}\bigg|_A = \begin{pmatrix} r_1 \\ r_2 \\ r_3 \end{pmatrix}, \quad \vec{v}_{y/x|A}\bigg|_A = \begin{pmatrix} \dot{r}_1 \\ \dot{r}_2 \\ \dot{r}_3 \end{pmatrix}, \quad \vec{a}_{y/x|A}\bigg|_A = \begin{pmatrix} \ddot{r}_1 \\ \ddot{r}_2 \\ \ddot{r}_3 \end{pmatrix}$$

### Changing Reference Frames

$$r_{y/x|B} = \vec{r}_{y/x}\big|_B = O_{B/A} \vec{r}_{y/x}\big|_A = O_{B/A} r_{y/x|A}$$

$$v_{y/x|A|B} = O_{B/A} \vec{v}_{y/x|A}\big|_A = O_{B/A} v_{y/x|A|A} = O_{B/A} \dot{r}_{y/x|A}$$

$$\vec{v}_{y/x|A|B} = \vec{v}_{y/x|A}\big|_B = \overset{A\bullet}{\vec{r}}_{y/x}\bigg|_B$$

$$\vec{a}_{y/x|A|B} = \overset{A\bullet}{\vec{v}}_{y/x|A}\bigg|_B = \overset{A\bullet\bullet}{\vec{r}}_{y/x}\bigg|_B$$

### Adding Velocities and Accelerations

$$\vec{v}_{z/x|A} = \vec{v}_{y/x|A} + \vec{v}_{z/y|A}$$

$$\vec{a}_{z/x|A} = \vec{a}_{y/x|A} + \vec{a}_{z/y|A}$$

$$\vec{a}_{z/x|A|B} = \vec{a}_{y/x|A|B} + \vec{a}_{z/y|A|B}$$

### Mixed Acceleration

$$\overset{B\bullet}{\vec{a}}_{y/x|A|B} = \overset{A\bullet}{\vec{v}}_{y/x|A} = \overset{B\bullet\bullet}{\vec{r}}_{y/x}$$

$$a_{y/x|A|B|C} = \vec{a}_{y/x|A|B}\big|_C = O_{C/B} \vec{a}_{y/x|A|B}\big|_B = O_{C/B} \overset{B\bullet}{\vec{v}}_{y/x|A}\big|_B$$

Working through the chain rule:
$$= O_{C/B} \overset{B\bullet}{\vec{v}}_{y/x|A|B} = O_{C/B} \frac{d}{dt}(v_{y/x|A|B}) = O_{C/B} \frac{d}{dt}(O_{B/A} v_{y/x|A})$$
$$= O_{C/B} \frac{d}{dt}(O_{B/A} \dot{r}_{y/x|A}) = O_{C/B}(\dot{O}_{B/A} \dot{r}_{y/x|A} + O_{B/A} \ddot{r}_{y/x|A})$$

---

## 4.2 Mixed-Dot Identity and Physical Angular Velocity Matrix

### Fact 4.2.1: Mixed-Dot Identity

$$\overbrace{\vec{R}_{B/A}}^{B\bullet} \overset{B\bullet}{\vec{x}} = \vec{R}_{B/A} \overset{A\bullet}{\vec{x}} \quad \text{or} \quad \overset{A\bullet}{\vec{x}} = \vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} \overset{B\bullet}{\vec{x}}$$

**Proof:**
$$\overset{A\bullet}{\vec{x}}\bigg|_A = \overbrace{\dot{\vec{x}}\big|_A}^{in} = O_{A/B} \overset{B\bullet}{\vec{x}}\big|_B = \overbrace{\vec{R}_{B/A}\big|_B}^{} \overset{B\bullet}{\vec{x}}\big|_B = \overbrace{\vec{R}_{B/A}}^{B\bullet} \overset{B\bullet}{\vec{x}}\big|_B$$

$$= O_{B/A} \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{x}\big|_A = \left(\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{x}\right)\big|_A$$

This shows how $\overset{A\bullet}{\vec{x}}$ and $\overset{B\bullet}{\vec{x}}$ are related. In this context, frames don't have a location, so this relationship describes the rate of change due to $F_A$ and $F_B$ rotating relative to each other.

### Fact 4.2.2

By 4.2.1:
$$\overset{A\bullet}{\vec{x}} = \vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{x} = \vec{R}_{A/B} \left(\overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{x} + \overbrace{\vec{R}_{B/A}}^{B} \overset{B\bullet}{\vec{x}}\right)$$

$$= \overset{B\bullet}{\vec{x}} + \vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{x}$$

### Definition 4.2.3: Physical Angular Velocity Matrix

$$\vec{\Omega}_{B/A} = \vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} = -\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{A\bullet}$$

So: $\overset{A\bullet}{\vec{x}} = \overset{B\bullet}{\vec{x}} + \vec{\Omega}_{B/A} \vec{x}$

### Fact 4.2.4

$\vec{\Omega}_{B/A}$ is skew symmetric.

**Proof:**
$$\vec{\Omega}_{B/A}' = \left(\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet}\right)' = \left(\overbrace{\vec{R}_{B/A}}^{B\bullet'} \vec{R}_{A/B}'\right) = \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{R}_{B/A} = -\vec{\Omega}_{B/A}$$

### Fact 4.2.7

$$\overset{A\bullet}{\hat{i}}_B = \vec{\Omega}_{B/A} \hat{i}_B, \quad \overset{A\bullet}{\hat{j}}_B = \vec{\Omega}_{B/A} \hat{j}_B, \quad \overset{A\bullet}{\hat{k}}_B = \vec{\Omega}_{B/A} \hat{j}_B$$

**Proof:**
$$\vec{\Omega}_{B/A} \hat{i}_B = \vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} \hat{i}_B \quad \text{(by definition)}$$
$$= \vec{R}_{B/A} \overbrace{\vec{R}_{A/B}}^{A\bullet} \hat{i}_B \quad \text{(by 4.1.10)}$$
$$= \vec{R}_{B/A} \overset{A\bullet}{\hat{i}}_A \quad \text{(by 2.8.2)}$$
$$= (\hat{i}_B \hat{i}_A' + \hat{j}_B \hat{j}_A' + \hat{k}_B \hat{k}_A') \overset{A\bullet}{\hat{i}}_A$$

Since $\overset{A\bullet}{\hat{i}}_A = \overset{A\bullet}{\hat{j}}_A = \overset{A\bullet}{\hat{k}}_A = 0$:
$$= \overset{A\bullet}{\hat{i}}_B$$

### Fact 4.2.9

$$\vec{\Omega}_{A/B} = -\vec{\Omega}_{B/A}$$

**Proof:** By 4.1.10:
$$\vec{\Omega}_{A/B} = \vec{R}_{B/A} \overbrace{\vec{R}_{A/B}}^{\bullet A} = -\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} = -\vec{\Omega}_{B/A}$$

### Fact 4.2.10

$$\vec{\Omega}_{B/A}\bigg|_B = \left(\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet}\right)\bigg|_B = \left(-\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet}\right)\bigg|_B$$

$$= \vec{R}_{B/A}\bigg|_B \overbrace{\vec{R}_{B/A}}^{B\bullet}\bigg|_B = -\vec{R}_{A/B}\bigg|_B \overbrace{\vec{R}_{B/A}}^{B\bullet}\bigg|_B$$

$$= O_{B/A} \dot{O}_{A/B} = -\dot{O}_{B/A} O_{A/B}$$

---

## 4.3 The Physical Angular Velocity Vector and Poisson's Equation

### Fact 4.3.1

There exists a physical vector $\vec{\omega}_{B/A}$ such that:
$$\vec{\Omega}_{B/A} = \vec{\omega}_{B/A}^\times$$

$$\vec{\Omega}_{B/A}\bigg|_B = \begin{pmatrix} 0 & -\omega_3 & \omega_2 \\ \omega_3 & 0 & -\omega_1 \\ -\omega_2 & \omega_1 & 0 \end{pmatrix} \quad \text{where} \quad \vec{\omega}_{B/A}\bigg|_B = \begin{pmatrix} \omega_1 \\ \omega_2 \\ \omega_3 \end{pmatrix}$$

Also: $\vec{\omega}_{A/B} = -\vec{\omega}_{B/A}$ (see 4.1.10)

### Fact 4.3.5 (Poisson's Equation)

$$\overset{A\bullet}{\hat{i}}_B = \vec{\omega}_{B/A} \times \hat{i}_B$$
$$\overset{A\bullet}{\hat{j}}_B = \vec{\omega}_{B/A} \times \hat{j}_B$$
$$\overset{A\bullet}{\hat{k}}_B = \vec{\omega}_{B/A} \times \hat{j}_B$$

**Proof:** See Fact 4.2.7

---

## 4.4 The Transport Theorem

### Fact 4.4.1: The Transport Theorem

Let $\vec{x}$ be a physical vector and let $F_A$ and $F_B$ be frames. Then:

$$\boxed{\overset{A\bullet}{\vec{x}} = \overset{B\bullet}{\vec{x}} + \vec{\omega}_{B/A} \times \vec{x}}$$

**Proof:**
$$\overset{A\bullet}{\vec{x}} = \overbrace{\dot{\vec{x}}\big|_A}^{in} = O_{A/B}\left(\overset{B\bullet}{\vec{x}}\right)\bigg|_B = \left(\vec{R}_{B/A} \overset{B\bullet}{\vec{x}}\right)\bigg|_B = \overbrace{\vec{R}_{B/A}}^{B\bullet} \overset{B\bullet}{\vec{x}}\bigg|_B$$

$$= O_{B/A}\left(\vec{R}_{B/A} \overset{B\bullet}{\vec{x}}\right)\bigg|_A = \vec{R}_{A/B}\left(\vec{R}_{B/A} \overset{B\bullet}{\vec{x}}\right)\bigg|_A$$

$$\Rightarrow \overset{A\bullet}{\vec{x}} = \vec{R}_{A/B}\left(\vec{R}_{B/A} \overset{B\bullet}{\vec{x}}\right)$$

$$= \vec{R}_{A/B}\left(\overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{x} + \overbrace{\vec{R}_{B/A}}^{B} \overset{B\bullet}{\vec{x}}\right)$$

$$= \overset{B\bullet}{\vec{x}} + \vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{x}$$

$$= \overset{B\bullet}{\vec{x}} + \vec{\Omega}_{B/A} \vec{x}$$

$$= \overset{B\bullet}{\vec{x}} + \vec{\omega}_{B/A} \times \vec{x}$$

### Fact 4.4.2

$$\overset{A\bullet}{\vec{\omega}}_{B/A} = \overset{B\bullet}{\vec{\omega}}_{B/A}$$

**Proof:** By the transport theorem:
$$\overset{A\bullet}{\vec{\omega}}_{B/A} = \overset{B\bullet}{\vec{\omega}}_{B/A} + \vec{\omega}_{B/A} \times \vec{\omega}_{B/A} = \overset{B\bullet}{\vec{\omega}}_{B/A}$$

### Angular Acceleration

$$\vec{\alpha}_{B/A} = \overset{B\bullet}{\vec{\omega}}_{B/A} = \overset{A\bullet}{\vec{\omega}}_{B/A}$$

### Mixed Angular Acceleration

$$\vec{\alpha}_{B/A|C} = \overset{C\bullet}{\vec{\omega}}_{B/A}$$

$$\vec{\alpha}_{B/A}\bigg|_D = \overset{B\bullet}{\vec{\omega}}_{B/A}\bigg|_D$$

---

## 4.1.9-4.1.11: Derivative Identities

### Fact 4.1.9

$$\overbrace{\vec{R}_{A/B}}^{B\bullet} = -\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{R}_{B/A} \quad (1)$$

$$\overbrace{\vec{R}_{A/B}}^{A\bullet} = -\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{A\bullet} \vec{R}_{B/A} \quad (2)$$

**Proof:**
$$\vec{R}_{A/B} \vec{R}_{B/A} = \vec{I} \Rightarrow \overbrace{\vec{R}_{A/B}}^{B\bullet} \vec{R}_{B/A} = -\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet}$$
$$\Rightarrow \overbrace{\vec{R}_{A/B}}^{B\bullet} = -\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{R}_{B/A}$$

### Fact 4.1.10

$$\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} = \vec{R}_{B/A} \overbrace{\vec{R}_{A/B}}^{A\bullet} = -\vec{R}_{B/A} \overbrace{\vec{R}_{A/B}}^{B\bullet}$$

**Proof:**
$$\left(\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet}\right)\bigg|_B = \mathfrak{F}_B'\left(\vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet}\right) F_B \quad \text{(resolve in } F_B \text{)}$$

$$= \mathfrak{F}_B' F_A \mathfrak{F}_B' \overbrace{\vec{R}_{B/A}}^{B\bullet} F_B \quad (\vec{R}_{A/B} = F_A \mathfrak{F}_B')$$

$$= \mathfrak{F}_B' F_A \overbrace{\vec{R}_{B/A}}^{B\bullet}\bigg|_B \quad \text{(resolve in } F_B \text{)}$$

$$= O_{B/A} \dot{R}_{B/A} O_{A/B} O_{B/A} \quad (\bullet O_{B/A} = \mathfrak{F}_B' F_A)$$
$$\quad (\bullet O_{A/B} O_{B/A} = I_3)$$

$$= O_{B/A} \overbrace{\vec{R}_{B/A}}^{A\bullet}\bigg|_A O_{A/B} O_{B/A} \quad (\bullet \overbrace{\vec{R}_{B/A}}^{B\bullet}\big|_B = \vec{R}_{B/A}\big|_B \quad \text{see cheat sheet})$$

$$= \overbrace{\vec{R}_{B/A}}^{A\bullet}\bigg|_B \vec{R}_{A/B}\bigg|_B$$

$$= \left(\overbrace{\vec{R}_{B/A}}^{A\bullet} \vec{R}_{A/B}\right)\bigg|_B$$

Thus: $\overset{A\bullet}{\vec{x}} = \vec{R}_{A/B} \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{x} \Leftrightarrow \vec{R}_{B/A} \overset{A\bullet}{\vec{x}} = \overbrace{\vec{R}_{B/A}}^{B\bullet} \vec{x}$

### Fact 4.1.11

$$\overbrace{\vec{R}_{B/A}}^{B\bullet'} = \overbrace{\vec{R}_{A/B}}^{B\bullet}$$

**Proof:**
$$\overbrace{\vec{R}_{B/A}}^{B\bullet'} = (\hat{i}_B \hat{i}_A' + \hat{j}_B \hat{j}_A' + \hat{k}_B \hat{k}_A')^{\bullet'} = (\overset{B\bullet}{\hat{i}}_B \hat{i}_A' + \overset{B\bullet}{\hat{j}}_B \hat{j}_A' + \overset{B\bullet}{\hat{k}}_B \hat{k}_A')' = (\overset{B\bullet}{\hat{i}}_A \hat{i}_B' + \overset{B\bullet}{\hat{j}}_A \hat{j}_B' + \overset{B\bullet}{\hat{k}}_A \hat{k}_B') = \overbrace{\vec{R}_{A/B}}^{B\bullet}$$

---

## 6.1 The Zeroth and First Moments of Mass

### Definition 6.1.1

Let $B$ be a body consisting of particles $y_1, \ldots, y_\ell$ whose masses are $m_1, \ldots, m_\ell$. Let $w$ be a point. Then:

$$M_B \triangleq \sum_{i=1}^{\ell} m_i \quad \text{(total mass)}$$

$$\vec{r}_{c/w} \triangleq \frac{1}{M_B} \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/w} \quad \text{(center of mass)}$$

$$M_B \vec{r}_{c/w} = \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/w} \quad \text{(first moment of mass)}$$

### Fact 6.1.2: Center of Mass is Independent of Reference Point

Let $B$ be a body and let $w$ and $w'$ be points. Define the points $c$ and $c'$ such that:

$$\vec{r}_{c/w} = \frac{1}{M_B} \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/w}$$

$$\vec{r}_{c'/w'} = \frac{1}{M_B} \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/w'}$$

Then $c$ and $c'$ are **colocated**.

**Proof:**
$$\vec{r}_{c/c'} = \vec{r}_{w/c} + \vec{r}_{w'/w} + \vec{r}_{c'/w'}$$
$$= \vec{r}_{w/w} + \vec{r}_{c'/w'} - \vec{r}_{c/w}$$
$$= \vec{r}_{w'/w} + \frac{1}{M_B} \sum_{i=1}^{\ell} m_i (\vec{r}_{y_i/w'} - \vec{r}_{y_i/w})$$
$$= \vec{r}_{w'/w} + \frac{1}{M_B} \sum_{i=1}^{\ell} m_i (\vec{r}_{y_i/w'} + \vec{r}_{w/y_i})$$
$$= \vec{r}_{w'/w} + \left(\frac{1}{M_B} \sum_{i=1}^{\ell} m_i\right) \vec{r}_{w/w'}$$
$$= \vec{r}_{w'/w} + \vec{r}_{w/w'} = 0$$

### Fact 6.1.3

Let $B$ be a body. Then $c$ (center of mass) satisfies:
$$\sum_{i=1}^{\ell} m_i \vec{r}_{y_i/c} = 0$$

**Proof:** By the above:
$$\vec{r}_{c/w} = \frac{1}{M_B} \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/w}$$
$$\Rightarrow 0 = \vec{r}_{c/c} = \frac{1}{M_B} \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/c} = \frac{1}{M_B} \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/c}$$

---

## 6.2 The Second Moment of Mass

### Definition 6.2.1: Physical Inertia Matrix

Let $B$ be a body with particles $y_1, \ldots, y_\ell$ whose masses are $m_1, \ldots, m_\ell$ respectively. Let $z$ be a point. Then the **physical inertia matrix** $\vec{J}_{B/z}$ of $B$ relative to $z$ is defined by:

$$\vec{J}_{B/z} \triangleq \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z}^{\times'} \vec{r}_{y_i/z}^\times$$

### Angular Momentum

The angular momentum of $B$ relative to $z$ with respect to $F_A$ is:
$$\vec{L}_{B/z/A} = \sum_{i=1}^{\ell} \vec{r}_{y_i/z} \times (m_i \vec{v}_{y_i/z/A})$$

We assume frame $F_B$ is rigidly attached to body $B$.

$$= \sum_{i=1}^{\ell} \vec{r}_{y_i/z} \times \left(m_i \overset{A\bullet}{\vec{r}}_{y_i/z}\right)$$

$$= \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z} \times \left(\overset{A\bullet}{\vec{r}}_{y_i/z}\right)$$

Using the transport theorem: $\overset{A\bullet}{\vec{r}}_{y_i/z} = \overset{B\bullet}{\vec{r}}_{y_i/z} + \vec{\omega}_{B/A} \times \vec{r}_{y_i/z}$

Since $y_i$ and $z$ are both fixed in $B$: $\overset{B\bullet}{\vec{r}}_{y_i/z} = 0$

$$= -\sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z} \times (\vec{r}_{y_i/z} \times \vec{\omega}_{B/A})$$

$$= -\sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z}^\times \vec{r}_{y_i/z}^\times \vec{\omega}_{B/A}$$

$$= \sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z}^{\times'} \vec{r}_{y_i/z}^\times \vec{\omega}_{B/A} = -\sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z}^{\times 2} \vec{\omega}_{B/A}$$

$$= \vec{J}_{B/z} \vec{\omega}_{B/A}$$

### Using $\vec{x}^{\times 2} = \vec{x}\vec{x}' - |\vec{x}|^2 \vec{I}$

$$-\sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z}^{\times 2} = -\sum_{i=1}^{\ell} m_i (\vec{r}\vec{r}' - |\vec{r}|^2 \vec{I}) = \sum_{i=1}^{\ell} m_i (|\vec{r}|^2 \vec{I} - \vec{r}\vec{r}')$$

### How to Resolve a Matrix $\vec{M}$?

The key is to recall $\vec{M} = \vec{x}\vec{y}'$ and resolve $\vec{x}$ and $\vec{y}'$ in a frame.

$$\vec{M} = \vec{x}\vec{y}' = F_A(\vec{x}\big|_A) \vec{y}\big|_A^T \mathfrak{F}_A' = F_A (\vec{x}\vec{y}')\big|_A \mathfrak{F}_A' = F_A \vec{M}\big|_A \mathfrak{F}_A'$$

$$\Rightarrow \vec{M}\big|_A = \mathfrak{F}_A' \vec{M} F_A = \begin{pmatrix} \hat{i}_A' \\ \hat{j}_A' \\ \hat{k}_A' \end{pmatrix} \vec{M} [\hat{i}_A \quad \hat{j}_A \quad \hat{k}_A] = \begin{pmatrix} \hat{i}_A' \vec{M} \hat{i}_A & \hat{i}_A' \vec{M} \hat{j}_A & \hat{i}_A \vec{M} \hat{k}_A \\ \hat{j}_A' \vec{M} \hat{i}_A & \hat{j}_A' \vec{M} \hat{j}_A & \hat{j}_A' \vec{M} \hat{k}_A \\ \hat{k}_A' \vec{M} \hat{i}_A & \hat{k}_A' \vec{M} \hat{j}_A & \hat{k}_A' \vec{M} \hat{k}_A \end{pmatrix}$$

### Resolving $\vec{J}_{B/z}$ in $F_B$

$$\vec{J}_{B/z} = F_B (\vec{J}_{B/z}\big|_B) \mathfrak{F}_B'$$

$$\vec{J}_{B/z}\big|_B = \sum_{i=1}^{\ell} m_i \left(|\vec{r}_{y_i/z}|^2 \vec{I} - \vec{r}_{y_i/z} \vec{r}_{y_i/z}'\right)\big|_B$$

$$= \sum_{i=1}^{\ell} m_i \left(|\vec{r}_{y_i/z}|^2 I_3 - \vec{r}_{y_i/z}\big|_B \vec{r}_{y_i/z}\big|_A^T\right)$$

With $\vec{r}_{y_i/z}\big|_B = (\bar{x}_i, \bar{y}_i, \bar{z}_i)^T$:

$$= \sum_{i=1}^{\ell} m_i \left\{ \begin{pmatrix} \bar{x}_i^2 + \bar{y}_i^2 + \bar{z}_i^2 & 0 & 0 \\ 0 & \bar{x}_i^2 + \bar{y}_i^2 + \bar{z}_i^2 & 0 \\ 0 & 0 & \bar{x}_i^2 + \bar{y}_i^2 + \bar{z}_i^2 \end{pmatrix} - \begin{pmatrix} \bar{x}_i^2 & \bar{x}_i \bar{y}_i & \bar{x}_i \bar{z}_i \\ \bar{y}_i \bar{x}_i & \bar{y}_i^2 & \bar{y}_i \bar{z}_i \\ \bar{z}_i \bar{x}_i & \bar{z}_i \bar{y}_i & \bar{z}_i^2 \end{pmatrix} \right\}$$

This gives the familiar inertia tensor form.

The $xx$-component: $\vec{J}_{xx/z}\big|_B = \hat{i}_B' \vec{J}_{B/z} \hat{i}_B = \sum_{i=1}^{\ell} m_i (\bar{y}_i^2 + \bar{z}_i^2)$

### Fact 6.2.3: Resolving Inertia in Different Frames

$$\vec{J}_{B/z}\big|_A = O_{A/B} (\vec{J}_{B/z}\big|_B) O_{B/A}$$

We can resolve the physical inertia in different frames.

### Fact 6.2.4: Rotating the Body

Let $B$ be a body and $z$ be a point in $B$. Let $\vec{R}$ be a physical rotation matrix, let $B'$ be the body $B$ rotated by $\vec{R}$, and let $z'$ be the point in $B'$ corresponding to $z$ in $B$. Then:

$$\vec{J}_{B'/z'} = \vec{R} \vec{J}_{B/z} \vec{R}'$$

(Proved this in Modern Robotics notes: $(\vec{R}\vec{x})^\times \vec{y} = \vec{R}\vec{x} \times \vec{y} = \vec{R}(\vec{x} \times \vec{R}'\vec{y}) = \vec{R}(\vec{x})^\times \vec{R}'\vec{y}$)

**Proof:**
$$\vec{J}_{B'/z'} = -\sum_{i=1}^{\ell} m_i \vec{r}_{y_i'/z'}^{\times 2} = -\sum_{i=1}^{\ell} m_i (\vec{R}\vec{r}_{y_i/z})^{\times 2} = -\sum_{i=1}^{\ell} m_i \{(\vec{R}\vec{r}_{y_i/z}^\times)^{\times}\}^2$$

$$= -\sum_{i=1}^{\ell} m_i (\vec{R}\vec{r}_{y_i/z}^\times \vec{R}')(\vec{R}\vec{r}_{y_i/z}^\times \vec{R}')$$

$$= -\sum_{i=1}^{\ell} m_i (\vec{R}\vec{r}_{y_i/z}^{\times 2}\vec{R}') = \vec{R}\left(-\sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z}^{\times 2}\right)\vec{R}' = \vec{R}\vec{J}_{B/z}\vec{R}'$$

### Fact 6.2.9: Adding Inertias

You can add physical inertia matrices if they're defined with respect to the same point:
$$\vec{J}_{B_3/z} = \vec{J}_{B_1/z} + \vec{J}_{B_2/z}$$

Think of the bodies $B_1, \ldots, B_3$ as being sets of points.

### Fact 6.2.10

$$B_3 = B_2 / B_1$$
$$\vec{J}_{B_3/z} = \vec{J}_{B_2/z} - \vec{J}_{B_1/z}$$

**Proof:** Just eliminate points from $B_2$.

### Fact 6.2.12: Shifting the Reference Point

Let $B$ be a body, $M_B$ the mass of the body, $z$ be a point, and $c$ be the center of mass of $B$.

$$\vec{J}_{B/z} = \vec{J}_{B/c} - M_B \vec{r}_{z/c}^{\times 2} = \vec{J}_{B/c} - M_B \vec{r}_{z/c}^{\times 2} = \vec{J}_{B/c} + M_B \vec{r}_{z/c}^{\times'} \vec{r}_{z/c}$$
$$= \vec{J}_{B/c} + M_B (|\vec{r}_{z/c}|^2 \vec{I} - \vec{r}_{z/c} \vec{r}_{z/c}')$$

**Proof:** Let
$$\vec{J}_{B/z} = -\sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z}^{\times 2} \quad \text{and} \quad \vec{J}_{B/c} = -\sum_{i=1}^{\ell} m_i \vec{r}_{y_i/c}^{\times 2}$$

Then:
$$\vec{J}_{B/z} = -\sum_{i=1}^{\ell} m_i \vec{r}_{y_i/z}^{\times 2} = -\sum_{i=1}^{\ell} m_i (\vec{r}_{y_i/c} + \vec{r}_{c/z})^{\times 2}$$

$$= -\sum_{i=1}^{\ell} m_i \left(\vec{r}_{y_i/c}^{\times 2} + \vec{r}_{c/z}^{\times 2} + \vec{r}_{y_i/c}^\times \vec{r}_{c/z}^\times + \vec{r}_{c/z}^\times \vec{r}_{y_i/c}^\times\right)$$

Using $\sum m_i \vec{r}_{y_i/c} = 0$ (Fact 6.1.3), the cross terms vanish:

$$= \vec{J}_{B/c} - M_B \vec{r}_{c/z}^{\times 2} = \vec{J}_{B/z} + M_B (|\vec{r}_{z/c}|^2 \vec{I} - \vec{r}_{z/c} \vec{r}_{z/c}')$$

### Fact 6.2.14: Parallel Axis Theorem

This fact basically establishes the **Parallel Axis Theorem** by resolving Fact 6.2.12.

---

## 6.5 Moments, Balanced Forces, and Torques

### Moment of a Force

The moment $\vec{M}_{y/w}$ on $y$ relative to $w$ due to $\vec{f}_y$:

$$\vec{M}_{y/w} = \vec{r}_{y/w} \times \vec{f}_y$$

The moment induces a rotation of $y$ around $w$ according to the right-hand rule.

### Moment on a Body

Let body $B$ consist of inertia points $y_1, \ldots, y_\ell$ and let $\vec{f}_{y_i}$ be the force applied to $y_i$, and let $w$ be a point. Then the moment on $B$ relative to $w$ due to $\vec{f}_{y_1}, \ldots, \vec{f}_{y_\ell}$ is:

$$\vec{M}_{B/w} = \sum_{i=1}^{\ell} \vec{M}_{y_i/w} = \sum_{i=1}^{\ell} \vec{r}_{y_i/w} \times \vec{f}_{y_i}$$

**The forces that generate a particular moment are not unique.**

### Total Force on a Body

The total force on $B$ due to $\vec{f}_{y_1}, \ldots, \vec{f}_{y_\ell}$ is:
$$\vec{f}_B = \sum_{i=1}^{\ell} \vec{f}_{y_i}$$

The forces are **balanced** if $\vec{f}_B = 0$.

---

## Useful Vector Identities

### Cross Product as Outer Products

$$[c \times d] = dc^T - cd^T$$

### Triple Cross Product

$$a \times (b \times c) = -(b \times c) \times a = (c \times b) \times a = [c \times b]a = (bc^T - cb^T)a = b(c^T a) - c(b^T a)$$

### Squared Cross Product Matrix Acting on a Vector

$$\vec{x}^{\times 2} \vec{y} = \vec{x} \times (\vec{x} \times \vec{y}) = (\vec{y} \times \vec{x}) \times \vec{x}$$
$$= [\vec{y} \times \vec{x}]\vec{x}$$
$$= \vec{x}(\vec{y}'\vec{x}) - \vec{y}(\vec{x}'\vec{x})$$
$$= \vec{x}(\vec{x}'\vec{y}) - (\vec{x}'\vec{x})\vec{I}\vec{y}$$
$$= (\vec{x}\vec{x}' - (\vec{x}'\vec{x})\vec{I})\vec{y}$$

---

## Summary of Key Results

| Result | Formula |
|--------|---------|
| Transport Theorem | $\overset{A\bullet}{\vec{x}} = \overset{B\bullet}{\vec{x}} + \vec{\omega}_{B/A} \times \vec{x}$ |
| Angular Velocity Matrix | $\vec{\Omega}_{B/A} = \vec{\omega}_{B/A}^\times$ |
| Physical Inertia | $\vec{J}_{B/z} = \sum m_i \vec{r}_{y_i/z}^{\times'} \vec{r}_{y_i/z}^\times = -\sum m_i \vec{r}_{y_i/z}^{\times 2}$ |
| Angular Momentum | $\vec{L}_{B/z/A} = \vec{J}_{B/z} \vec{\omega}_{B/A}$ |
| Parallel Axis | $\vec{J}_{B/z} = \vec{J}_{B/c} + M_B(|\vec{r}_{z/c}|^2\vec{I} - \vec{r}_{z/c}\vec{r}_{z/c}')$ |
| Resolving Matrices | $\vec{M}\big|_B = O_{B/A} \vec{M}\big|_A O_{A/B}$ |
| Squared Cross Product | $\vec{x}^{\times 2} = \vec{x}\vec{x}' - |\vec{x}|^2\vec{I}$ |

---

<!-- ## References

- Bernstein, D. — Robot Dynamics (Ch. 6, 7)
- Murray, Li, Sastry — A Mathematical Introduction to Robotic Manipulation
- Modern Robotics — Lynch & Park -->
