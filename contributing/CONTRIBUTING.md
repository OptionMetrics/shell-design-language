# Contributing to SDL

Thank you for your interest in contributing to the Shell Design Language.

SDL is a living standard. The design of real applications will always surface structural patterns the current vocabulary cannot describe cleanly — and those cases are exactly where the language needs to grow. The best contributions come from encountering a real application that the SDL cannot describe precisely, and formalising what is missing.

---

## What kinds of contributions are welcome

### New examples
The most immediately useful contributions are SDL descriptions of real applications not yet covered. A good example demonstrates the vocabulary in use, stress-tests it against edge cases, and makes the spec more legible to newcomers.

### Proposals for new concepts
If you encounter a structural pattern in a real application that SDL cannot describe cleanly, that is a candidate for a new concept. New concepts must earn their place — the bar is whether the concept enables an SDL description that was genuinely impossible or ambiguous before.

### Corrections and clarifications
If existing definitions are ambiguous, contradicted by a real application, or inconsistent with other parts of the spec, corrections are welcome. Please be specific about what is wrong and why.

### Diagram improvements
The visual reference is a key part of SDL. Contributions that make the diagrams clearer, more accurate, or more complete are welcome.

---

## What makes a good proposal

The core question is: **does this concept enable an SDL description that wasn't possible before?**

A concept that merely renames something already expressible in SDL is not a good candidate. A concept that resolves a genuine ambiguity — where two different structures would receive the same SDL description under the current vocabulary — is a strong candidate.

Before submitting a proposal, try writing an SDL description of the application that exposes the gap. If you can describe it adequately with the current vocabulary, the gap may not require a new concept.

---

## How to submit a proposal

1. Open an issue using the **Proposal** label
2. Use the [proposal template](PROPOSAL-TEMPLATE.md) — fill in all sections
3. Include at least one real application that exposes the gap
4. Include a before/after SDL description showing what the new concept enables

Proposals will be discussed in the issue before any changes are made to the spec. The goal of discussion is to check the concept against other applications and ensure it does not conflict with existing vocabulary.

---

## How to submit an example

1. Fork the repository
2. Create a new file in `examples/` named after the application (e.g. `notion.md`)
3. Follow the structure in the existing examples
4. Open a pull request with a brief description of why the application is a useful addition

A good example file includes:
- A brief description of the application and what makes it structurally interesting
- The complete SDL description
- Notes on any patterns or edge cases the application illustrates
- The SDL version the description was written against

---

## How to submit a correction

1. Open an issue using the **Correction** label
2. Quote the specific text that is incorrect or ambiguous
3. Explain what is wrong and why
4. Suggest the corrected text if possible

For small corrections (typos, formatting), a pull request directly is fine. For anything that changes the meaning of a definition, please open an issue for discussion first.

---

## Design principles for SDL

When evaluating proposals — including your own — these principles should guide the decision:

**Precision over brevity.** An SDL description should be unambiguous. If two different shell architectures could receive the same description, the vocabulary is not doing its job.

**Named, not implied.** The reason SDL exists is that structural decisions were being left implicit. A concept that names something real is always preferable to leaving it undescribed. The sidebar toggle pattern is a good example — it existed in almost every web application for years without a name, which is precisely why the wiring was consistently left undocumented.

**Earn your concept.** Every new concept adds cognitive load for everyone learning SDL. A concept must describe something that genuinely cannot be expressed with existing vocabulary. Synonyms and stylistic variants are not concepts.

**Stress-test against real applications.** An abstract definition that sounds right but cannot be applied to a real application cleanly is not ready. Every concept in SDL was tested against at least one real application. New concepts should be too.

**Implementation neutrality.** SDL describes structure, not implementation. It should never require knowledge of a specific framework, CSS technique, or JavaScript pattern. A concept that only makes sense in React is not an SDL concept.

---

## On the use of AI in contributions

SDL was itself developed through human-AI collaboration — see the README for the full context. Contributions developed with AI assistance are welcome, subject to the same standards as any other contribution.

If you use AI assistance in developing a proposal or example, please disclose this in the pull request or issue. The standard for acceptance is the quality and correctness of the contribution, not the method by which it was developed.

---

## Versioning

SDL uses a simple draft versioning scheme: Draft 1.0, Draft 1.1, Draft 1.2, and so on. Changes to the spec that add new concepts, modify existing definitions, or remove concepts increment the minor version. Corrections that do not change the meaning of any definition do not change the version number but are noted in the changelog.

Breaking changes — where an existing SDL description would need to be rewritten — are handled with care and noted explicitly in the changelog.

---

## Code of conduct

SDL is a technical vocabulary project. Discussions should focus on the concepts and the applications that motivate them. Contributions that are respectful, specific, and grounded in real examples are most welcome.

---

## Questions

If you are unsure whether something is worth proposing, open a discussion issue first. The question "I encountered this pattern and I am not sure SDL covers it cleanly — thoughts?" is a perfectly good starting point.
