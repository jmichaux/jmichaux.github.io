# Denoising Diffusion Probabilistic Models (DDPMs)

*Working notes on the mathematical foundations of diffusion models*

---

## From VAEs to Hierarchical VAEs

Before diving into diffusion models, it helps to understand hierarchical VAEs as a stepping stone.

### Hierarchical VAE Structure

Instead of a single latent $z$, we have a chain of latents $z_1, z_2, \ldots, z_T$:

```
    p(x|z₁)      p(z₁|z₂)              p(z_{T-1}|z_T)
x ←────────── z₁ ←────────── z₂  ···  ←────────────── z_T
      ↓            ↓                        ↓
   g(z₁|x)     g(z₂|z₁)               g(z_T|z_{T-1})
```

**Decoder (generative model):**
$$p(x, z_{1:T}) = p(z_T) \cdot p_\theta(x|z_1) \cdot \prod_{t=2}^{T} p_\theta(z_{t-1}|z_t)$$

**Encoder (inference model):**
$$g(z_{1:T}|x; \phi) = g(z_1|x; \phi) \prod_{t=2}^{T} g(z_t|z_{t-1}; \phi)$$

### ELBO for Hierarchical VAEs

$$\log p(x) = \log \int p(x, z_{1:T}) \, dz_{1:T}$$

Using the same trick as before (multiply by encoder, apply Jensen's):

$$\geq \mathbb{E}_{g(z_{1:T}|x; \phi)} \left[ \log \frac{p(x, z_{1:T})}{g(z_{1:T}|x; \phi)} \right]$$

---

## Variational Diffusion Models (VDMs)

VDMs are a special case of hierarchical VAEs with specific structural constraints.

### Key Assumptions

1. **Latent dimension matches data dimension:** The latent dimension is the same as the data dimension at each timestep.

2. **Fixed encoder structure:** The structure of the latent encoder at each timestep is **not learned**. It is a predefined linear Gaussian centered around the output from the previous timestep.

3. **Variance schedule:** Gaussian parameters of the latent encoders vary over time such that the distribution of the latent at $T$ is a standard Gaussian.

### Notation

- $x_0$: true data samples
- $x_t$ for $t \in [1, T]$: latents (we use $x_t$ instead of $z_t$ since dimensions match)

### The Forward Process (Encoder)

The encoder transitions are:

$$g(x_t|x_{t-1}) = \mathcal{N}(x_t; \sqrt{\alpha_t} \, x_{t-1}, (1-\alpha_t)I)$$

**Key properties:**
- The mean is $\sqrt{\alpha_t} \, x_{t-1}$ (scaled previous state)
- The variance is $(1-\alpha_t)I$
- The encoding is **variance preserving**
- $\alpha_t$ evolves over time as a fixed or learnable schedule such that $p(x_T)$ is a standard Gaussian

The full posterior:
$$g(x_{1:T}|x_0) = g(x_1|x_0) \prod_{t=1}^{T} g(x_t|x_{t-1})$$

### The Reverse Process (Decoder)

$$p(x_{0:T}; \theta) = p(x_T) \prod_{t=1}^{T} p_\theta(x_{t-1}|x_t)$$

where $p(x_T) = \mathcal{N}(x_T; 0, I)$.

**Critical insight:** The encoder is no longer parameterized by $\phi$ — it's fixed! VDMs only learn the conditionals $p_\theta(x_{t-1}|x_t)$.

These steps describe how to **steadily noisify an image over time with Gaussian noise**.

---

## ELBO for VDM (First Derivation)

Starting from the hierarchical VAE ELBO:

$$\log p(x) \geq \mathbb{E}_{g(x_{1:T}|x_0)} \left[ \log \frac{p(x_{0:T}; \theta)}{g(x_{1:T}|x_0)} \right]$$

Expanding the joint distributions:

$$= \mathbb{E}_{g(x_{1:T}|x_0)} \left[ \log \frac{p(x_T) \cdot p_\theta(x_0|x_1) \cdot \prod_{t=2}^{T} p_\theta(x_{t-1}|x_t)}{g(x_T|x_{T-1}) \prod_{t=1}^{T-1} g(x_t|x_{t-1})} \right]$$

After careful manipulation (matching indices), this becomes:

$$= \underbrace{\mathbb{E}_{g(x_{1:T}|x_0)}[\log p_\theta(x_0|x_1)]}_{\text{① Reconstruction term}} - \underbrace{\mathbb{E}_{g(x_{T-1}|x_0)}[D_{KL}(g(x_T|x_{T-1}) \| p(x_T))]}_{\text{② Prior matching term}}$$
$$- \underbrace{\sum_{t=1}^{T-1} \mathbb{E}_{g(x_{t-1}, x_{t+1}|x_0)} [D_{KL}(g(x_t|x_{t-1}) \| p_\theta(x_t|x_{t+1}))]}_{\text{③ Consistency term}}$$

### Problems with This Form

The **consistency term** is problematic:
- We're sampling over two random variables ($x_{t-1}$ and $x_{t+1}$)
- This can introduce higher variance
- Also, the prior matching term doesn't have any trainable parameters

**Solution:** Re-derive the ELBO in a different way.

---

## ELBO for VDM (Better Derivation)

Using Bayes' theorem to rewrite $g(x_t|x_{t-1}, x_0)$:

$$g(x_t|x_{t-1}, x_0) = \frac{g(x_{t-1}|x_t, x_0) \cdot g(x_t|x_0)}{g(x_{t-1}|x_0)}$$

**Key insight (Markov property):** $g(x_t|x_{t-1}) = g(x_t|x_{t-1}, x_0)$ since given $x_{t-1}$, the process doesn't depend on $x_0$.

After a lengthy derivation using this substitution, we get the **improved ELBO**:

$$\log p(x) \geq \underbrace{\mathbb{E}_{g(x_1|x_0)}[\log p_\theta(x_0|x_1)]}_{\text{Reconstruction}} - \underbrace{D_{KL}(g(x_T|x_0) \| p(x_T))}_{\text{Prior matching}}$$
$$- \underbrace{\sum_{t=2}^{T} \mathbb{E}_{g(x_t|x_0)} \left[ D_{KL}(g(x_{t-1}|x_t, x_0) \| p_\theta(x_{t-1}|x_t)) \right]}_{\text{Denoising matching term}}$$

### Why This Is Better

1. **Reconstruction term:** Same as before — how well can we reconstruct $x_0$ from $x_1$?

2. **Prior matching term:** $D_{KL}(g(x_T|x_0) \| p(x_T))$ — no trainable parameters, but it goes to 0 as $T \to \infty$ by construction of the noise schedule.

3. **Denoising matching term:** This is the key! We're matching:
   - $g(x_{t-1}|x_t, x_0)$: the true reverse process (which we can compute!)
   - $p_\theta(x_{t-1}|x_t)$: our learned reverse process

---

## Understanding the Transition Distribution

### Why $\sqrt{\alpha}$ and $\sqrt{1-\alpha}$?

Consider the encoder transition:
$$g(x_t|x_{t-1}; \phi) = \mathcal{N}(x_t; \sqrt{\alpha_t} \, x_{t-1}, (1-\alpha_t)I)$$

**Theorem 2.1 (Chan):** Suppose $g(x_t|x_{t-1}; \phi) = \mathcal{N}(x_t; a \, x_{t-1}, b^2 I)$ for some $a, b \in \mathbb{R}$.

If we choose $a = \sqrt{\alpha}$ and $b = \sqrt{1-\alpha}$, then as $t \to \infty$, $x_t$ will converge to $\mathcal{N}(0, I)$.

### Proof Sketch

Using the reparameterization trick:
$$x_t = a \, x_{t-1} + b \, \epsilon_{t-1}, \quad \epsilon_{t-1} \sim \mathcal{N}(0, I)$$

Recursively expanding:
$$x_t = a^t x_0 + b[\epsilon_{t-1} + a\epsilon_{t-2} + a^2\epsilon_{t-3} + \cdots + a^{t-1}\epsilon_0]$$

Let $W_t = \epsilon_{t-1} + a\epsilon_{t-2} + \cdots + a^{t-1}\epsilon_0$.

This is a sum of independent Gaussians, so:
- $\mathbb{E}[W_t] = 0$
- $\text{Cov}[W_t] = b^2 \sum_{k=0}^{t-1} a^{2k} \cdot I = b^2 \cdot \frac{1-a^{2t}}{1-a^2} \cdot I$

For $0 < a < 1$, as $t \to \infty$:
$$\lim_{t \to \infty} \text{Cov}[W_t] = \frac{b^2}{1-a^2} I$$

This equals $I$ if and only if $\frac{b^2}{1-a^2} = 1$, i.e., $b^2 = 1 - a^2$.

If $a^2 = \alpha$, then $a = \sqrt{\alpha}$ and $b = \sqrt{1-\alpha}$.

---

## Direct Sampling: $g(x_t|x_0)$

**Theorem 2.2:** The conditional distribution $g(x_t|x_0; \phi)$ is given by:

$$g(x_t|x_0; \phi) = \mathcal{N}(x_t; \sqrt{\bar{\alpha}_t} \, x_0, (1-\bar{\alpha}_t)I)$$

where $\bar{\alpha}_t = \prod_{i=1}^{t} \alpha_i$.

### Proof

Starting from:
$$x_t = \sqrt{\alpha_t} \, x_{t-1} + \sqrt{1-\alpha_t} \, \epsilon_{t-1}$$

Substituting recursively:
$$x_t = \sqrt{\alpha_t}(\sqrt{\alpha_{t-1}} \, x_{t-2} + \sqrt{1-\alpha_{t-1}} \, \epsilon_{t-2}) + \sqrt{1-\alpha_t} \, \epsilon_{t-1}$$

$$= \sqrt{\alpha_t \alpha_{t-1}} \, x_{t-2} + \sqrt{\alpha_t(1-\alpha_{t-1})} \, \epsilon_{t-2} + \sqrt{1-\alpha_t} \, \epsilon_{t-1}$$

The noise terms: We're adding two independent Gaussians:
- $\sqrt{\alpha_t(1-\alpha_{t-1})} \, \epsilon_{t-2}$ with variance $\alpha_t(1-\alpha_{t-1})$
- $\sqrt{1-\alpha_t} \, \epsilon_{t-1}$ with variance $1-\alpha_t$

Total variance: $\alpha_t(1-\alpha_{t-1}) + (1-\alpha_t) = \alpha_t - \alpha_t\alpha_{t-1} + 1 - \alpha_t = 1 - \alpha_t\alpha_{t-1}$

Continuing this pattern:
$$x_t = \sqrt{\prod_{i=1}^{t} \alpha_i} \, x_0 + \sqrt{1 - \prod_{i=1}^{t} \alpha_i} \, \epsilon_0$$

$$= \sqrt{\bar{\alpha}_t} \, x_0 + \sqrt{1-\bar{\alpha}_t} \, \epsilon_0$$

Therefore: $g(x_t|x_0; \phi) = \mathcal{N}(x_t; \sqrt{\bar{\alpha}_t} \, x_0, (1-\bar{\alpha}_t)I)$

**This is crucial!** We can sample $x_t$ directly from $x_0$ without iterating through all intermediate steps.

---

## Distribution of the Reverse Process

**Theorem 2.5:** The distribution $g(x_{t-1}|x_t, x_0)$ takes the form:

$$g(x_{t-1}|x_t, x_0; \phi) = \mathcal{N}(x_{t-1}; \mu_g(x_t, x_0), \Sigma_g(t))$$

where:

$$\mu_g(x_t, x_0) = \frac{(1-\bar{\alpha}_{t-1})\sqrt{\alpha_t}}{1-\bar{\alpha}_t} x_t + \frac{(1-\alpha_t)\sqrt{\bar{\alpha}_{t-1}}}{1-\bar{\alpha}_t} x_0$$

$$\Sigma_g(t) = \frac{(1-\alpha_t)(1-\bar{\alpha}_{t-1})}{1-\bar{\alpha}_t} I \stackrel{\text{def}}{=} \sigma_g^2(t) I$$

### Proof Sketch

Using Bayes' rule:
$$g(x_{t-1}|x_t, x_0) = \frac{g(x_t|x_{t-1}, x_0) \cdot g(x_{t-1}|x_0)}{g(x_t|x_0)}$$

All three terms on the right are Gaussian:
- $g(x_t|x_{t-1}, x_0) = g(x_t|x_{t-1}) = \mathcal{N}(x_t; \sqrt{\alpha_t} \, x_{t-1}, (1-\alpha_t)I)$
- $g(x_{t-1}|x_0) = \mathcal{N}(x_{t-1}; \sqrt{\bar{\alpha}_{t-1}} \, x_0, (1-\bar{\alpha}_{t-1})I)$
- $g(x_t|x_0) = \mathcal{N}(x_t; \sqrt{\bar{\alpha}_t} \, x_0, (1-\bar{\alpha}_t)I)$

The product of Gaussians (in the numerator) divided by a Gaussian gives another Gaussian. Working through the algebra yields the result.

---

## Training Objective

The denoising matching term is:

$$\sum_{t=2}^{T} \mathbb{E}_{g(x_t|x_0)} \left[ D_{KL}(g(x_{t-1}|x_t, x_0) \| p_\theta(x_{t-1}|x_t)) \right]$$

Both distributions are Gaussian:
- $g(x_{t-1}|x_t, x_0) = \mathcal{N}(\mu_g(x_t, x_0), \sigma_g^2(t)I)$ — known, fixed
- $p_\theta(x_{t-1}|x_t) = \mathcal{N}(\mu_\theta(x_t, t), \sigma_\theta^2(t)I)$ — learned

The KL divergence between two Gaussians with the same (fixed) variance reduces to the squared difference of means:

$$D_{KL} \propto \|\mu_g(x_t, x_0) - \mu_\theta(x_t, t)\|^2$$

### Parameterization Options

We can parameterize $\mu_\theta$ in different ways:

1. **Predict $x_0$ directly:** Learn $\hat{x}_\theta(x_t, t) \approx x_0$, then compute $\mu_\theta$ from it.

2. **Predict the noise $\epsilon$:** Since $x_t = \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1-\bar{\alpha}_t} \epsilon$, we have:
   $$x_0 = \frac{x_t - \sqrt{1-\bar{\alpha}_t} \epsilon}{\sqrt{\bar{\alpha}_t}}$$
   
   Learn $\epsilon_\theta(x_t, t) \approx \epsilon$, then derive $\mu_\theta$.

The **noise prediction** parameterization (option 2) is what's used in DDPMs and tends to work better empirically.

---

## Summary: From VAE to DDPM

| Aspect | VAE | DDPM |
|--------|-----|------|
| Latent dimension | Typically lower | Same as data |
| Encoder | Learned $g_\phi(z\|x)$ | Fixed $g(x_t\|x_{t-1})$ |
| Number of latents | 1 | $T$ (many) |
| Training signal | Reconstruction + KL | Denoising matching |
| Generation | Sample $z$, decode | Iterative denoising |

---

## To-Do / Questions from Notes

- [ ] What do mixture models have to do with the transition distribution?
- [ ] Why is $\mathbb{E}[\epsilon_{t-2} \epsilon_{t-2}^T] = I$? (Prove this)
- [ ] Figure out intuition for the ELBO derivation index manipulations
- [ ] Derive KL divergence between two multivariate Gaussians from scratch
- [ ] What does "variance preserving" mean more precisely?

---

## Architecture Notes

Common components for the denoising network:
- **Activation functions:** Softmax, GELU, ReLU, Tanh, Sigmoid
- **Architectures:** Transformer, ConvNet, LSTM, RNN
- Positional encodings, VIT-style patches, Alphabets (?)

---

## Further Reading

- Ho et al. (2020): Denoising Diffusion Probabilistic Models
- Chan (notes referenced in handwritten notes)
- Sohl-Dickstein et al. (2015): Deep Unsupervised Learning using Nonequilibrium Thermodynamics
