# Report Structure — Hybrid Modeling
> Living outline for the seminar report. Cross-reference with `SEMINAR_AI_GUIDELINES.md` for formal requirements, writing rules, and source hierarchy.

---

## Core Framing

The report should explain **hybrid modeling as a way to combine reliable prediction with a model of the input distribution**. The main narrative is:

1. Purely discriminative models can be confident on unfamiliar inputs because they model \(p(y \mid x)\) but not \(p(x)\).
2. Hybrid models target the joint distribution \(p(x,y)=p(y \mid x)p(x)\), so they can support prediction and uncertainty-aware behavior.
3. Normalizing flows are a natural backbone because they provide invertible features and exact likelihoods.
4. The shared objective creates a scale mismatch between the discriminative and generative terms, so the weighted objective is central.
5. HybridIDF illustrates the idea for discrete data, where integer-valued invertible transformations avoid continuous dequantization issues.

---

## Sections at a Glance

| # | Required Section | Working Title | Core Concepts | Main Sources |
|---|---|---|---|---|
| 1 | Introduction | Why Hybrid Modeling? | Motivation, OOD confidence failure, joint distribution | Book Ch. 6; Nalisnick et al. |
| 2 | Background | Generative Modeling and Invertible Features | \(p(y \mid x)\), \(p(x)\), normalizing flows, change of variables, predictive head | Book Ch. 1 and Ch. 6; Nalisnick et al. |
| 3 | Main Content / Results | Hybrid Models with Shared Invertible Representations | Naive vs. shared parameterization, scale mismatch, weighted objective, HybridIDF | Book Ch. 6; Nalisnick et al.; Hoogeboom et al. |
| 4 | Discussion | Applications, Robustness, and Limitations | OOD detection, semi-supervised learning, role of \(\lambda\), limits of likelihood-based confidence | Book Ch. 6; Nalisnick et al. |
| 5 | Conclusion | What Hybrid Modeling Contributes | Takeaways and significance | Synthesized from all sources |
| 6 | References | References | Verified bibliography | All cited sources |

---

## Section Details

### 1. Introduction — Why Hybrid Modeling?

**Goal:** Motivate the problem before introducing architecture or notation.

- Start from the common discriminative setup \(p(y \mid x)\): it learns decision behavior but does not model whether \(x\) is plausible under the training distribution.
- Explain the central failure mode: a classifier may assign high confidence to an out-of-distribution input because the conditional model still has to choose a class.
- Introduce the hybrid objective through the factorization:
  \[
  p(x,y)=p(y \mid x)p(x).
  \]
- State the report's guiding question: how can one share representation learning between a predictive model and a generative model without losing the benefits of either?
- Preview the answer: use an invertible backbone that supports exact likelihood evaluation and feeds a predictive head.

**Keep brief:** This section should orient the reader, not teach normalizing flows yet.

---

### 2. Background — Generative Modeling and Invertible Features

**Goal:** Give the reader the mathematical tools needed for the main section.

#### 2.1 Discriminative, Generative, and Hybrid Models

- Define the discriminative component \(p(y \mid x)\) and the generative component \(p(x)\).
- Explain why \(p(x,y)=p(y \mid x)p(x)\) is useful for classification with uncertainty, while \(p(x,y)=p(x \mid y)p(y)\) is more natural when class-conditional generation is the primary goal.
- Mention that the report follows the book's preference for the \(p(y \mid x)p(x)\) factorization because the focus is reliable decision-making rather than class-conditional sampling.

#### 2.2 Normalizing Flows and Change of Variables

- Define a normalizing flow as an invertible transformation between data \(x\) and latent variable \(z\).
- Use one notation convention consistently. The book often writes \(z=f^{-1}(x)\); with this convention:
  \[
  p_X(x)=p_Z(f^{-1}(x))\left|\det J_{f^{-1}}(x)\right|.
  \]
  Equivalently, if \(x=f(z)\), then:
  \[
  p_X(x)=p_Z(z)\left|\det J_f(z)\right|^{-1}.
  \]
- Explain the role of the Jacobian determinant as a volume-correction term in continuous spaces.
- Keep the explanation didactic: the reader should understand why flows provide exact likelihoods and why invertibility matters.

#### 2.3 Predictive Head on Invertible Features

- Introduce the predictive head as a classifier or regressor operating on the learned representation \(z\).
- Explain that Nalisnick et al. frame this component as a generalized linear model on invertible features, while the book's implementation example uses a small neural classification head.
- Emphasize the common idea: the same invertible backbone supports \(p(x)\) and \(p(y \mid x)\).

---

### 3. Main Content / Results — Hybrid Models with Shared Invertible Representations

**Goal:** Present the core technical material in order of importance.

#### 3.1 Naive Composition vs. Shared Parameterization

- **Naive approach:** train \(p(y \mid x)\) and \(p(x)\) independently, then combine their scores at inference time.
- Explain why this approach is simple but inefficient: it uses separate parameterizations, gives no representation sharing, and creates no training-time information flow between labels and density modeling.
- **Shared approach:** use a common feature extractor/backbone, with separate task-specific heads where needed.
- Show that shared parameters receive gradient information from both the discriminative and generative terms.

**Possible figure:** A two-panel diagram comparing separate networks with a shared invertible backbone.

#### 3.2 The Scale Mismatch Problem

- Explain the dimensionality imbalance: the log-likelihood term for \(x\) often aggregates over many dimensions, while the label term may contribute only a small amount of information per example.
- Use the book's binary example: one binary label contributes roughly \(-\log 2\), while a \(D\)-dimensional binary input contributes roughly \(-D\log 2\) under uniform Bernoulli predictions.
- State the consequence: without correction, the generative signal can dominate the shared representation and hurt classification.

#### 3.3 Weighted Hybrid Objective

- Present the objective to **maximize**:
  \[
  \mathcal{J}_\lambda(x,y)=\log p(y \mid x)+\lambda \log p(x),\quad \lambda \geq 0.
  \]
- If discussing implementation or optimization as a loss, use the negative objective:
  \[
  \mathcal{L}_\lambda(x,y)=-\log p(y \mid x)-\lambda \log p(x).
  \]
- Explain \(\lambda\) as a balancing hyperparameter between discriminative accuracy and generative fidelity.
- Include Nalisnick et al.'s interpretation of \(\lambda\) as related to robustness and Jacobian-based regularization, but flag that it is not derived as a normalized probabilistic model.

#### 3.4 Case Study: Hybrid Integer Discrete Flows

**Goal:** Ground the abstract objective in a concrete model for ordinal discrete data such as 8-bit images.

- Explain why continuous flows are awkward for lossless discrete data: dequantization or quantization can introduce a mismatch between the model and the stored data.
- Introduce Integer Discrete Flows as bijective maps on integer-valued data.
- For a discrete bijection, avoid the continuous volume-correction term:
  \[
  p_X(x)=p_Z(f^{-1}(x)).
  \]
- State that IDFs usually place a **discretized logistic distribution** or a discretized logistic mixture on the latent integer variables. This prior fits ordinal data because nearby integer values remain meaningfully related.
- Explain integer discrete coupling layers:
  - split the input into parts,
  - copy one part,
  - transform the other part with an integer-valued additive update,
  - use rounding to preserve integer-valued representations.
- Explain the Straight-Through Estimator:
  - rounding is non-differentiable in the usual gradient sense,
  - the backward pass treats rounding approximately like the identity,
  - this approximation enables gradient-based training but introduces bias.
- Make clear that the report should **not** include code from the book. It should explain the concepts and equations only.
- Use Hoogeboom et al.'s results only as supporting evidence, not as the main empirical story: Table 3 reports competitive generative modeling in bits per dimension; Tables 1 and 2 report lossless-compression performance.

**Possible figure:** Bipartite integer coupling layer showing split, neural transformation, rounding, and additive update.

---

### 4. Discussion — Applications, Robustness, and Limitations

**Goal:** Interpret what the hybrid model buys us, and where caution is needed.

#### 4.1 Out-of-Distribution Detection

- Explain the intended mechanism: \(p(x)\) can provide an input plausibility score before or alongside the predictive distribution.
- Use Nalisnick et al.'s experiments as the main empirical support for OOD behavior:
  - Table 1 and Figure 4 compare MNIST with NotMNIST as the OOD set.
  - Table 2 and Figure 5 compare SVHN with CIFAR-10 as the OOD set.
- Avoid a universal claim that likelihood always solves OOD detection. Phrase the point as: likelihood gives a useful signal that must be validated empirically for the data and model family.

#### 4.2 Semi-Supervised Learning

- Explain why hybrid modeling naturally supports semi-supervised learning:
  - labeled data trains both \(p(y \mid x)\) and \(p(x)\),
  - unlabeled data can still train \(p(x)\),
  - the shared backbone can improve the representation available to the classifier.
- Tie this point to the book's "What's Next?" section and to Nalisnick et al.'s Table 3, where adding unlabeled MNIST data improved the semi-supervised classifier relative to using 1000 labels alone.

#### 4.3 Limitations and Open Questions

- The balancing factor \(\lambda\) requires tuning and can strongly shift behavior between predictive and generative performance.
- The weighted objective is useful but not a fully normalized joint probability model when \(\lambda \neq 1\).
- Exact likelihood is attractive, but likelihood alone is not the same as semantic understanding or calibrated uncertainty.
- IDFs solve a discrete-data modeling issue, but the straight-through estimator creates biased gradients.

---

### 5. Conclusion — What Hybrid Modeling Contributes

**Goal:** Add higher-level analysis rather than restating the previous sections.

Required takeaways:

- Hybrid modeling reframes prediction as part of joint probabilistic modeling.
- Invertible features make this practical because they support both exact density evaluation and predictive heads.
- The weighted objective is the technical hinge: it enables useful training but introduces a hyperparameter and a probabilistic compromise.
- HybridIDF shows how the framework can be adapted to discrete data, but it also reveals the engineering trade-offs of integer constraints and STE-based training.
- The broader lesson is that robust decision-making needs both predictive accuracy and a representation of where the data distribution lies.

---

### 6. References

**Goal:** Provide verified citations only.

- Use the LNCS/BibTeX style from the template (`splncs04`).
- Build the bibliography from the book chapter and the two supplied papers first.
- Add outside references only if a claim cannot be supported from the supplied sources, and mark uncertain entries with `[VERIFY]` until checked.

---

## Source-to-Section Map

| Source | Use for |
|---|---|
| `resources/hybrid_modeling.pdf`, Chapter 6 | Main story, naive/shared parameterization, scale mismatch, weighted objective, HybridIDF formulation, semi-supervised outlook. |
| `resources/nalisnick19b.pdf` | Deep invertible features, exact joint density, GLM framing, empirical OOD examples in Tables 1--2/Figures 4--5, semi-supervised result in Table 3, interpretation of \(\lambda\). |
| `resources/NeurIPS-2019-integer-discrete-flows-and-lossless-compression-Paper.pdf` | Integer Discrete Flows, integer discrete coupling, discrete likelihood without continuous Jacobian term, discretized logistic prior, rounding and STE, compression motivation and results. |

---

## Figure and Equation Plan

- **Figure 1:** Discriminative vs. hybrid view of an unfamiliar input. Adapt conceptually from the book's motivation, with original drawing.
- **Figure 2:** Naive separate models vs. shared invertible backbone.
- **Figure 3:** Normalizing-flow likelihood via change of variables.
- **Figure 4:** Integer discrete coupling layer with rounding and additive update.
- **Equation 1:** Joint factorization \(p(x,y)=p(y \mid x)p(x)\).
- **Equation 2:** Change-of-variables formula using the chosen \(f\) convention.
- **Equation 3:** Weighted objective \(\mathcal{J}_\lambda\) and corresponding loss \(\mathcal{L}_\lambda\).
- **Equation 4:** Discrete-flow likelihood \(p_X(x)=p_Z(f^{-1}(x))\).
- **Equation 5:** Discretized logistic probability mass for integer-valued latent variables, if the IDF section needs one additional concrete formula.

---

## Open Questions / Placeholders

- [ ] Confirm the final notation convention for \(f\), \(f^{-1}\), \(z\), and Jacobians before drafting LaTeX.
- [ ] Extract exact bibliographic metadata for all cited sources into a `.bib` file.
- [ ] Decide the final report title, author metadata, abstract, and keywords for the LNCS template.
- [ ] Create original versions of the planned figures and add LaTeX labels.
