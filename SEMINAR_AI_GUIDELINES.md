# AI Writing Guidelines — MA Seminar "Recent Advances in Machine Learning"
> LMU Munich | Prof. Eyke Hüllermeier & Valentin Margraf | SoSe 2026

This file defines the rules and context for AI assistance on this seminar report. Follow these instructions precisely for every task.

---

## 1. What You Are Helping With

This is a **university seminar report** for the Master's seminar *"Recent Advances in Machine Learning"* at LMU Munich. The deliverable is a written report accompanied by a 20-minute presentation. The report is **not** a research paper — it is a **lecture-script-style summary** of an assigned ML topic.

---

## 2. Hard Formal Requirements

| Parameter | Requirement |
|---|---|
| **Language** | English |
| **Length** | 7,000 – 14,000 words (~10 pages is sufficient for this topic) |
| **Format** | LaTeX (template already located in `thesis/`) |
| **Style** | Lecture script — concise, explanatory, didactic |

> When generating or editing text, keep a running estimate of word count and flag if the draft is heading outside the 7,000–14,000 range. Around 10 pages is a natural target given the scope of the topic.
> If the page target and word-count target appear to conflict during drafting, flag the conflict explicitly instead of silently compressing or expanding the text.
> The official seminar handout currently says "characters"; this is a typo. Treat **7,000–14,000 words** as the correct requirement.

> **Current drafting decision:** After reviewing the scope, the current report intentionally targets roughly **4,500–5,500 words** because this is more concise while still covering the topic completely. If strict formal compliance becomes necessary, expand the draft toward the official 7,000-word lower bound rather than silently changing the target.

### Guideline Source Files

| File | Use |
|---|---|
| `guidelines/_Teaching__MA_Seminar__SoSe_26_ (dragged).pdf` | Official seminar organization, grading split, report requirements, and lecture-script framing. |
| `guidelines/Whitesides_writing_res_paper.pdf` | Writing process and style rules: outline-first drafting, importance-based organization, figure/table/equation planning, and concise scientific prose. |

---

## 3. Report Structure

Follow this section order. Do not reorganize without being asked.

1. **Introduction** — Why is this topic relevant? What are the central motivations and hypotheses? What should the reader expect?
2. **Background** — What prior work exists? What concepts does the reader need to understand the main content?
3. **Main Content / Results** — The core technical material. Organized by topic, not by chronology. Start with the most important results.
4. **Discussion** — What do the results mean? What are limitations, open questions, or implications?
5. **Conclusion** — A concise list of key takeaways. Not a summary of what was already said — add a higher level of analysis.
6. **References** — Properly formatted bibliography.

> The Conclusions section must go beyond restating the Results. It should indicate the significance of the work and what was learned.

---

## 4. Writing Style Rules

These are derived from Whitesides' *"Writing a Paper"* (Adv. Mater. 2004) and apply to every piece of text generated.

### Voice and Tense
- Use the **active voice** whenever possible.
  - ❌ *It was observed that the model converged.*
  - ✅ *The model converged.* / *We observed that the model converged.*
- Describe specific experimental findings from papers in the **past tense**.
  - ❌ *Addition of regularization gives better accuracy.*
  - ✅ *Addition of regularization gave better accuracy.*
- Use the **present tense** for definitions, mathematical facts, and the report's own structure.
  - ✅ *A normalizing flow maps data to a latent variable through an invertible transformation.*
  - ✅ *Section 3 introduces the weighted hybrid objective.*

### Clarity
- The word **"this"** must always be followed by a noun so its reference is explicit.
  - ❌ *This leads to higher accuracy.*
  - ✅ *This regularization strategy leads to higher accuracy.*
- **Complete all comparisons** — never leave them dangling.
  - ❌ *The accuracy was higher.*
  - ✅ *The accuracy was higher than the baseline.*
- Do **not** use nouns as adjectives.
  - ❌ *gradient descent optimization convergence*
  - ✅ *convergence of gradient descent optimization*

### Structure and Length
- Organize content **by importance**, not by chronology.
- Shorter is better. Compress information into figures, tables, and equations where possible. Text explains the data — it is secondary to the data.
- Section headings should be **specific and information-rich**.
  - ❌ *Experimental Results*
  - ✅ *Attention Mechanisms Improve Long-Range Dependency Modeling*

---

## 5. Grading Criteria to Optimize For

### Overall Seminar Grade
- Active participation in the seminar: **10%**
- Presentation quality and independent preparation: **60%**
- Written report quality: **30%**

### Report (30% of final grade)
- Structure and readability
- Length and balance between breadth and depth
- Formal notation and correctness
- Linguistic quality
- Amount and quality of illustrations, figures, and tables
- References and bibliography

---

## 6. Outline-First Workflow

> A drafted report structure is documented in **`REPORT_STRUCTURE.md`** in this repository. Use it as the authoritative reference for section order, subsection breakdown, and content scope. Feel free to suggest improvements, but do not deviate from it without being explicitly asked to.
> The outline must preserve the formal top-level section order from Section 3. Topic-specific headings in `REPORT_STRUCTURE.md` should be nested inside those required sections.

Before drafting any section, produce an **outline** following this process (adapted from Whitesides):

1. Write down all important ideas in any order — hypotheses, results, open questions.
2. Sort them into the three main heaps: Introduction / Main Content / Conclusion.
3. Organize each heap by importance (most important first).
4. Identify where figures, tables, or equations belong — place them before writing prose around them.
5. Only begin drafting prose once the outline is approved.

> Do not start writing full text until the outline for a section is confirmed. Iterating on an outline is far more efficient than rewriting prose.
> Write the abstract only after the main report is stable. Whitesides explicitly recommends delaying the abstract until the paper is complete.

---

## 7. Figures, Tables, and Equations

- Every figure and table must have a **descriptive caption** that makes it self-contained.
- Prefer figures and tables over text for presenting structured information.
- Equations must use **consistent notation** throughout the document — define all symbols on first use.
- Illustrations should directly support a claim made in the surrounding text.

---

## 8. References

- Cite all claims that are not common knowledge.
- Use the reference style required by the LaTeX template.
- Prefer **primary sources** (original papers) over textbooks or blog posts where possible.
- Do not cite sources you (or I) cannot verify. If a citation is uncertain, flag it explicitly with `[VERIFY]`.

### Academic Integrity and Source Use

- Avoid plagiarism strictly: do **not** copy whole sentences, paragraphs, captions, or explanations 1:1 from the book, papers, slides, or any other source.
- Exact wording is only acceptable when it is intentionally used as a short quotation, clearly marked as a quotation, and cited correctly.
- Default to paraphrasing in original language while preserving the technical meaning and citing the source of the idea.
- When adapting a figure, table, definition, or example from a source, make the adaptation explicit in the caption or surrounding text and cite the source.
- If a sentence is too close to a source sentence, rewrite it before it enters the report draft.

---

## 9. What AI Should and Should Not Do

| Task | Allowed |
|---|---|
| Drafting and editing text | ✅ Yes |
| Suggesting structure and outlines | ✅ Yes |
| Explaining technical concepts | ✅ Yes |
| Summarizing papers | ✅ Yes (with citations) |
| Generating figures or diagrams | ✅ Yes (as drafts) |
| Fabricating citations or results | ❌ Never |
| Submitting text without human review | ❌ Never |
| Changing the formal structure without being asked | ❌ No |

> All AI-generated content must be reviewed and verified by the author before submission. The author is responsible for the final text.

---

## 10. Source Material and Resources

All source material is located in the `resources/` folder in this repository. The report must be written primarily based on these sources — do not rely on general knowledge or outside sources unless something is clearly missing from the provided material.

### Primary Source — Book
The **book** in `resources/` is the main source. The report is based on **Chapter 6: Hybrid Modeling**. This chapter should drive the structure, depth, and content of the report. When in doubt about what to include or how to frame something, defer to how the book presents it.

- Read and internalize Chapter 6 before drafting any section.
- The report's structure should reflect the logical flow of the chapter.
- Figures, definitions, and examples from the chapter may be adapted (with citation).

> **Note:** Chapter 6 contains an implementation example with code. The report does **not** include any code. Concepts from the implementation may be described and explained in prose, but no code blocks or snippets should appear in the report.

### Supplementary Sources — Papers
The two **papers** in `resources/` serve as supplementary material. Use them to:
- deepen or extend specific points made in the book,
- provide concrete examples or empirical results,
- add recent context where the book's treatment is more theoretical.

They should not replace the book as the structural backbone of the report.

### Repository Layout
```
/
├── guidelines/      ← official seminar/writing guidelines
├── thesis/          ← write the LaTeX report here; LNCS template already present
├── resources/       ← source PDFs for the report
└── SEMINAR_AI_GUIDELINES.md
```

> Before starting any draft, read Chapter 6 of the book in full. Then check both papers for sections that directly relate to topics covered in that chapter.

### Current Source Files

| File | Role in the report |
|---|---|
| `resources/hybrid_modeling.pdf` | Main source; use Chapter 6, **Hybrid Modeling**, as the structural backbone. |
| `resources/nalisnick19b.pdf` | Primary supplementary paper for hybrid models with deep invertible features, weighted objectives, OOD detection, and semi-supervised learning. |
| `resources/NeurIPS-2019-integer-discrete-flows-and-lossless-compression-Paper.pdf` | Primary supplementary paper for Integer Discrete Flows, discrete coupling layers, rounding, STE, and lossless compression context. |

---

## 11. Quick Checklist Before Submission

- [ ] Word count follows the current agreed target of roughly 4,500–5,500 words, or the draft has been deliberately expanded if strict 7,000–14,000-word compliance is required
- [ ] Written in English
- [ ] Compiled in LaTeX using the Moodle template
- [ ] All section headings are specific and informative
- [ ] Active voice used throughout
- [ ] All "this" references followed by a noun
- [ ] All comparisons are complete
- [ ] Every figure/table has a caption
- [ ] All symbols defined on first use
- [ ] All citations verified and correctly formatted
- [ ] Conclusions go beyond restating results
