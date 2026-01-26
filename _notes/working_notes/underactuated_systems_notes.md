---
layout: page
title: "Equations of Motion for Underactuated Systems"
---

# Equations of Motion for Underactuated Systems

*Working notes on Lagrangian mechanics applied to pendulum-type systems*

---

## 1. Introduction

An **underactuated system** has fewer control inputs than degrees of freedom. This makes control more challenging—we can't independently command each coordinate. Classic examples include:

| System | DOF | Actuators | Underactuation |
|--------|-----|-----------|----------------|
| Simple Pendulum | 1 | 1 | Fully actuated |
| Cart-Pole | 2 | 1 | 1 |
| Double Pendulum | 2 | 1 (at base) | 1 |
| Dual Inverted Pendulum | 3 | 1 | 2 |
| Wheeled Inverted Pendulum | 2 | 1 | 1 |

This document derives the equations of motion for each system using Lagrangian mechanics, arriving at the standard **manipulator equation** form:

$$M(q)\ddot{q} + C(q, \dot{q})\dot{q} + G(q) = Bu$$

where:
- $M(q)$ — Mass/inertia matrix (symmetric, positive definite)
- $C(q, \dot{q})$ — Coriolis and centrifugal terms
- $G(q)$ — Gravity vector
- $B$ — Input matrix (maps actuator forces to generalized coordinates)
- $u$ — Control input

---

## 2. The Lagrangian Method

### 2.1 Recipe

1. Choose generalized coordinates $q = [q_1, \ldots, q_n]^T$
2. Express positions of all masses in terms of $q$
3. Compute kinetic energy $T = \frac{1}{2}\sum_i m_i \|\dot{x}_i\|^2 + \frac{1}{2}\sum_i I_i \dot{\theta}_i^2$
4. Compute potential energy $V = \sum_i m_i g y_i$
5. Form the Lagrangian $\mathcal{L} = T - V$
6. Apply the Euler-Lagrange equations:

$$\frac{d}{dt}\frac{\partial \mathcal{L}}{\partial \dot{q}_i} - \frac{\partial \mathcal{L}}{\partial q_i} = \tau_i$$

where $\tau_i$ is the generalized force for coordinate $q_i$.

### 2.2 Notation Conventions

Throughout these notes:
- $c_i = \cos\theta_i$, $s_i = \sin\theta_i$
- $c_{1+2} = \cos(\theta_1 + \theta_2)$, $s_{1+2} = \sin(\theta_1 + \theta_2)$
- $l_i$ = distance to center of mass of link $i$
- $L_i$ = total length of link $i$
- $I_i$ = moment of inertia of link $i$ about its center of mass
- $\bar{M}_i = I_i + m_i l_i^2$ = moment of inertia about pivot (parallel axis theorem)

---

## 3. Simple Pendulum

### 3.1 Setup

A rod of length $L$ and mass $m$, pivoting about one end. The center of mass is at distance $l = L/2$ from the pivot.

**Generalized coordinate:** $q = \theta$ (angle from downward vertical)

**State:** $\mathbf{x} = [\theta, \dot{\theta}]^T$

**Input:** $u$ = torque at pivot

```
        O  (pivot)
        |\
        | \
        |  \  θ
        |   \
        | l  * (center of mass)
        |     \
        |      \
        v       * (tip)
      gravity
```

### 3.2 Kinematics

Position of center of mass:
$$x_{cm} = l\sin\theta, \quad y_{cm} = -l\cos\theta$$

Velocity:
$$\dot{x}_{cm} = l\cos\theta \cdot \dot{\theta}, \quad \dot{y}_{cm} = l\sin\theta \cdot \dot{\theta}$$

### 3.3 Energy

**Kinetic energy:**
$$T = \frac{1}{2}I_{cm}\dot{\theta}^2 + \frac{1}{2}m(\dot{x}_{cm}^2 + \dot{y}_{cm}^2) = \frac{1}{2}(I_{cm} + ml^2)\dot{\theta}^2 = \frac{1}{2}\bar{M}\dot{\theta}^2$$

where $\bar{M} = I_{cm} + ml^2$ (parallel axis theorem).

For a uniform rod: $I_{cm} = \frac{1}{12}mL^2$, so $\bar{M} = \frac{1}{12}mL^2 + m\left(\frac{L}{2}\right)^2 = \frac{1}{3}mL^2$

**Potential energy:** (taking $y=0$ at pivot)
$$V = mgy_{cm} = -mgl\cos\theta$$

### 3.4 Lagrangian and Equations of Motion

$$\mathcal{L} = T - V = \frac{1}{2}\bar{M}\dot{\theta}^2 + mgl\cos\theta$$

Applying Euler-Lagrange:
$$\frac{\partial \mathcal{L}}{\partial \dot{\theta}} = \bar{M}\dot{\theta} \quad \Rightarrow \quad \frac{d}{dt}\frac{\partial \mathcal{L}}{\partial \dot{\theta}} = \bar{M}\ddot{\theta}$$

$$\frac{\partial \mathcal{L}}{\partial \theta} = -mgl\sin\theta$$

**Equation of motion:**
$$\boxed{\bar{M}\ddot{\theta} + mgl\sin\theta = u}$$

Or solving for $\ddot{\theta}$:
$$\ddot{\theta} = \frac{u - mgl\sin\theta}{\bar{M}}$$

### 3.5 State-Space Form

$$\dot{\mathbf{x}} = \begin{pmatrix} \dot{\theta} \\ \ddot{\theta} \end{pmatrix} = \begin{pmatrix} \dot{\theta} \\ \frac{u - mgl\sin\theta}{\bar{M}} \end{pmatrix} = f(\mathbf{x}, u)$$

### 3.6 Linearization

To design a linear controller (e.g., LQR) for balancing at $\theta^* = \pi$ (upright):

$$\dot{\mathbf{x}} \approx A(\mathbf{x} - \mathbf{x}^*) + B(u - u^*)$$

where $\mathbf{x}^* = [\pi, 0]^T$, $u^* = 0$, and:

$$A = \frac{\partial f}{\partial \mathbf{x}}\bigg|_{\mathbf{x}^*, u^*} = \begin{pmatrix} 0 & 1 \\ \frac{-mgl\cos\theta}{\bar{M}} & 0 \end{pmatrix}_{\theta=\pi} = \begin{pmatrix} 0 & 1 \\ \frac{mgl}{\bar{M}} & 0 \end{pmatrix}$$

$$B = \frac{\partial f}{\partial u}\bigg|_{\mathbf{x}^*, u^*} = \begin{pmatrix} 0 \\ \frac{1}{\bar{M}} \end{pmatrix}$$

### 3.7 Energy-Based Swing-Up Control

**Total energy:**
$$E = T + V = \frac{1}{2}\bar{M}\dot{\theta}^2 - mgl\cos\theta$$

**Desired energy** (at upright position with zero velocity):
$$E^* = -mgl\cos\pi = mgl$$

**Energy rate:**
$$\dot{E} = \bar{M}\dot{\theta}\ddot{\theta} + mgl\sin\theta \cdot \dot{\theta} = \dot{\theta}(u - mgl\sin\theta) + mgl\sin\theta\dot{\theta} = u\dot{\theta}$$

To pump energy into the system when $E < E^*$ and remove it when $E > E^*$:
$$u = -k\dot{\theta}(E - E^*)$$

This drives $E \to E^*$.

---

## 4. Cart-Pole (Inverted Pendulum)

### 4.1 Setup

A cart of mass $M$ moves horizontally. A pole of mass $m$ is attached at the cart by a pivot.

**Generalized coordinates:** $q = [x, \theta]^T$
- $x$ = cart position
- $\theta$ = pole angle from vertical (up = 0)

**State:** $\mathbf{x} = [x, \theta, \dot{x}, \dot{\theta}]^T$

**Input:** $u = f$ = horizontal force on cart

```
                 O (pole tip)
                /
               / 
              /  θ
             /
    ========*======== (cart, mass M)
       |    ^    |
       |    |    |
    ───O────O────O───  (track)
            x -->
            
         u (force)
```

### 4.2 Kinematics

**Cart:**
$$\vec{x}_c = \begin{pmatrix} x \\ 0 \end{pmatrix} \quad \Rightarrow \quad \dot{\vec{x}}_c = \begin{pmatrix} \dot{x} \\ 0 \end{pmatrix}$$

**Pole center of mass:**
$$\vec{x}_p = \begin{pmatrix} x + l\sin\theta \\ -l\cos\theta \end{pmatrix} \quad \Rightarrow \quad \dot{\vec{x}}_p = \begin{pmatrix} \dot{x} + l\cos\theta \cdot \dot{\theta} \\ l\sin\theta \cdot \dot{\theta} \end{pmatrix}$$

*Note: Here $\theta = 0$ is upright, so CoM is at $-l\cos\theta$ (below pivot when upright).*

### 4.3 Energy

**Kinetic energy:**
$$T = \underbrace{\frac{1}{2}M\dot{x}^2}_{\text{cart}} + \underbrace{\frac{1}{2}m\|\dot{\vec{x}}_p\|^2 + \frac{1}{2}I\dot{\theta}^2}_{\text{pole}}$$

Expanding $\|\dot{\vec{x}}_p\|^2$:
$$\|\dot{\vec{x}}_p\|^2 = (\dot{x} + l\cos\theta \cdot \dot{\theta})^2 + (l\sin\theta \cdot \dot{\theta})^2 = \dot{x}^2 + 2l\dot{x}\dot{\theta}\cos\theta + l^2\dot{\theta}^2$$

Therefore:
$$T = \frac{1}{2}M\dot{x}^2 + \frac{1}{2}m(\dot{x}^2 + 2l\dot{x}\dot{\theta}\cos\theta + l^2\dot{\theta}^2) + \frac{1}{2}I\dot{\theta}^2$$

$$\boxed{T = \frac{1}{2}(M+m)\dot{x}^2 + ml\dot{x}\dot{\theta}\cos\theta + \frac{1}{2}\bar{M}\dot{\theta}^2}$$

where $\bar{M} = I + ml^2$.

**Potential energy:**
$$V = mgy_p = -mgl\cos\theta$$

### 4.4 Lagrangian

$$\mathcal{L} = T - V = \frac{1}{2}(M+m)\dot{x}^2 + ml\dot{x}\dot{\theta}\cos\theta + \frac{1}{2}\bar{M}\dot{\theta}^2 + mgl\cos\theta$$

### 4.5 Euler-Lagrange Equations

**For $x$:**
$$\frac{\partial \mathcal{L}}{\partial \dot{x}} = (M+m)\dot{x} + ml\dot{\theta}\cos\theta$$

$$\frac{d}{dt}\frac{\partial \mathcal{L}}{\partial \dot{x}} = (M+m)\ddot{x} + ml\ddot{\theta}\cos\theta - ml\dot{\theta}^2\sin\theta$$

$$\frac{\partial \mathcal{L}}{\partial x} = 0$$

**Equation 1:**
$$\boxed{(M+m)\ddot{x} + ml\ddot{\theta}\cos\theta - ml\dot{\theta}^2\sin\theta = f}$$

**For $\theta$:**
$$\frac{\partial \mathcal{L}}{\partial \dot{\theta}} = ml\dot{x}\cos\theta + \bar{M}\dot{\theta}$$

$$\frac{d}{dt}\frac{\partial \mathcal{L}}{\partial \dot{\theta}} = ml\ddot{x}\cos\theta - ml\dot{x}\dot{\theta}\sin\theta + \bar{M}\ddot{\theta}$$

$$\frac{\partial \mathcal{L}}{\partial \theta} = -ml\dot{x}\dot{\theta}\sin\theta - mgl\sin\theta$$

**Equation 2:**
$$\boxed{ml\ddot{x}\cos\theta + \bar{M}\ddot{\theta} + mgl\sin\theta = 0}$$

### 4.6 Matrix Form

$$\underbrace{\begin{pmatrix} M+m & ml\cos\theta \\ ml\cos\theta & \bar{M} \end{pmatrix}}_{M(q)} \begin{pmatrix} \ddot{x} \\ \ddot{\theta} \end{pmatrix} + \underbrace{\begin{pmatrix} 0 & -ml\dot{\theta}\sin\theta \\ 0 & 0 \end{pmatrix}}_{C(q,\dot{q})} \begin{pmatrix} \dot{x} \\ \dot{\theta} \end{pmatrix} + \underbrace{\begin{pmatrix} 0 \\ mgl\sin\theta \end{pmatrix}}_{G(q)} = \underbrace{\begin{pmatrix} 1 \\ 0 \end{pmatrix}}_{B} f$$

Or compactly:
$$M(q)\ddot{q} + C(q,\dot{q})\dot{q} + G(q) = Bf$$

### 4.7 Solving for Accelerations

To simulate, we need $\ddot{q} = M^{-1}(Bf - C\dot{q} - G)$.

From equations (1) and (2), solve for $\ddot{x}$ and $\ddot{\theta}$:

**From (2):**
$$\ddot{\theta} = \frac{-mgl\sin\theta - ml\ddot{x}\cos\theta}{\bar{M}}$$

**Substitute into (1):**
$$(M+m)\ddot{x} + ml\cos\theta \cdot \frac{-mgl\sin\theta - ml\ddot{x}\cos\theta}{\bar{M}} = f + ml\dot{\theta}^2\sin\theta$$

$$\ddot{x}\left[(M+m) - \frac{(ml\cos\theta)^2}{\bar{M}}\right] = f + ml\dot{\theta}^2\sin\theta + \frac{(ml)^2 g\cos\theta\sin\theta}{\bar{M}}$$

Let $D = (M+m)\bar{M} - (ml)^2\cos^2\theta$ (the determinant of $M(q)$). Then:

$$\boxed{\ddot{x} = \frac{f\bar{M} + ml\dot{\theta}^2\sin\theta \cdot \bar{M} + (ml)^2 g\cos\theta\sin\theta}{D}}$$

$$\boxed{\ddot{\theta} = \frac{-mgl\sin\theta(M+m) - ml\cos\theta(f + ml\dot{\theta}^2\sin\theta)}{D}}$$

Or more simply (for $I = 0$):
$$\ddot{x} = \frac{f + m\sin\theta(l\dot{\theta}^2 + g\cos\theta)}{M + m\sin^2\theta}$$

$$\ddot{\theta} = \frac{-\cos\theta(f + ml\dot{\theta}^2\sin\theta) - (M+m)g\sin\theta}{l(M + m\sin^2\theta)}$$

### 4.8 Linearization for LQR

**Fixed point:** $\mathbf{x}^* = [x^*, 0, 0, 0]^T$, $u^* = 0$ (pole upright, at rest)

At the fixed point ($\theta = 0$):
- $\sin\theta \approx \theta$, $\cos\theta \approx 1$
- $C(q,\dot{q})\dot{q} \approx 0$

Linearizing $\dot{\mathbf{x}} = f(\mathbf{x}, u)$:

$$A = \begin{pmatrix} 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \\ 0 & a_{32} & 0 & 0 \\ 0 & a_{42} & 0 & 0 \end{pmatrix}, \quad B = \begin{pmatrix} 0 \\ 0 \\ b_3 \\ b_4 \end{pmatrix}$$

where (with $D_0 = (M+m)\bar{M} - (ml)^2$):
$$a_{32} = \frac{(ml)^2 g}{D_0}, \quad a_{42} = \frac{(M+m)mgl}{D_0}$$

$$b_3 = \frac{\bar{M}}{D_0}, \quad b_4 = \frac{-ml}{D_0}$$

---

## 5. Double Pendulum

### 5.1 Setup

Two links connected by revolute joints. The first joint is fixed to the world.

**Generalized coordinates:** $q = [\theta_1, \theta_2]^T$
- $\theta_1$ = angle of link 1 from downward vertical
- $\theta_2$ = angle of link 2 relative to link 1

**State:** $\mathbf{x} = [\theta_1, \theta_2, \dot{\theta}_1, \dot{\theta}_2]^T$

```
         O (pivot 1 - fixed)
          \
           \  θ₁
            \
             * (m₁)
              \
               O (pivot 2)
                \
                 \  θ₂ (relative)
                  \
                   * (m₂)
```

### 5.2 Kinematics

**Link 1 center of mass:**
$$\vec{x}_1 = \begin{pmatrix} l_1 \sin\theta_1 \\ -l_1 \cos\theta_1 \end{pmatrix}$$

$$\dot{\vec{x}}_1 = \begin{pmatrix} l_1 c_1 \dot{\theta}_1 \\ l_1 s_1 \dot{\theta}_1 \end{pmatrix}$$

**Link 2 center of mass:**

Position of pivot 2: $(L_1 \sin\theta_1, -L_1\cos\theta_1)$

$$\vec{x}_2 = \begin{pmatrix} L_1 \sin\theta_1 + l_2 \sin(\theta_1 + \theta_2) \\ -L_1 \cos\theta_1 - l_2 \cos(\theta_1 + \theta_2) \end{pmatrix} = \begin{pmatrix} L_1 s_1 + l_2 s_{1+2} \\ -L_1 c_1 - l_2 c_{1+2} \end{pmatrix}$$

$$\dot{\vec{x}}_2 = \begin{pmatrix} L_1 c_1 \dot{\theta}_1 + l_2 c_{1+2} (\dot{\theta}_1 + \dot{\theta}_2) \\ L_1 s_1 \dot{\theta}_1 + l_2 s_{1+2} (\dot{\theta}_1 + \dot{\theta}_2) \end{pmatrix}$$

### 5.3 Kinetic Energy

$$T = \frac{1}{2}m_1 \|\dot{\vec{x}}_1\|^2 + \frac{1}{2}m_2 \|\dot{\vec{x}}_2\|^2$$

**Link 1:**
$$\frac{1}{2}m_1 \|\dot{\vec{x}}_1\|^2 = \frac{1}{2}m_1 l_1^2 \dot{\theta}_1^2$$

**Link 2:**
$$\|\dot{\vec{x}}_2\|^2 = (L_1 c_1 \dot{\theta}_1 + l_2 c_{1+2}(\dot{\theta}_1 + \dot{\theta}_2))^2 + (L_1 s_1 \dot{\theta}_1 + l_2 s_{1+2}(\dot{\theta}_1 + \dot{\theta}_2))^2$$

Expanding (using $\cos^2 + \sin^2 = 1$ and $\cos A \cos B + \sin A \sin B = \cos(A-B)$):

$$= L_1^2 \dot{\theta}_1^2 + 2L_1 l_2 \dot{\theta}_1 (\dot{\theta}_1 + \dot{\theta}_2)(c_1 c_{1+2} + s_1 s_{1+2}) + l_2^2 (\dot{\theta}_1 + \dot{\theta}_2)^2$$

$$= L_1^2 \dot{\theta}_1^2 + 2L_1 l_2 \dot{\theta}_1 (\dot{\theta}_1 + \dot{\theta}_2) \cos\theta_2 + l_2^2 (\dot{\theta}_1 + \dot{\theta}_2)^2$$

*Key simplification: $c_1 c_{1+2} + s_1 s_{1+2} = \cos(\theta_1 - (\theta_1 + \theta_2)) = \cos(-\theta_2) = \cos\theta_2$*

**Total kinetic energy:**
$$\boxed{T = \frac{1}{2}(m_1 + m_2)l_1^2 \dot{\theta}_1^2 + \frac{1}{2}m_2 l_2^2 (\dot{\theta}_1 + \dot{\theta}_2)^2 + m_2 l_1 l_2 c_2 \dot{\theta}_1 (\dot{\theta}_1 + \dot{\theta}_2)}$$

*Note: Using point masses with $l_i = L_i$.*

### 5.4 Potential Energy

$$V = m_1 g y_1 + m_2 g y_2 = -m_1 g l_1 c_1 - m_2 g (l_1 c_1 + l_2 c_{1+2})$$

$$\boxed{V = -(m_1 + m_2) g l_1 c_1 - m_2 g l_2 c_{1+2}}$$

### 5.5 Lagrangian

$$\mathcal{L} = T - V$$

$$= \frac{1}{2}(m_1 + m_2)l_1^2 \dot{\theta}_1^2 + \frac{1}{2}m_2 l_2^2 (\dot{\theta}_1 + \dot{\theta}_2)^2 + m_2 l_1 l_2 c_2 \dot{\theta}_1 (\dot{\theta}_1 + \dot{\theta}_2)$$
$$+ (m_1 + m_2) g l_1 c_1 + m_2 g l_2 c_{1+2}$$

### 5.6 Euler-Lagrange Calculations

**Partials for $\dot{\theta}_1$:**
$$\frac{\partial T}{\partial \dot{\theta}_1} = (m_1 + m_2)l_1^2 \dot{\theta}_1 + m_2 l_2^2 (\dot{\theta}_1 + \dot{\theta}_2) + m_2 l_1 l_2 c_2 (2\dot{\theta}_1 + \dot{\theta}_2)$$

$$\frac{d}{dt}\frac{\partial T}{\partial \dot{\theta}_1} = (m_1 + m_2)l_1^2 \ddot{\theta}_1 + m_2 l_2^2 (\ddot{\theta}_1 + \ddot{\theta}_2) + m_2 l_1 l_2 c_2 (2\ddot{\theta}_1 + \ddot{\theta}_2) - m_2 l_1 l_2 s_2 \dot{\theta}_2 (2\dot{\theta}_1 + \dot{\theta}_2)$$

**Partials for $\theta_1$:**
$$\frac{\partial T}{\partial \theta_1} = 0$$

$$\frac{\partial V}{\partial \theta_1} = (m_1 + m_2) g l_1 s_1 + m_2 g l_2 s_{1+2}$$

**Equation for $\theta_1$:**

$$\boxed{\tau_1 = [(m_1+m_2)l_1^2 + m_2 l_2^2 + 2m_2 l_1 l_2 c_2]\ddot{\theta}_1 + [m_2 l_2^2 + m_2 l_1 l_2 c_2]\ddot{\theta}_2}$$
$$\boxed{\quad - m_2 l_1 l_2 s_2 (2\dot{\theta}_1 + \dot{\theta}_2)\dot{\theta}_2 + (m_1+m_2) g l_1 s_1 + m_2 g l_2 s_{1+2}}$$

**Partials for $\dot{\theta}_2$:**
$$\frac{\partial T}{\partial \dot{\theta}_2} = m_2 l_2^2 (\dot{\theta}_1 + \dot{\theta}_2) + m_2 l_1 l_2 c_2 \dot{\theta}_1$$

$$\frac{d}{dt}\frac{\partial T}{\partial \dot{\theta}_2} = m_2 l_2^2 (\ddot{\theta}_1 + \ddot{\theta}_2) + m_2 l_1 l_2 c_2 \ddot{\theta}_1 - m_2 l_1 l_2 s_2 \dot{\theta}_1 \dot{\theta}_2$$

**Partials for $\theta_2$:**
$$\frac{\partial T}{\partial \theta_2} = -m_2 l_1 l_2 s_2 \dot{\theta}_1 (\dot{\theta}_1 + \dot{\theta}_2)$$

$$\frac{\partial V}{\partial \theta_2} = m_2 g l_2 s_{1+2}$$

**Equation for $\theta_2$:**

$$\boxed{\tau_2 = [m_2 l_2^2 + m_2 l_1 l_2 c_2]\ddot{\theta}_1 + m_2 l_2^2 \ddot{\theta}_2 + m_2 l_1 l_2 s_2 \dot{\theta}_1^2 + m_2 g l_2 s_{1+2}}$$

### 5.7 Matrix Form

$$M(q) = \begin{pmatrix} (m_1+m_2)l_1^2 + m_2 l_2^2 + 2m_2 l_1 l_2 c_2 & m_2 l_2^2 + m_2 l_1 l_2 c_2 \\ m_2 l_2^2 + m_2 l_1 l_2 c_2 & m_2 l_2^2 \end{pmatrix}$$

$$C(q, \dot{q})\dot{q} = \begin{pmatrix} -m_2 l_1 l_2 s_2 (2\dot{\theta}_1 + \dot{\theta}_2)\dot{\theta}_2 \\ m_2 l_1 l_2 s_2 \dot{\theta}_1^2 \end{pmatrix}$$

$$G(q) = \begin{pmatrix} (m_1+m_2) g l_1 s_1 + m_2 g l_2 s_{1+2} \\ m_2 g l_2 s_{1+2} \end{pmatrix}$$

For the **passive double pendulum**: $\tau_1 = \tau_2 = 0$.

---

## 6. Dual Inverted Pendulum (Cart + Two Poles)

### 6.1 Setup

A cart with two independent pendulums attached at the same pivot point. This is significantly underactuated (3 DOF, 1 actuator).

**Generalized coordinates:** $q = [x, \theta_1, \theta_2]^T$
- $x$ = cart position
- $\theta_1$ = angle of pole 1 from vertical
- $\theta_2$ = angle of pole 2 from vertical

**State:** $\mathbf{y} = [x, \dot{x}, \theta_1, \dot{\theta}_1, \theta_2, \dot{\theta}_2]^T$

**Input:** $u = f$ = horizontal force on cart

```
           O (pole 1)      O (pole 2)
          /              /
         /              /
        / θ₁          / θ₂
       /              /
    ==*======O======*==  (cart)
         ^
         |
      x, u (force)
```

### 6.2 Kinematics

**Cart:**
$$\vec{x}_c = \begin{pmatrix} x \\ 0 \end{pmatrix}$$

**Pole 1 center of mass:**
$$\vec{x}_1 = \begin{pmatrix} x + l_1 s_1 \\ l_1 c_1 \end{pmatrix} \quad \Rightarrow \quad \dot{\vec{x}}_1 = \begin{pmatrix} \dot{x} + l_1 \dot{\theta}_1 c_1 \\ -l_1 \dot{\theta}_1 s_1 \end{pmatrix}$$

**Pole 2 center of mass:**
$$\vec{x}_2 = \begin{pmatrix} x + l_2 s_2 \\ l_2 c_2 \end{pmatrix} \quad \Rightarrow \quad \dot{\vec{x}}_2 = \begin{pmatrix} \dot{x} + l_2 \dot{\theta}_2 c_2 \\ -l_2 \dot{\theta}_2 s_2 \end{pmatrix}$$

*Note: Here $\theta = 0$ is upright (pole pointing up).*

### 6.3 Kinetic Energy

$$T = T_{cart} + T_1 + T_2$$

$$T = \frac{1}{2}M_c \dot{x}^2 + \frac{1}{2}m_1 \|\dot{\vec{x}}_1\|^2 + \frac{1}{2}I_1 \dot{\theta}_1^2 + \frac{1}{2}m_2 \|\dot{\vec{x}}_2\|^2 + \frac{1}{2}I_2 \dot{\theta}_2^2$$

Expanding:
$$\|\dot{\vec{x}}_1\|^2 = (\dot{x} + l_1 \dot{\theta}_1 c_1)^2 + l_1^2 \dot{\theta}_1^2 s_1^2 = \dot{x}^2 + 2l_1 \dot{x}\dot{\theta}_1 c_1 + l_1^2 \dot{\theta}_1^2$$

Similarly for pole 2.

$$\boxed{T = \frac{1}{2}(M_c + m_1 + m_2)\dot{x}^2 + m_1 l_1 \dot{\theta}_1 \dot{x} c_1 + m_2 l_2 \dot{\theta}_2 \dot{x} c_2 + \frac{1}{2}\bar{M}_1 \dot{\theta}_1^2 + \frac{1}{2}\bar{M}_2 \dot{\theta}_2^2}$$

where $\bar{M}_i = I_i + m_i l_i^2$.

### 6.4 Potential Energy

$$V = m_1 g y_1 + m_2 g y_2 = m_1 g l_1 c_1 + m_2 g l_2 c_2$$

### 6.5 Lagrangian

$$\mathcal{L} = \frac{1}{2}M_T \dot{x}^2 + m_1 l_1 \dot{\theta}_1 \dot{x} c_1 + m_2 l_2 \dot{\theta}_2 \dot{x} c_2 + \frac{1}{2}\bar{M}_1 \dot{\theta}_1^2 + \frac{1}{2}\bar{M}_2 \dot{\theta}_2^2 - m_1 g l_1 c_1 - m_2 g l_2 c_2$$

where $M_T = M_c + m_1 + m_2$.

### 6.6 Equations of Motion

**For $x$:**

$$\frac{\partial \mathcal{L}}{\partial \dot{x}} = M_T \dot{x} + m_1 l_1 \dot{\theta}_1 c_1 + m_2 l_2 \dot{\theta}_2 c_2$$

$$\frac{d}{dt}\frac{\partial \mathcal{L}}{\partial \dot{x}} = M_T \ddot{x} + m_1 l_1 \ddot{\theta}_1 c_1 + m_2 l_2 \ddot{\theta}_2 c_2 - m_1 l_1 \dot{\theta}_1^2 s_1 - m_2 l_2 \dot{\theta}_2^2 s_2$$

$$\frac{\partial \mathcal{L}}{\partial x} = 0$$

**Equation (1):**
$$\boxed{M_T \ddot{x} + m_1 l_1 \ddot{\theta}_1 c_1 + m_2 l_2 \ddot{\theta}_2 c_2 - m_1 l_1 \dot{\theta}_1^2 s_1 - m_2 l_2 \dot{\theta}_2^2 s_2 = f}$$

**For $\theta_1$:**

$$\frac{\partial \mathcal{L}}{\partial \dot{\theta}_1} = m_1 l_1 \dot{x} c_1 + \bar{M}_1 \dot{\theta}_1$$

$$\frac{d}{dt}\frac{\partial \mathcal{L}}{\partial \dot{\theta}_1} = m_1 l_1 \ddot{x} c_1 - m_1 l_1 \dot{x} \dot{\theta}_1 s_1 + \bar{M}_1 \ddot{\theta}_1$$

$$\frac{\partial \mathcal{L}}{\partial \theta_1} = -m_1 l_1 \dot{\theta}_1 \dot{x} s_1 + m_1 g l_1 s_1$$

**Equation (2):**
$$\boxed{m_1 l_1 \ddot{x} c_1 + \bar{M}_1 \ddot{\theta}_1 + m_1 g l_1 s_1 = 0}$$

**For $\theta_2$:** (by symmetry)

**Equation (3):**
$$\boxed{m_2 l_2 \ddot{x} c_2 + \bar{M}_2 \ddot{\theta}_2 + m_2 g l_2 s_2 = 0}$$

### 6.7 Matrix Form

$$\underbrace{\begin{pmatrix} M_T & m_1 l_1 c_1 & m_2 l_2 c_2 \\ m_1 l_1 c_1 & \bar{M}_1 & 0 \\ m_2 l_2 c_2 & 0 & \bar{M}_2 \end{pmatrix}}_{M(q)} \begin{pmatrix} \ddot{x} \\ \ddot{\theta}_1 \\ \ddot{\theta}_2 \end{pmatrix} + \begin{pmatrix} 0 \\ m_1 g l_1 s_1 \\ m_2 g l_2 s_2 \end{pmatrix} = \begin{pmatrix} f + \sum_i m_i l_i \dot{\theta}_i^2 s_i \\ 0 \\ 0 \end{pmatrix}$$

*Note: The two poles don't directly interact — their coupling is only through the cart.*

### 6.8 Solving for Accelerations

From (2): 
$$\ddot{\theta}_1 = \frac{-m_1 l_1 (g s_1 + \ddot{x} c_1)}{\bar{M}_1}$$

From (3):
$$\ddot{\theta}_2 = \frac{-m_2 l_2 (g s_2 + \ddot{x} c_2)}{\bar{M}_2}$$

Substituting into (1) and solving for $\ddot{x}$:

$$\boxed{\ddot{x} = \frac{f + \sum_i m_i l_i s_i \dot{\theta}_i^2 + \sum_i \frac{(m_i l_i)^2}{\bar{M}_i} g c_i s_i}{M_T - \sum_i \frac{(m_i l_i)^2 c_i^2}{\bar{M}_i}}}$$

**Key observation:** $\bar{M}_1, \bar{M}_2, M_T$ are all **constants** — they don't depend on configuration!

---

## 7. Wheeled Inverted Pendulum (Segway-type)

### 7.1 Setup

A wheel of mass $M$ and radius $R$ with a body/pendulum of mass $m$ attached above it. The motor applies torque between the wheel and body (like a Segway or self-balancing robot).

**Generalized coordinates:** $q = [\phi, \theta]^T$
- $\phi$ = wheel rotation angle
- $\theta$ = body angle from vertical ($\theta = 0$ is upright)

**Derived quantity:** $x = R\phi$ (horizontal position of wheel center)

**State:** $\mathbf{x} = [\phi, \theta, \dot{\phi}, \dot{\theta}]^T$

**Input:** $\tau$ = motor torque (applied to wheel, with reaction on body)

```
              O  (body CoM)
             /|
            / |
           /  | l
          /   |
         / θ  |
        /_____|
       (  O  )  ← wheel (mass M, radius R, inertia I_w)
    ─────────────
         ↑
       x = Rφ
```

*Key difference from cart-pole:* The motor torque $\tau$ acts on the wheel, but the **reaction torque** $-\tau$ acts on the body. This couples the two equations differently than the cart-pole.

### 7.2 Kinematics

**Wheel center:**
$$\vec{x}_w = \begin{pmatrix} R\phi \\ 0 \end{pmatrix} = \begin{pmatrix} x \\ 0 \end{pmatrix} \quad \Rightarrow \quad \dot{\vec{x}}_w = \begin{pmatrix} R\dot{\phi} \\ 0 \end{pmatrix}$$

**Body center of mass:**
$$\vec{x}_b = \vec{x}_w + \begin{pmatrix} l\sin\theta \\ -l\cos\theta \end{pmatrix} = \begin{pmatrix} x + l s_\theta \\ -l c_\theta \end{pmatrix}$$

$$\dot{\vec{x}}_b = \begin{pmatrix} R\dot{\phi} + l\dot{\theta} c_\theta \\ l\dot{\theta} s_\theta \end{pmatrix}$$

### 7.3 Kinetic Energy

The kinetic energy has four components:

$$T = T_w^{trans} + T_w^{rot} + T_b^{trans} + T_b^{rot}$$

**Wheel translation:**
$$T_w^{trans} = \frac{1}{2}M\|\dot{\vec{x}}_w\|^2 = \frac{1}{2}MR^2\dot{\phi}^2$$

**Wheel rotation:**
$$T_w^{rot} = \frac{1}{2}I_w\dot{\phi}^2$$

**Body translation:**
$$T_b^{trans} = \frac{1}{2}m\|\dot{\vec{x}}_b\|^2$$

Expanding:
$$\|\dot{\vec{x}}_b\|^2 = (R\dot{\phi} + l\dot{\theta}c_\theta)^2 + (l\dot{\theta}s_\theta)^2$$
$$= R^2\dot{\phi}^2 + 2Rl\dot{\phi}\dot{\theta}c_\theta + l^2\dot{\theta}^2c_\theta^2 + l^2\dot{\theta}^2s_\theta^2$$
$$= R^2\dot{\phi}^2 + 2Rl\dot{\phi}\dot{\theta}c_\theta + l^2\dot{\theta}^2$$

**Body rotation:**
$$T_b^{rot} = \frac{1}{2}I_b\dot{\theta}^2$$

**Total kinetic energy:**
$$T = \frac{1}{2}MR^2\dot{\phi}^2 + \frac{1}{2}I_w\dot{\phi}^2 + \frac{1}{2}m(R^2\dot{\phi}^2 + 2Rl\dot{\phi}\dot{\theta}c_\theta + l^2\dot{\theta}^2) + \frac{1}{2}I_b\dot{\theta}^2$$

$$\boxed{T = \frac{1}{2}\left[(M+m)R^2 + I_w\right]\dot{\phi}^2 + mRl\dot{\phi}\dot{\theta}c_\theta + \frac{1}{2}\left(ml^2 + I_b\right)\dot{\theta}^2}$$

Let $\bar{M}_\phi = (M+m)R^2 + I_w$ (effective inertia for wheel rotation) and $\bar{M}_\theta = ml^2 + I_b$ (effective inertia for body rotation).

$$T = \frac{1}{2}\bar{M}_\phi\dot{\phi}^2 + mRl\dot{\phi}\dot{\theta}c_\theta + \frac{1}{2}\bar{M}_\theta\dot{\theta}^2$$

### 7.4 Potential Energy

$$V = mgy_b = -mglc_\theta$$

### 7.5 Lagrangian

$$\mathcal{L} = T - V = \frac{1}{2}\bar{M}_\phi\dot{\phi}^2 + mRl\dot{\phi}\dot{\theta}c_\theta + \frac{1}{2}\bar{M}_\theta\dot{\theta}^2 + mglc_\theta$$

### 7.6 Euler-Lagrange Equations

**For $\phi$ (wheel angle):**

$$\frac{\partial \mathcal{L}}{\partial \dot{\phi}} = \bar{M}_\phi\dot{\phi} + mRl\dot{\theta}c_\theta$$

$$\frac{d}{dt}\frac{\partial \mathcal{L}}{\partial \dot{\phi}} = \bar{M}_\phi\ddot{\phi} + mRl\ddot{\theta}c_\theta - mRl\dot{\theta}^2 s_\theta$$

$$\frac{\partial \mathcal{L}}{\partial \phi} = 0$$

**Equation (1) — Wheel dynamics:**
$$\boxed{\bar{M}_\phi\ddot{\phi} + mRlc_\theta\ddot{\theta} - mRl\dot{\theta}^2 s_\theta = \tau}$$

**For $\theta$ (body angle):**

$$\frac{\partial \mathcal{L}}{\partial \dot{\theta}} = mRl\dot{\phi}c_\theta + \bar{M}_\theta\dot{\theta}$$

$$\frac{d}{dt}\frac{\partial \mathcal{L}}{\partial \dot{\theta}} = mRl\ddot{\phi}c_\theta - mRl\dot{\phi}\dot{\theta}s_\theta + \bar{M}_\theta\ddot{\theta}$$

$$\frac{\partial \mathcal{L}}{\partial \theta} = -mRl\dot{\phi}\dot{\theta}s_\theta - mgls_\theta$$

**Equation (2) — Body dynamics:**

The generalized force for $\theta$ is the reaction torque from the motor: $\xi = -\tau$

$$mRlc_\theta\ddot{\phi} + \bar{M}_\theta\ddot{\theta} + mgls_\theta = -\tau$$

$$\boxed{mRlc_\theta\ddot{\phi} + \bar{M}_\theta\ddot{\theta} + mgls_\theta = -\tau}$$

*Note: The $-\tau$ on the RHS is the key difference from cart-pole. The motor torque $\tau$ that drives the wheel forward creates a reaction torque $-\tau$ that tips the body backward.*

### 7.7 Matrix Form

$$\underbrace{\begin{pmatrix} \bar{M}_\phi & mRlc_\theta \\ mRlc_\theta & \bar{M}_\theta \end{pmatrix}}_{M(q)} \begin{pmatrix} \ddot{\phi} \\ \ddot{\theta} \end{pmatrix} + \underbrace{\begin{pmatrix} 0 & -mRl\dot{\theta}s_\theta \\ 0 & 0 \end{pmatrix}}_{C(q,\dot{q})} \begin{pmatrix} \dot{\phi} \\ \dot{\theta} \end{pmatrix} + \underbrace{\begin{pmatrix} 0 \\ mgls_\theta \end{pmatrix}}_{G(q)} = \underbrace{\begin{pmatrix} 1 \\ -1 \end{pmatrix}}_{B} \tau$$

*Compare to cart-pole:* In the cart-pole, $B = [1, 0]^T$. Here $B = [1, -1]^T$ because the motor torque appears with opposite signs in the two equations.

### 7.8 Solving for Accelerations

From equations (1) and (2):

**From (2):**
$$\ddot{\theta} = \frac{-\tau - mgls_\theta - mRlc_\theta\ddot{\phi}}{\bar{M}_\theta}$$

**Substitute into (1):**
$$\bar{M}_\phi\ddot{\phi} + mRlc_\theta \cdot \frac{-\tau - mgls_\theta - mRlc_\theta\ddot{\phi}}{\bar{M}_\theta} = \tau + mRl\dot{\theta}^2 s_\theta$$

Let $D = \bar{M}_\phi\bar{M}_\theta - (mRlc_\theta)^2$ (determinant of mass matrix). Then:

$$\boxed{\ddot{\phi} = \frac{(\tau + mRls_\theta\dot{\theta}^2)\bar{M}_\theta - mRlc_\theta(-\tau - mgls_\theta)}{D}}$$

$$= \frac{\tau(\bar{M}_\theta + mRlc_\theta) + mRl\dot{\theta}^2 s_\theta \bar{M}_\theta + (mRl)^2 gc_\theta s_\theta}{D}$$

$$\boxed{\ddot{\theta} = \frac{-\tau(\bar{M}_\phi + mRlc_\theta) - mgls_\theta\bar{M}_\phi - (mRl)^2\dot{\theta}^2 c_\theta s_\theta}{D}}$$

Or more compactly:

$$\ddot{\phi} = \frac{(\tau + mRls_\theta\dot{\theta}^2)(\bar{M}_\theta) + mRlc_\theta(\tau + mgls_\theta)}{D}$$

$$\ddot{\theta} = \frac{-(\tau + mgls_\theta)(\bar{M}_\phi) - mRlc_\theta(\tau + mRls_\theta\dot{\theta}^2)}{D}$$

### 7.9 Comparison: Cart-Pole vs. Wheeled Inverted Pendulum

| Aspect | Cart-Pole | Wheeled Inv. Pend. |
|--------|-----------|-------------------|
| Input | Force $f$ on cart | Torque $\tau$ on wheel |
| Input matrix $B$ | $[1, 0]^T$ | $[1, -1]^T$ |
| Coupling | Force doesn't directly affect $\theta$ | Reaction torque directly affects $\theta$ |
| Locomotion | Cart moves linearly | Wheel rolls (no slip assumed) |
| Constraint | None | $x = R\phi$ (rolling) |

*Physical intuition:* In the cart-pole, pushing the cart forward doesn't directly torque the pendulum—it only accelerates the pivot point. In the wheeled system, spinning the wheel faster creates a direct reaction torque that tips the body.

### 7.10 Linearization

**Fixed point:** $\mathbf{x}^* = [\phi^*, 0, 0, 0]^T$ (body upright, at rest)

At $\theta = 0$: $s_\theta \approx \theta$, $c_\theta \approx 1$

Linearized equations:
$$\bar{M}_\phi\ddot{\phi} + mRl\ddot{\theta} = \tau$$
$$mRl\ddot{\phi} + \bar{M}_\theta\ddot{\theta} + mgl\theta = -\tau$$

**State-space form:** $\dot{\mathbf{x}} = A\mathbf{x} + B\tau$

With $D_0 = \bar{M}_\phi\bar{M}_\theta - (mRl)^2$:

$$A = \begin{pmatrix} 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \\ 0 & \frac{(mRl)(mgl)}{D_0} & 0 & 0 \\ 0 & \frac{\bar{M}_\phi(mgl)}{D_0} & 0 & 0 \end{pmatrix}, \quad B = \begin{pmatrix} 0 \\ 0 \\ \frac{\bar{M}_\theta + mRl}{D_0} \\ \frac{-(\bar{M}_\phi + mRl)}{D_0} \end{pmatrix}$$

---

## 8. Summary and Comparison

### 8.1 System Comparison Table

| System | $n$ | $m$ | Mass Matrix | Coupling |
|--------|-----|-----|-------------|----------|
| Simple Pendulum | 1 | 1 | Scalar | — |
| Cart-Pole | 2 | 1 | 2×2 | $\cos\theta$ off-diagonal |
| Double Pendulum | 2 | 1 | 2×2 | $\cos\theta_2$ off-diagonal |
| Dual Inv. Pend. | 3 | 1 | 3×3 block | Poles coupled through cart |
| Wheeled Inv. Pend. | 2 | 1 | 2×2 | $\cos\theta$ off-diagonal, $B = [1,-1]^T$ |

### 8.2 Common Structure

All systems share:
$$M(q)\ddot{q} + C(q, \dot{q})\dot{q} + G(q) = Bu$$

**Properties:**
- $M(q)$ is symmetric positive definite
- $\dot{M} - 2C$ is skew-symmetric
- Energy: $E = \frac{1}{2}\dot{q}^T M(q) \dot{q} + V(q)$
- Power: $\dot{E} = \dot{q}^T B u$

---

## 9. Control Strategies (Overview)

### 9.1 LQR (Linear Quadratic Regulator)

For stabilization near equilibrium:
$$J = \int_0^\infty (\mathbf{x}^T Q \mathbf{x} + u^T R u) \, dt$$

```python
def lqr(A, B, Q, R):
    P = riccati(A, B, Q, R)
    K = np.linalg.solve(R, B.T @ P)
    return K
```

### 9.2 Energy-Based Swing-Up

$$u = -k \dot{\theta} (E - E^*)$$

### 9.3 Switching Control

```python
if near_upright(state):
    u = lqr_controller(state)
else:
    u = energy_controller(state)
```

---

## References

- Underactuated Robotics (Russ Tedrake, MIT OCW)
- Spong, Hutchinson, Vidyasagar — *Robot Modeling and Control*
- Murray, Li, Sastry — *A Mathematical Introduction to Robotic Manipulation*

---

## Appendix: Useful Identities

### Trigonometric
$$c_{1+2} = c_1 c_2 - s_1 s_2$$
$$s_{1+2} = s_1 c_2 + c_1 s_2$$
$$c_1 c_{1+2} + s_1 s_{1+2} = c_2$$

### Parallel Axis Theorem
$$I_{pivot} = I_{cm} + m d^2$$

For uniform rod of length $L$: $I_{cm} = \frac{1}{12}mL^2$

### Matrix Inverse (2×2)
$$\begin{pmatrix} a & b \\ c & d \end{pmatrix}^{-1} = \frac{1}{ad - bc}\begin{pmatrix} d & -b \\ -c & a \end{pmatrix}$$
