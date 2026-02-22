# SDL Proposal Template

Use this template when proposing a new concept, a modification to an existing concept, or a new named composition. Copy the relevant section below into a new GitHub issue and fill in all fields.

Incomplete proposals will be returned for revision before discussion begins. The template exists because the quality of a proposal is almost entirely determined by the specificity of the motivating application and the precision of the before/after SDL description — not by the elegance of the abstract definition.

---

## Proposal type

Select one:

- [ ] New concept (region type, behaviour, property, or named composition)
- [ ] Modification to an existing concept
- [ ] New named composition only
- [ ] Correction to an existing definition

---

## For new concepts and modifications

### 1. Summary

*One or two sentences. What is the concept and what structural pattern does it describe?*

---

### 2. Motivating application

*Name at least one real, publicly available application that exposes the gap this concept addresses. Vague references ("some apps do this") are not sufficient — name the application and describe the specific structural feature.*

**Application name:**

**Structural feature:**
*Describe what the application does structurally. Be specific — which region, what behaviour, what happens when.*

**Why the current SDL cannot describe it cleanly:**
*Quote the SDL description you would write today and explain exactly where it becomes ambiguous, incorrect, or silent.*

---

### 3. Proposed concept definition

*Write the definition as it would appear in the specification. Follow the style of existing definitions: name the concept, describe what it is, list its properties or values if applicable, and give at least one example.*

**Concept name:**

**Definition:**

**Properties / values (if applicable):**

**Examples:**

---

### 4. Before and after SDL description

*Write two complete SDL descriptions of the motivating application: one using only the current vocabulary (showing the gap), and one using the proposed concept (showing what it enables). This is the most important part of the proposal.*

**Before (current SDL, showing the gap):**

> *[SDL description]*

**What is wrong or missing:**

**After (with proposed concept):**

> *[SDL description]*

**What is now expressible that was not before:**

---

### 5. Stress-test against other applications

*Apply the proposed concept to at least one other application to verify it generalises. If the concept only applies to a single application, it may be too narrow.*

**Application:**

**SDL description with proposed concept:**

> *[SDL description]*

---

### 6. Conflict check

*Does the proposed concept conflict with, overlap with, or render redundant any existing SDL concept? If yes, explain how the conflict should be resolved.*

---

### 7. Implementation neutrality check

*SDL describes structure, not implementation. Confirm that the proposed concept does not require knowledge of a specific framework, CSS technique, or JavaScript pattern to understand or apply.*

- [ ] The concept can be described and understood without reference to any specific technology
- [ ] The concept can be applied to applications built in React, Angular, Vue, or any other framework

*If you checked neither box, explain:*

---

### 8. Suggested SDL version

*If accepted, which version number would this change warrant?*

- [ ] Minor version increment (new concept or modified definition) — Draft 1.X
- [ ] Correction (no meaning change) — no version increment

---

## For new examples only

If you are proposing a new worked example rather than a new concept, use this shorter form instead.

### Application name

### Why this application is a useful SDL example

*What structural patterns does it illustrate? Does it stress-test any existing concept in an interesting way?*

### SDL version this description is written against

### Complete SDL description

> *[SDL description]*

### Notes

*Any edge cases, ambiguities, or patterns worth calling out for readers.*

---

## For corrections

### Quote the text to be corrected

### What is wrong and why

### Suggested corrected text

### Does this correction change the meaning of the definition?

- [ ] Yes — this should be treated as a concept modification and use the full proposal form
- [ ] No — this is a clarification or correction only
