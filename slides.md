---
theme: seriph
layout: intro
colorSchema: light
---

# Status update

## Robust counterfactual explanations

<br>

## February 2026

### Marcin Kostrzewa



<img src="./media/logo.svg" width="300" height="250"
     style="position: absolute; bottom: 60px; right: 50px;">

---

# Motivation

<div style="display: flex; align-items: center; gap: 2em;">

<div style="flex: 1;">

Classifiers change over time:
- Retrained on new data
- Hyperparameters tuned
- Architecture modifications

<br/>

A counterfactual $x'$ valid for model $M$ may become **invalid** after $M$ is updated.

<br/>

**Goal:** Generate counterfactuals that remain valid across plausible model changes.

</div>

<div style="flex: 1.3;">
<img src="./media/robust_cfe_motivation.svg" style="width: 100%;">
</div>

</div>

<SlideNumber/>

---

# Robust CFE &mdash; Problem Setup

<br>

<div>

**Setting:** Dataset $X = \{x_i\}_{i=1}^{N}$ with classes $\mathcal{C} = \{1,\ldots,C\}$

<br/>

**Space of admissible models** $\mathcal{M}$ (following Stępka et al., 2025):

> The probabilistic distribution of all models resulting from complete retraining of $M$ using arbitrary settings from a predefined set of model changes.

<br/>

We sample $K$ models $M_1,\ldots,M_K$ from $\mathcal{M}$.

</div>

<SlideNumber/>

---

# Generating Model Variants

<div>

How to sample from $\mathcal{M}$?

<br/>

**Bootstrap resampling:**
- Create bootstrap samples of the training data
- Retrain the model on each bootstrap dataset

**Weight perturbation:**
- Add noise to model weights (linear models, neural networks)

**Architecture changes (neural networks):**
- Add or remove layers
- Add or remove neurons within layers

</div>

<SlideNumber/>

---

# Consensus Vector

<div>

For each point $x$, define $\mathbf{p}(x) = (p_1,\ldots,p_C) \in [0,1]^C$, where:

$$p_i = \mathbb{E}_{M \sim \mathcal{M}}[M(x)]_i$$

is the average probability assigned to class $i$ across models.

<br/>

In practice, approximated with $K$ sampled models:

$$p_i \approx \frac{1}{K}\sum_{k=1}^{K} [M_k(x)]_i$$


</div>

<SlideNumber/>

---

# Conditional Normalizing Flow

<div>

Learn $p_\theta(x \mid \mathbf{c})$ where $\mathbf{c} \in [0,1]^C$ is the consensus vector.

**Training:** Pairs $\{(x_i, \mathbf{p}(x_i))\}_{i=1}^N$.

<br/>

**Counterfactual generation** &mdash; for target class $c'$:

$$x' = \arg\min_x \; d(x_0, x) + \lambda \cdot \max\left(0, \; \tau - \log p_\theta(x' \mid \mathbf{e}_{c'})\right)$$

where $\mathbf{e}_{c'} \in \{0,1\}^C$ is the one-hot vector for $c'$ and $\tau$ is the **median** of $\log p_\theta(x)$ over the training data.

</div>

<SlideNumber/>

---

# Problem?

<div>

Conditioning on $\mathbf{e}_{c'}$ means we require **all classifiers to agree** on class $c'$ with probability 1.

<br/>

The flow pushes the counterfactual toward regions where $\mathbf{p}(x) \approx \mathbf{e}_{c'}$ &mdash; perfect consensus.

<br/>

These regions may be **far from the original instance** $x_0$, leading to a **high proximity cost** $d(x_0, x')$.

<br/>

**Can we relax the consensus requirement?**

</div>

<SlideNumber/>

---

# Relaxed Consensus Conditioning

<div>

Same flow, same training, but different inference.

Instead of conditioning on $\mathbf{e}_{c'}$, sample consensus vectors from:

$$\mathcal{C}_{c'} = \{\mathbf{p} : p_{c'} \geq \gamma\}, \quad \text{e.g. } \gamma = 0.9$$

<br/>

**Theoretical objective:**

$$x' = \arg\min_x \; d(x_0, x) + \lambda \cdot \max\left(0, \; \tau - \mathbb{E}_{\mathbf{c} \sim \mathcal{C}_{c'}} \left[\log p_\theta(x' \mid \mathbf{c})\right]\right)$$

**Approximation** with $L$ sampled vectors $\mathbf{c}_1, \ldots, \mathbf{c}_L \in \mathcal{C}_{c'}$:

$$x' = \arg\min_x \; d(x_0, x) + \lambda \cdot \max\left(0, \; \tau - \frac{1}{L}\sum_{l=1}^{L} \log p_\theta(x' \mid \mathbf{c}_l)\right)$$

</div>

<SlideNumber/>

---

# Key Properties

<div>

**Classifier-agnostic:**
- Does **not** require gradients from the classifier (as PPCEF for instance)
- Classifiers are used only to label the dataset

<br/>

**Plausibility:**
- The flow directly models $p(x \mid c')$ &ndash; the distribution of class $c'$
- Counterfactuals are pushed toward high-density regions of the target class

<br/>

**Robustness:**
- By training on labels from multiple model variants, the flow captures the **consensus** decision surface
- Counterfactuals are valid across the space of admissible models $\mathcal{M}$

</div>

<SlideNumber/>

---

# Other news

<br> 
<br> 
<br> 

<div style="font-size: 1.4em; text-align: center;">

*Towards plausibility in time series counterfactual explanations*

got accepted at **ACIIDS 2026**

</div>

<SlideNumber/>

---


<div style="display: flex; justify-content: center; align-items: center; height: 40vh;">
  <h1 style="font-size: 5em;">Thanks!</h1>
</div>

<SlideNumber/>
