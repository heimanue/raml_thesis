# Future Agent Notes

This repository contains a seminar report on **Hybrid Modeling** for the LMU
seminar *Recent Advances in Machine Learning*. The current main artifact is
`thesis/report.tex`, with the compiled PDF at `thesis/report.pdf`.

Use this file as a quick handoff before making changes. The longer planning
documents are `REPORT_STRUCTURE.md` and `SEMINAR_AI_GUIDELINES.md`.

---

## Current State

- The report title is **Hybrid Modeling**.
- The author is **Manuel Hein**.
- The report is intentionally concise: the current agreed target is roughly
  **4,500--5,500 words**, even though the official handout says
  **7,000--14,000 words**. Do not expand toward 7,000 words unless the user asks
  for strict formal compliance.
- The current LaTeX draft has already gone through several refinement passes for
  structure, math weight, notation, discussion length, and citation coverage.
- Before making substantial edits, read the relevant part of `thesis/report.tex`
  and preserve the current balance unless the user explicitly asks to change it.

---

## Source Hierarchy

Use the supplied local sources. Do not rely on outside sources unless the user
asks for additional literature.

1. `resources/hybrid_modeling.pdf`
   - Main source and structural backbone.
   - Use Chapter 6 for hybrid modeling, Chapter 4 for normalizing flows, and
     Chapter 1 for the motivation that \(p(y\mid x)\) alone is insufficient.
2. `resources/nalisnick19b.pdf`
   - Supporting source for deep invertible features, weighted hybrid objectives,
     out-of-distribution examples, semi-supervised learning, and the
     Jacobian-regularization interpretation of \(\lambda\).
3. `resources/NeurIPS-2019-integer-discrete-flows-and-lossless-compression-Paper.pdf`
   - Supporting source for Integer Discrete Flows, dequantization issues,
     integer discrete coupling, discretized logistic priors, rounding, and the
     straight-through estimator.

The book should remain the center of the report. Nalisnick et al. should not
become the organizing frame.

---

## Settled Writing Decisions

- Use the book's notation strictly:
  - \(\ln\), not `log`
  - \(z=f^{-1}(x)\)
  - \(\pi(z)\)
  - \(J_f\)
  - \(\lambda\)
  - \(\ell(x,y;\lambda)\) for the weighted objective
- The report explains the equation chain from Chapter 6, but keeps the math
  lighter than the book:
  - Eqs. 6.1--6.5 are displayed.
  - Eqs. 6.6--6.7 are handled in prose.
  - Eq. 6.8, Eq. 6.9, and Eq. 6.13 are displayed.
  - Eqs. 6.10--6.12 are summarized conceptually, not displayed.
- Section 3.3 should stay compact and structural. Avoid repeating too often that
  smaller/larger \(\lambda\) changes the classification-density balance.
- Section 3.4 should transition from continuous flows to integer-valued data and
  then explain HybridIDF.
- The Discussion is intentionally compact and paragraph-based. Do not turn it
  into a broad survey unless asked.
- Avoid meta phrasing like "the report treats..." in the report body.

---

## Figures

All current figures are original conceptual redrafts with editable `.drawio`
sources and `.png` files in `thesis/figures/`.

Included figures:

- `fig_1_1_confidence_flip`
- `fig_1_2_discriminative_vs_hybrid`
- `fig_6_1_naive_separate_models`
- `fig_6_2_shared_parameterization`
- `fig_6_3_hybrid_flow_model`
- `fig_6_4_hybrididf_outcomes`

Do not add the IDF coupling-layer figure for now. The user explicitly chose to
skip it.

Captions should say when a figure is an original conceptual redraw inspired by
the book, and they should cite the book.

---

## Citation And Plagiarism Rules

- Cite source-specific claims near the claim.
- Keep citations verified against the local PDFs.
- Do not copy sentences, paragraphs, captions, or explanations 1:1 from the book
  or papers unless using a short, explicitly marked quotation. The current report
  is written as paraphrase and should stay that way.
- If a figure, example, or equation comes from the book, cite the book in nearby
  prose or the caption.
- If a claim comes specifically from Nalisnick et al. or Hoogeboom et al., cite
  that paper directly.

---

## Build And Check Commands

Run commands from `thesis/` unless otherwise noted.

```sh
latexmk -pdf -interaction=nonstopmode -halt-on-error report.tex
pdftotext report.pdf - | wc -w
detex report.tex | wc -w
rg -n "Citation .* undefined|Reference .* undefined|undefined references|TODO|FIXME|VERIFY" report.log report.tex
```

Useful current reference point: after the latest citation pass, the source-text
word count was about 4,600 words and the PDF was 14 pages.

---

## Editing Guidance

- Prefer small, targeted edits.
- Keep the report didactic and lecture-script-like.
- Preserve the current section order:
  1. Introduction
  2. Background
  3. Main Content / Results
  4. Discussion
  5. Conclusion
  6. References
- If the user asks for a review, prioritize correctness, missing citations,
  notation consistency, and whether the prose is homogeneous.
- Rebuild the PDF after LaTeX edits.
