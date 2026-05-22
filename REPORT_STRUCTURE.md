# Report Structure — Hybrid Modeling
> This is a living outline. Adjust section depth and emphasis as the draft develops.
> Cross-reference with `SEMINAR_AI_GUIDELINES.md` for all formatting and style rules.

---

## Sections at a Glance

| # | Title | Core Concepts |
|---|---|---|
| 1 | Why Hybrid Modeling? | Discriminative vs. generative, OOD failure, joint distribution |
| 2 | Foundational Building Blocks | Normalizing Flows, Change of Variables, GLM head |
| 3 | Training Strategies | Naive vs. shared parameterization, scale mismatch, weighted objective |
| 4 | Case Study: HybridIDF | Discrete flows, Jacobian = 1, integer coupling, STE |
| 5 | Applications and Robustness | OOD detection, semi-supervised learning |

---

## Section Details

### 1. Why Hybrid Modeling?

**Goal:** Motivate the hybrid approach from first principles before introducing any architecture.

- Contrast the **discriminative** approach $p(y \mid x)$ — learns decision boundaries, ignores the structure of the input space — with the **generative** approach $p(x)$ — models where data actually lives.
- Explain the core failure mode: classifiers are often **"certain but wrong"** on out-of-distribution (OOD) inputs because they have no mechanism to detect unfamiliar data.
- Introduce the **hybrid solution**: model the joint distribution $p(x, y) = p(y \mid x)\, p(x)$, which enables both classification and uncertainty quantification.
- Briefly preview rejection rules as a practical downstream benefit.

---

### 2. Foundational Building Blocks

**Goal:** Give the reader the mathematical tools needed to understand everything that follows.

#### 2.1 Normalizing Flows
- Define as bijective, high-capacity transformations $f: x \mapsto z$ with tractable inverse.
- Explain the **Change of Variables formula**: how to evaluate the exact likelihood $p(x)$ by transforming to a simple base distribution $\pi(z)$.
- Detail the role of the **Jacobian determinant** $\left|\det \frac{\partial f}{\partial x}\right|$ in accounting for volume change under the transformation.

#### 2.2 Generalized Linear Model (GLM) as Predictive Head
- Introduce the GLM as a lightweight classifier sitting on top of the learned latent representation $z = f^{-1}(x)$.
- Emphasize that the same backbone $f$ serves both the generative and discriminative objectives.

---

### 3. Training Strategies

**Goal:** Show how and why shared parameterization is chosen over naive composition, and introduce the objective that balances both tasks.

#### 3.1 Naive Approach vs. Shared Parameterization
- **Naive:** train a generative model and a classifier independently, combine at inference. Simple but wasteful — no shared representation.
- **Shared:** a single invertible backbone $f(x)$ feeds both the density estimator and the GLM head. More parameter-efficient and encourages complementary learning.

#### 3.2 The Scale Mismatch Problem
- The generative term $\ln p(x)$ operates over the full input dimensionality (e.g., thousands of pixels), while the discriminative term $\ln p(y \mid x)$ contributes only 1 bit per sample.
- Without correction, the generative signal dominates and the classifier degrades.
- Note: $\lambda$ can also be interpreted as a **Jacobian-based regularizer** that encourages robustness to input perturbations.

#### 3.3 Weighted Objective
- Present the combined loss:
$$\ell(x, y;\, \lambda) = \ln p(y \mid x) + \lambda \ln p(x)$$
- Justify $\lambda$ as a balancing hyperparameter that controls the relative weight of the two objectives.
- Discuss how the choice of $\lambda$ affects the bias toward classification accuracy vs. generative fidelity.

---

### 4. Case Study: Hybrid Integer Discrete Flows (HybridIDF)

**Goal:** Concretely ground the theory in a model designed for discrete data such as 8-bit images.

> **Lecture script note:** Explicitly point out the contrast — discrete flows *simplify* the math (Jacobian = 1) but introduce a new engineering hurdle: the rounding operation has zero gradient almost everywhere, necessitating the Straight-Through Estimator.

> **No code.** The book chapter includes an implementation example; the report covers only the conceptual and mathematical content.

#### 4.1 Discrete vs. Continuous Density
- In discrete space, the transformation $f^{-1}$ involves no volume change, so the **Jacobian determinant equals 1**.
- Likelihood simplifies to: $p(x) = \pi(z = f^{-1}(x))$
- Contrast with the continuous case to make this simplification explicit and meaningful.

#### 4.2 Integer Discrete Coupling Layers
- Describe the **bipartite coupling layer** structure.
- Explain how rounding operations are used to maintain integer-valued representations throughout the flow.

> **Visual suggestion:** Include a diagram of the bipartite coupling layer, highlighting where the rounding operation occurs relative to the neural network transformation $s, t$.

#### 4.3 Straight-Through Estimator (STE)
- Rounding is non-differentiable: its gradient is zero almost everywhere, which breaks standard backpropagation.
- Explain the STE: treat rounding as the identity function during the backward pass, allowing gradients to flow through.
- Briefly note the theoretical justification and practical implications for training stability.

---

### 5. Applications and Robustness

**Goal:** Show what the hybrid model actually achieves in practice, grounding the theory in concrete results.

#### 5.1 Out-of-Distribution Detection
- The generative component $p(x)$ assigns lower likelihood to unfamiliar inputs.
- Example: a model trained on MNIST assigns noticeably lower $p(x)$ to NotMNIST digits, enabling reliable OOD detection without a separate detector.
- Connect back to Section 1: this is precisely the failure mode that pure discriminative models cannot address.

#### 5.2 Semi-Supervised Learning
- Unlabeled data can be used to train the generative component $p(x)$, improving the learned representation even when labels are scarce.
- The shared backbone benefits: a better-understood data manifold leads to smoother, more robust decision boundaries in the classifier.

---

## Open Questions / Placeholders

- [ ] Confirm exact notation used in the book (Chapter 6) and align all symbols accordingly.
- [ ] Check which results (OOD, semi-supervised) are empirically demonstrated in the papers vs. the book.
- [ ] Decide whether a short **Conclusion** section is needed or if Section 5 closes the report naturally.
- [ ] Add figure references once diagrams are created in LaTeX.