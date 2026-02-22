# Shell Design Language (SDL)

**A vocabulary for the structural architecture of web applications.**

---

When building web applications, the industry has well-established languages for two layers of design: component design has atomic design, and visual design has design tokens and design systems. But there has been no equivalent formal language for the **application shell** — the structural layer that defines how regions relate to each other, how layout changes in response to interaction, and how the chrome of an application is organised.

SDL fills that gap. It gives designers, developers, and product teams a shared vocabulary to describe, discuss, and specify the structural layer of an application before a line of code is written — or to precisely document an existing application's architecture.

SDL is framework-agnostic, tool-agnostic, and technology-agnostic.

---

## What SDL covers

- **Region types** — Chrome, Anchor, Viewport, Panel, and Overlay (Modal and Anchored)
- **Region behaviours** — Viewport modes (Linear, Staged, Routed, Spatial), Anchor Span, dimension behaviour, breakpoint responses, and trigger types
- **Anchor Zones** — the internal Header, Body, and Footer structure of Anchors
- **Controller relationships** — how Anchors drive Viewports, how Anchors drive other Anchors (the sidebar toggle pattern), and bidirectional relationships in Canvas applications
- **Spatial Viewport properties** — navigation models, object models, selection models, and the Minimap Viewport Fixture
- **Panel Groups** — Panel Policy (Exclusive, Additive, Tabbed) and Corner Ownership
- **Named compositions** — Workspace, Master-Detail, Canvas, Dashboard, Staged, and Command
- **Transitions** — the vocabulary for how shells change over time

---

## Quick example

Here is a complete SDL description of Claude Desktop:

> *Workspace pattern with Controller. Chrome Anchor Span: Top Anchor full-span, Leading Anchor inset. Top Anchor: header, fixed height, full-span. Contains: sidebar toggle button (Anchor-to-Anchor controller of Leading Anchor collapse state, reflects collapse state visually), tab bar (Controller of main Viewport). Leading Anchor: sidebar, collapsible, inset, breakpoint response: Lift. Anchor Header: workspace switcher. Anchor Body: conversation list, owned scroll. Anchor Footer: User Profile trigger (Anchored Overlay, preferred direction Up), Invite action (Modal Overlay). Main Viewport: Staged mode, three stages (Chat, Cowork, Code), owned vertical scroll per stage. Trailing Panel: content-initiated trigger (artifact content type), fixed width, compresses main Viewport on Inject, user-dismissible.*

A developer reading this has a clear architectural picture before writing a line of code. A designer can verify it matches their intent. A product manager can understand the structural implications of adding or removing a region.

---

## Repository structure

```
shell-design-language/
│
├── README.md                     ← you are here
├── LICENSE                       ← Creative Commons CC BY 4.0
├── CHANGELOG.md                  ← version history
│
├── spec/
│   └── SDL.md                    ← the canonical specification
│
├── reference/
│   ├── sdl-reference-dark.html   ← visual reference (dark theme)
│   └── sdl-reference-light.html  ← visual reference (light theme)
│
├── examples/
│   ├── README.md                 ← how to read and contribute examples
│   ├── claude-desktop.md
│   ├── figma.md
│   └── vscode.md
│
└── contributing/
    ├── CONTRIBUTING.md           ← how to propose changes
    └── PROPOSAL-TEMPLATE.md     ← structured template for new concepts
```

---

## Getting started

**To learn SDL:** Read the [specification](spec/SDL.md). Then open the [visual reference](reference/sdl-reference-light.html) in a browser — every concept in the spec has a corresponding diagram.

**To apply SDL:** Read the [examples](examples/) to see how real applications are described. Then try writing an SDL description of an application you know well. The worked examples for Claude Desktop, Figma, and VS Code are good starting points.

**To contribute:** Read [CONTRIBUTING.md](contributing/CONTRIBUTING.md). Proposals for new concepts, additional examples, and corrections to existing content are all welcome.

---

## Current version

**Draft 1.2** — February 2026

SDL is an evolving standard. See [CHANGELOG.md](CHANGELOG.md) for the full version history and a summary of what changed in each version.

---

## Licence

Shell Design Language is published under [Creative Commons CC BY 4.0](LICENSE). You are free to use, share, adapt, and build on SDL — including commercially — as long as you credit the source.

**Suggested citation:**

```
Shell Design Language (SDL), Draft 1.2
Developed by David Hait, with Claude (Anthropic), February 2026
https://github.com/OptionMetrics/shell-design-language
```

---

## Origin and authorship

SDL was developed in February 2026 through an extended collaborative conversation between the author and **Claude**, an AI assistant made by Anthropic.

The intellectual direction came from a practical problem: beginning the design of a web application and realising there was no shared language for discussing the shell architecture — the structural layer that sits above components and below visual design. Every existing vocabulary (atomic design, design tokens, CSS frameworks) addressed adjacent concerns but left this layer unnamed.

The concepts in SDL were developed iteratively, stress-tested against real applications (Claude Desktop, Figma, VS Code), and refined when those applications exposed gaps in the vocabulary. Claude contributed articulation, synthesis, and the worked examples. The author contributed the problem framing, the direction of inquiry, and the identification of every gap that pushed the language forward.

The result is a collaboratively developed specification. Both contributions are real; neither is sufficient alone. The citation above reflects this.

---

## Contributing

SDL is a living standard. The design of real applications will always surface cases the current vocabulary cannot describe cleanly — and those cases are exactly where the language needs to grow.

See [CONTRIBUTING.md](contributing/CONTRIBUTING.md) for how to propose additions, corrections, and new examples.
