# SDL Examples

This directory contains worked SDL descriptions of real, publicly available applications.

Each example demonstrates the SDL vocabulary in use against a real shell architecture. Collectively they serve as a stress-test of the specification — every gap that has been found and addressed in SDL was discovered by trying to describe a real application and finding the vocabulary insufficient.

---

## How to read an example

Each example file contains:

- **Overview** — what the application is and why it is a useful SDL example
- **SDL description** — the complete formal description using SDL notation
- **Annotations** — notes on specific patterns, edge cases, or concepts the application illustrates
- **SDL version** — the version of the specification the description was written against

The SDL description is the canonical part. The annotations exist to help readers understand *why* the description is written the way it is, and to draw attention to patterns worth noticing.

---

## Current examples

| Application | Composition | Notable patterns |
|-------------|-------------|-----------------|
| [Claude Desktop](claude-desktop.md) | Workspace | Anchor Span, Anchor-to-Anchor controller, content-initiated Panel |
| [VS Code](vscode.md) | Canvas | Multiple Panel Groups, Exclusive and Tabbed policies, Corner Ownership |
| [Figma](figma.md) | Canvas | Spatial Viewport, bidirectional controllers, Viewport Fixture (Minimap) |

---

## How to contribute an example

1. Fork the repository
2. Create a new file in this directory named after the application in lowercase with hyphens (e.g. `notion.md`, `linear.md`)
3. Follow the structure of the existing examples
4. Open a pull request with a brief note on why the application is a useful addition

A good example contributes something the existing set does not already cover — a new named composition, an unusual combination of behaviours, or a pattern that stress-tests a concept in an interesting way. Applications that are structurally similar to an existing example without adding anything new are lower priority.

See [CONTRIBUTING.md](../contributing/CONTRIBUTING.md) for the full guidelines.

---

## A note on examples and the spec

Examples illustrate the specification; they do not define it. If an example description conflicts with the spec, the spec takes precedence and the example should be corrected. If an application cannot be described cleanly with the current vocabulary, that is a candidate for a new concept proposal rather than an example with a footnoted workaround.

Examples are versioned against the SDL draft they were written against. When the spec changes in a way that affects an existing example, the example should be updated and the SDL version field revised.
