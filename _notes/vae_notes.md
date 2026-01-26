# Variational Autoencoders (VAEs)

*Working notes on the mathematical foundations of VAEs*

---

## Problem Setup

**Given:** Samples $x$ from a distribution of interest

**Goal:** Model the true distribution $p(x)$, then generate new samples from our approximate distribution

**Related approaches to explore:** Autoregressive models, normalizing flows, variational autoencoders, energy-based models, score-based generative models

---

## The Evidence Lower Bound (ELBO)

### Why We Need It

Let $p(x, z)$ be the joint distribution of data and latent variables.

In a likelihood-based approach, we'd want to learn a model to maximize $p(x)$:

$$p(x) = \int_z p(x, z) \, dz \quad \text{or} \quad p(x) = \frac{p(x, z)}{p(z|x)}$$

**The difficulties:**
- The integral over $z$ is difficult to compute
- We need access to the ground truth latent encoder $p(z|x)$

### Deriving the ELBO

**Approach:** Derive the Evidence Lower Bound (ELBO)

Introduce $g_\phi(z|x)$: an approximate variational distribution with parameters $\phi$ that we seek to optimize. This model is meant to learn the true distribution over latent variables for given observations $x$.

Starting from the log-likelihood:

$$\log p(x) = \log \int_z p(x, z) \, dz$$

Multiply and divide by the variational distribution:

$$= \log \int_z \frac{p(x, z) \cdot g_\phi(z|x)}{g_\phi(z|x)} \, dz$$

$$= \log \mathbb{E}_{g_\phi(z|x)} \left[ \frac{p(x, z)}{g_\phi(z|x)} \right]$$

Apply **Jensen's inequality** (since log is concave):

$$\geq \mathbb{E}_{g_\phi(z|x)} \left[ \log \frac{p(x, z)}{g_\phi(z|x)} \right]$$

This is the **ELBO** — a lower bound on the log-probability (the "evidence").

### Decomposing the ELBO

We can show that:

$$\log p(x) = \mathbb{E}_{g_\phi(z|x)} \left[ \log \frac{p(x, z)}{g_\phi(z|x)} \right] + D_{KL}(g_\phi(z|x) \| p(z|x))$$

Since KL divergence is always $\geq 0$, we have:

$$\log p(x) \geq \mathbb{E}_{g_\phi(z|x)} \left[ \log \frac{p(x, z)}{g_\phi(z|x)} \right] = \text{ELBO}$$

The KL term represents the difference between the true encoder and the learned encoder. It's non-negative, so we drop it from the objective.

**Key insight:** We want to optimize the parameters of the variational posterior $g_\phi(z|x)$ to match the true posterior $p(z|x)$. We could do this by minimizing their KL divergence, but we don't have access to the ground truth posterior $p(z|x)$.

**Alternative:** Maximize the ELBO. Since $\log p(x) = \text{ELBO} + D_{KL}$, and $\log p(x)$ is a constant, minimizing $D_{KL}$ is equivalent to maximizing the ELBO.

---

## VAE Architecture

```
        Encoder                     Decoder
   ┌─────────────┐              ┌─────────────┐
   │             │    μ(x)      │             │
x ─┤    E_φ     ├──────┬───z───┤    D_θ     ├── x̂
   │             │      │       │             │
   └─────────────┘   log σ²(x)  └─────────────┘
                        │
                   z = μ_φ(x) + σ(x) ⊙ ε̂
                   
                   where ε̂ ~ N(0, I)
```

**Encoder:** $g_\phi(z|x)$ maps observations to latents

**Decoder:** $p_\theta(x|z)$ maps latents to observations

### Expanding the ELBO for VAEs

$$\mathbb{E}_{g_\phi(z|x)} \left[ \log \frac{p(x, z)}{g_\phi(z|x)} \right] = \mathbb{E}_{g_\phi(z|x)} \left[ \log \frac{p(z) \cdot p_\theta(x|z)}{g_\phi(z|x)} \right]$$

$$= \mathbb{E}_{g_\phi(z|x)} \left[ \log p_\theta(x|z) \right] + \mathbb{E}_{g_\phi(z|x)} \left[ \log \frac{p(z)}{g_\phi(z|x)} \right]$$

$$= \mathbb{E}_{g_\phi(z|x)} \left[ \log p_\theta(x|z) \right] - D_{KL}(g_\phi(z|x) \| p(z))$$

This gives us:

$$\text{ELBO} = \underbrace{\mathbb{E}_{g_\phi(z|x)} \left[ \log p_\theta(x|z) \right]}_{\text{Reconstruction term}} - \underbrace{D_{KL}(g_\phi(z|x) \| p(z))}_{\text{Prior matching term}}$$

- **Reconstruction term:** How well does the decoder reconstruct $x$ from samples of $z$?
- **Prior matching term:** How well does the learned encoder match the latent prior?

---

## Evaluating the VAE Objective

### Assumptions

- $x \in \mathbb{R}^{D_x}$, $z \in \mathbb{R}^{D_z}$
- Encoder: $\mathcal{E}_\phi : \mathbb{R}^{D_x} \to \mathbb{R}^{2D_z}$ (outputs mean and variance)
- Decoder: $\mathcal{D}_\theta : \mathbb{R}^{D_z} \to \mathbb{R}^{D_x}$
- Prior: $p(z) \sim \mathcal{N}(z; 0, I)$ — standard multivariate Gaussian
- Encoder distribution: $g_\phi(z|x) \sim \mathcal{N}(z; \mu_\phi(x), \Sigma_\phi(x))$ — multivariate Gaussian with diagonal covariance
- Decoder distribution: $p_\theta(x|z) \sim \mathcal{N}(x; \mu_\theta(z), \sigma^2 I)$, where $\sigma^2 = 1$

### The Objective

$$\arg\max_{\phi, \theta} \mathbb{E}_{g_\phi(z|x)} \left[ \log p_\theta(x|z) \right] - D_{KL}(g_\phi(z|x) \| p(z))$$

Using Monte Carlo estimation with $L$ samples:

$$\approx \arg\max_{\phi, \theta} \sum_{\ell=1}^{L} \log p_\theta(x|z^{(\ell)}) - D_{KL}(g_\phi(z|x) \| p(z))$$

The KL term can be computed exactly for two multivariate Gaussians.

Latents $\{z^{(\ell)}\}_{\ell=1}^{L}$ are sampled from $g_\phi(z|x)$.

### The Reparameterization Trick

**Problem:** Each latent $z^{(\ell)}$ is sampled, which is non-differentiable.

**Solution:** Use the reparameterization trick.

If $x \sim \mathcal{N}(x; \mu, \sigma^2)$, we can write:
$$x = \mu + \sigma \epsilon, \quad \epsilon \sim \mathcal{N}(\epsilon; 0, 1)$$

In the VAE:
$$z = \mu_\phi(x) + \sigma_\phi(x) \odot \hat{\epsilon}, \quad \hat{\epsilon} \sim \mathcal{N}(\hat{\epsilon}; 0, I)$$

Now gradients can be computed with respect to $\phi$!

**After training:** Generate new data by sampling directly from the latent prior $p(z)$ and running it through the decoder.

---

## The Reconstruction Loss (Detailed Derivation)

$$\mathbb{E}_{g_\phi(z|x)} \left[ \log p_\theta(x|z) \right] = \int_z \log p_\theta(x|z) \cdot g_\phi(z|x) \, dz$$

This maps each $z$ to the log-probability of the fixed $x$.

For Gaussian decoder:

$$\log p_\theta(x|z) = \log \mathcal{N}(x; \mu_\theta(z), \sigma^2 I)$$

$$= \log \left( \frac{1}{(2\pi)^{D/2} |\sigma^2 I|^{1/2}} \exp\left[ -\frac{1}{2} (x - \mu_\theta(z))^T (\sigma^2 I)^{-1} (x - \mu_\theta(z)) \right] \right)$$

$$= \log \left( \frac{1}{(2\pi)^{D/2} |\sigma^2 I|^{1/2}} \right) - \frac{1}{2\sigma^2} \|x - \mu_\theta(z)\|_2^2$$

Setting $\sigma^2 = 1$:

$$= -\frac{D}{2} \log(2\pi) - \frac{1}{2} \|x - \mu_\theta(z)\|_2^2$$

Therefore:

$$\mathbb{E}_{z \sim g_\phi(z|x)} \left[ \log p_\theta(x|z) \right] \approx -\frac{D}{2} \log(2\pi\sigma^2) - \frac{1}{2} \|x - \mu_\theta(\mu_\phi(x) + \sigma_\phi(x) \cdot \epsilon)\|_2^2$$

where $\epsilon \sim \mathcal{N}(0, I)$.

---

## The Prior Matching Loss (Detailed Derivation)

### KL Divergence Background

For discrete distributions:
$$KL(p \| g) = \sum_j p(y) \log \frac{p(y)}{g(y)} = -H(p) + H(p, g)$$

where $H(p)$ is entropy and $H(p, g)$ is cross-entropy.

For continuous distributions:
$$KL(g \| p) = \int g(z) \log \frac{g(z)}{p(z)} \, dz = \text{Cross-entropy}(g, p) - \text{Entropy}(g)$$

### KL Divergence for Univariate Gaussians

For $p_1(x) = \mathcal{N}(x; \mu_1, \sigma_1^2)$ and $p_2(x) = \mathcal{N}(x; \mu_2, \sigma_2^2)$:

$$KL(p_1(x) \| p_2(x)) = \frac{1}{2} \left[ \log \frac{\sigma_2^2}{\sigma_1^2} - 1 + \frac{\sigma_1^2 + (\mu_1 - \mu_2)^2}{\sigma_2^2} \right]$$

If $p_2(x) = \mathcal{N}(x; 0, 1)$:

$$KL(p_1(x) \| p_2(x)) = -\frac{1}{2} \left[ \log(\sigma_1^2) + 1 - \mu_1^2 - \sigma_1^2 \right]$$

### KL Divergence for Multivariate Gaussians

$$D_{KL}(p(x|\mu_1, \Sigma_1) \| g(x|\mu_2, \Sigma_2))$$

$$= \frac{1}{2} \left[ \log \frac{|\Sigma_2|}{|\Sigma_1|} - D + (\mu_1 - \mu_2)^T \Sigma_2^{-1} (\mu_1 - \mu_2) + \text{tr}(\Sigma_2^{-1} \Sigma_1) \right]$$

### For Diagonal Covariance (VAE Case)

When both distributions have diagonal covariance, the KL divergence decomposes:

$$D_{KL}(g(z) \| p(z)) = \sum_{j=1}^{D_z} D_{KL}(g_j(z_j) \| p_j(z_j))$$

For the VAE prior matching term:

$$D_{KL}(g(z|x; \phi) \| p(z)) = \frac{1}{2} \sum_{j=1}^{D_z} \left[ \mu_j^2(x; \phi) + \sigma_j^2(x; \phi) - \log(\sigma_j^2(x; \phi)) - 1 \right]$$

---

## Final VAE Loss

$$\mathcal{L}_{\text{VAE}} = \arg\min_{\phi, \theta} \beta \cdot D_{KL}(g_\phi(z|x) \| p(z)) - \sum_{\ell=1}^{L} \log p_\theta(x|z^{(\ell)})$$

Expanding:

$$= \frac{1}{2} \sum_{j=1}^{D_z} \left[ \mu_j^2(x; \phi) + \sigma_j^2(x; \phi) - \log(\sigma_j^2(x; \phi)) - 1 \right] + \frac{1}{2} \left[ D_x \log(2\pi) + \|x - \mu_\theta(z^{(\ell)}; \theta)\|_2^2 \right]$$

**Note:** The $\beta$ term (when $\beta > 1$) gives the $\beta$-VAE, which encourages more disentangled representations.

---

## Implementation Notes

In code, the encoder outputs:
- `mu`: $\mu_\phi(x)$
- `logvar`: $\log \sigma^2(x; \phi)$

Then:
$$\sigma(x) = \exp(0.5 \cdot \text{logvar})$$

And the reconstruction:
$$\hat{x} = \mathcal{D}_\theta(\mu_\phi(x) + \sigma(x) \odot \hat{\epsilon})$$

---

## Bernoulli Decoder Variant

For binary data (e.g., MNIST), assume each pixel $x_i$ represents the expected value of a binary random variable $x_i' \in \{0, 1\}$.

Each pixel is generated by a Bernoulli process:
- $x_i' \sim \text{Bernoulli}(x_i)$ (latent variable)
- $x_i = \mathbb{E}_{x_i' \sim \text{Ber}(x_i)}[x_i']$ (observation)

The log-likelihood becomes:
$$\log p_\theta(x|z) = \sum_{i=1}^{D} x_i \log \hat{x}_i + (1 - x_i) \log(1 - \hat{x}_i)$$

This is the binary cross-entropy loss.

---

## Conditional VAE

The same architecture, but the encoder and decoder are conditioned on some additional information (e.g., class labels).

---

## Further Reading

- Kingma & Welling (2014): Auto-Encoding Variational Bayes
- Murphy, PML-1: Chapter 6.2.1 (KL divergence), pages 105, 174, 205, 308
- Matrix Cookbook: Section 8.2.2 (equation 380)
