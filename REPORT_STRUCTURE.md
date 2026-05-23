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

## Drafting Balance

- Treat Chapter 6 of the book as the report's spine. The supplementary papers clarify, support, or contextualize individual points; they should not determine the overall structure.
- Spend most of the report on the derivation, shared parameterization, scale mismatch, weighted objective, and HybridIDF construction.
- Keep the Discussion compact. It should interpret the book's example and outlook, not become a separate literature survey. Target roughly **700--1,000 words** for the full Discussion unless the later LaTeX draft clearly needs more.
- Use the bullets below as drafting guidance, not as a requirement to write one paragraph per bullet.

---

## Sections at a Glance

| # | Required Section | Working Title | Core Concepts | Main Sources |
|---|---|---|---|---|
| 1 | Introduction | Why Hybrid Modeling? | Motivation, OOD confidence failure, joint distribution | Book Ch. 1 and Ch. 6 |
| 2 | Background | Generative Modeling and Invertible Features | \(p(y \mid x)\), \(p(x)\), normalizing flows, change of variables, predictive head | Book Ch. 1, Ch. 4, and Ch. 6; Nalisnick et al. as support |
| 3 | Main Content / Results | Hybrid Models with Shared Invertible Representations | Naive vs. shared parameterization, scale mismatch, weighted objective, HybridIDF | Book Ch. 6; Nalisnick et al. and Hoogeboom et al. as support |
| 4 | Discussion | What the HybridIDF Example Suggests | Fig. 6.4 outcomes, book Section 6.5, OOD detection, semi-supervised learning, role of \(\lambda\), open directions | Book Ch. 6; Nalisnick et al. as support |
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

**Planned figures:**

- Include `thesis/figures/fig_1_2_discriminative_vs_hybrid.png` after the motivation for \(p(x,y)=p(y \mid x)p(x)\). Use it to contrast decision-boundary reasoning with density-aware reasoning.
- Optionally include `thesis/figures/fig_1_1_confidence_flip.png` before Fig. 1.2 if the introduction needs a concrete confidence-failure example and the page budget allows it.

---

### 2. Background — Generative Modeling and Invertible Features

**Goal:** Give the reader the mathematical tools needed for the main section.

#### 2.1 Discriminative, Generative, and Hybrid Models

- Define the discriminative component \(p(y \mid x)\) and the generative component \(p(x)\).
- Explain why \(p(x,y)=p(y \mid x)p(x)\) is useful for classification with uncertainty, while \(p(x,y)=p(x \mid y)p(y)\) is more natural when class-conditional generation is the primary goal.
- Mention that the report follows the book's preference for the \(p(y \mid x)p(x)\) factorization because the focus is reliable decision-making rather than class-conditional sampling.

#### 2.2 Normalizing Flows and Change of Variables

- Use Chapter 4 of the book as the primary source for this subsection.
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
- Explicitly walk through the book's equation chain from Eq. 6.1 to Eq. 6.5:
  - Eq. 6.1 introduces the joint factorization \(p(x,y)=p(y \mid x)p(x)\).
  - Eq. 6.2 writes the naive log-joint with separate parameterizations.
  - Eqs. 6.3--6.4 show that the gradients decouple when the classifier and density model share no parameters.
  - Eq. 6.5 introduces the shared parameterization with \(\gamma\), where both terms depend on the common representation.

**Planned figures:**

- Include `thesis/figures/fig_6_1_naive_separate_models.png` to show the naive two-model setup.
- Include `thesis/figures/fig_6_2_shared_parameterization.png` immediately after it to show how shared parameters create a common representation.
- If the LaTeX draft becomes too figure-heavy, place these two figures as subfigures or adjacent panels.

#### 3.2 The Scale Mismatch Problem

- Explain the dimensionality imbalance: the log-likelihood term for \(x\) often aggregates over many dimensions, while the label term may contribute only a small amount of information per example.
- Use the book's binary example: one binary label contributes roughly \(-\log 2\), while a \(D\)-dimensional binary input contributes roughly \(-D\log 2\) under uniform Bernoulli predictions.
- State the consequence: without correction, the generative signal can dominate the shared representation and hurt classification.
- Continue the equation chain from the book:
  - Eq. 6.6 states the unweighted shared log-joint.
  - Eq. 6.7 shows the shared-parameter gradient as a sum of discriminative and generative gradient contributions.
  - Eq. 6.8 introduces the convex combination proposed in earlier work and explains why the book treats it as useful but not fully probabilistically elegant.

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
- Treat the book's Eq. 6.9 as the final target of the derivation from Eqs. 6.1--6.8. Make clear that the report uses \(\mathcal{J}_\lambda\) for the maximized objective and \(\mathcal{L}_\lambda\) for the corresponding loss to avoid notation ambiguity.

**Planned figure:** Include `thesis/figures/fig_6_3_hybrid_flow_model.png` after the weighted objective to show how the invertible backbone feeds both \(p(y \mid x)\) and \(p(x)\).

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

**Optional figure:** Bipartite integer coupling layer showing split, neural transformation, rounding, and additive update.

---

### 4. Discussion — What the HybridIDF Example Suggests

**Goal:** Interpret the book's HybridIDF example and "What's Next?" section without turning the discussion into a survey of the supplementary papers.

**Scope:** Keep the full Discussion short, approximately 700--1,000 words in the final report. Prefer a few dense paragraphs over an expansive subsection-by-subsection survey.

Begin with a compact discussion of the book's Fig. 6.4:

- Explain what the four panels demonstrate at a high level: real images, unconditional generations, classification error during validation, and negative log-likelihood during validation.
- Use Fig. 6.4 as the bridge from the technical construction to the broader question: what does the hybrid objective achieve, and what remains unresolved?
- Do not overstate the empirical strength of this example. Treat it as an illustrative outcome of the book's implementation, not as a comprehensive benchmark.

#### 4.1 Out-of-Distribution Detection

- Keep this subsection short and conceptual. Explain the intended mechanism: \(p(x)\) can provide an input plausibility score before or alongside the predictive distribution.
- Connect back to the book's motivation from Chapter 1 and Chapter 6: hybrid modeling was introduced because \(p(y \mid x)\) alone cannot say whether \(x\) is familiar.
- Use Nalisnick et al. only as supporting empirical evidence, not as the organizing center:
  - Table 1 and Figure 4 compare MNIST with NotMNIST as the OOD set.
  - Table 2 and Figure 5 compare SVHN with CIFAR-10 as the OOD set.
- Avoid a universal claim that likelihood always solves OOD detection. Phrase the point as: likelihood gives a useful signal that must be validated empirically for the data and model family.

#### 4.2 Semi-Supervised Learning

- Base this subsection primarily on book Section 6.5. Explain why hybrid modeling naturally supports semi-supervised learning:
  - labeled data trains both \(p(y \mid x)\) and \(p(x)\),
  - unlabeled data can still train \(p(x)\),
  - the shared backbone can improve the representation available to the classifier.
- Mention Nalisnick et al.'s Table 3 only briefly as supporting evidence that unlabeled MNIST data improved performance relative to using 1000 labels alone.

#### 4.3 Limitations and Open Questions

This should be the strongest discussion subsection, but it should still stay compact. Group the book's Section 6.5 points into a few connected observations:

- **Objective trade-off:** \(\lambda\) requires tuning, can shift behavior between predictive and generative performance, and is not derived from a clean normalized probability model.
- **Extensions:** The hybrid idea extends beyond flows, for example to VAEs, but these variants may require variational bounds or different parameterizations.
- **Future directions:** Alternative architectures or learning procedures may reduce or remove the need for \(\lambda\).
- **Factorization choice:** \(p(y \mid x)p(x)\) is appropriate when reliable decision-making and input plausibility are central, while \(p(x \mid y)p(y)\) may be preferable for class-conditional generation.
- **IDF caveat:** IDFs solve a discrete-data modeling issue, but the straight-through estimator introduces biased gradients.

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
| `resources/hybrid_modeling.pdf`, Chapter 1 | Motivation for why \(p(y \mid x)\) alone is insufficient and why modeling \(p(x)\) helps with uncertainty-aware decision-making. |
| `resources/hybrid_modeling.pdf`, Chapter 4 | Primary source for normalizing flows, invertibility, latent variables, and the change-of-variables formula used in Section 2. |
| `resources/hybrid_modeling.pdf`, Chapter 6 | Main story, naive/shared parameterization, scale mismatch, weighted objective, HybridIDF formulation, Fig. 6.4 outcomes, and Section 6.5 discussion points. |
| `resources/nalisnick19b.pdf` | Supporting context for deep invertible features, exact joint density, GLM framing, empirical OOD examples in Tables 1--2/Figures 4--5, semi-supervised result in Table 3, and interpretation of \(\lambda\). |
| `resources/NeurIPS-2019-integer-discrete-flows-and-lossless-compression-Paper.pdf` | Integer Discrete Flows, integer discrete coupling, discrete likelihood without continuous Jacobian term, discretized logistic prior, rounding and STE, compression motivation and results. |

---

## Created Figure Assets

All figure assets are original conceptual redrafts. The `.drawio` files are editable source files; the `.png` files are ready for LaTeX inclusion. Captions should state that the figures are adapted conceptually from the corresponding book figures and cite the book.

| Thesis figure role | Editable source | PNG for LaTeX | Placement |
|---|---|---|---|
| Optional confidence-flip example, based on book Fig. 1.1 | `thesis/figures/fig_1_1_confidence_flip.drawio` | `thesis/figures/fig_1_1_confidence_flip.png` | Introduction, before the discriminative-vs-hybrid motivation if space allows. |
| Discriminative vs. density-aware decision-making, based on book Fig. 1.2 | `thesis/figures/fig_1_2_discriminative_vs_hybrid.drawio` | `thesis/figures/fig_1_2_discriminative_vs_hybrid.png` | Introduction, after introducing \(p(x,y)=p(y \mid x)p(x)\). |
| Naive separate parameterizations, based on book Fig. 6.1 | `thesis/figures/fig_6_1_naive_separate_models.drawio` | `thesis/figures/fig_6_1_naive_separate_models.png` | Main Content / Results, Section 3.1. |
| Shared parameterization, based on book Fig. 6.2 | `thesis/figures/fig_6_2_shared_parameterization.drawio` | `thesis/figures/fig_6_2_shared_parameterization.png` | Main Content / Results, Section 3.1, immediately after the naive setup. |
| Flow-based hybrid model, based on book Fig. 6.3 | `thesis/figures/fig_6_3_hybrid_flow_model.drawio` | `thesis/figures/fig_6_3_hybrid_flow_model.png` | Main Content / Results, Section 3.3, after the weighted objective. |

---

## Figure and Equation Plan

- **Figure 1:** Optional confidence-flip example (`fig_1_1_confidence_flip.png`), adapted conceptually from book Fig. 1.1.
- **Figure 2:** Discriminative vs. density-aware decision-making (`fig_1_2_discriminative_vs_hybrid.png`), adapted conceptually from book Fig. 1.2.
- **Figure 3:** Naive separate models (`fig_6_1_naive_separate_models.png`), adapted conceptually from book Fig. 6.1.
- **Figure 4:** Shared parameterization (`fig_6_2_shared_parameterization.png`), adapted conceptually from book Fig. 6.2.
- **Figure 5:** Flow-based hybrid model (`fig_6_3_hybrid_flow_model.png`), adapted conceptually from book Fig. 6.3.
- **Figure 6:** Book Fig. 6.4 outcomes, either described in text or redrawn as a compact conceptual results figure if the Discussion needs a visual bridge.
- **Figure 7:** Integer discrete coupling layer with rounding and additive update, to be created later if the HybridIDF section needs a model-specific figure.
- **Equation 1:** Joint factorization \(p(x,y)=p(y \mid x)p(x)\).
- **Equation 2:** Naive log-joint with separate parameterizations and the resulting gradient decoupling.
- **Equation 3:** Shared-parameter log-joint and shared-parameter gradient.
- **Equation 4:** Scale-mismatch illustration using the binary-label and \(D\)-dimensional binary-input example.
- **Equation 5:** Weighted hybrid objective \(\mathcal{J}_\lambda\) and corresponding loss \(\mathcal{L}_\lambda\), corresponding to the book's Eq. 6.9.
- **Equation 6:** Change-of-variables formula using the chosen \(f\) convention.
- **Equation 7:** Discrete-flow likelihood \(p_X(x)=p_Z(f^{-1}(x))\).
- **Equation 8:** Discretized logistic probability mass for integer-valued latent variables, if the IDF section needs one additional concrete formula.

---

## Open Questions / Placeholders

- [ ] Confirm the final notation convention for \(f\), \(f^{-1}\), \(z\), and Jacobians before drafting LaTeX.
- [ ] Extract exact bibliographic metadata for all cited sources into a `.bib` file.
- [ ] Decide the final report title, author metadata, abstract, and keywords for the LNCS template.
- [ ] Add LaTeX labels and final captions for the created figures during thesis drafting.
