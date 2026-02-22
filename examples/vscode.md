# Visual Studio Code

**SDL version:** Draft 1.2
**Composition:** Canvas (adapted)
**URL:** https://code.visualstudio.com

---

## Overview

Visual Studio Code is Microsoft's open-source code editor. It is one of the most structurally complex applications in the SDL example set, and was the primary motivation for introducing **Panel Groups**, **Panel Policy**, and **Corner Ownership** into the specification.

VS Code is a useful SDL example because:

- It has **two Panel Groups operating simultaneously** on perpendicular axes — a leading vertical group and a bottom horizontal group — which requires Corner Ownership to resolve
- The two Panel Groups use **different Panel Policies** — Exclusive on the leading edge, Tabbed on the bottom — demonstrating that policies are per-group, not per-application
- The activity bar on the leading edge is a visually distinctive Anchor that drives an Exclusive Panel Group, making the Anchor-to-Panel-Group controller relationship unusually legible
- The main Viewport is in **Staged mode** (open editor tabs), not Spatial mode — which is a deliberate departure from the Canvas composition's typical Spatial Viewport, and worth noting explicitly

### A note on composition classification

VS Code is classified here as **Canvas (adapted)** because it matches the Canvas composition's structural signature — a primary editing surface surrounded by tool Anchors and Panel Groups — but the main Viewport is Staged rather than Spatial. The editing surface does not pan and zoom; it is a tab container where each tab hosts a scrolling document.

This is a genuine edge case in the named compositions. The Canvas composition as defined in the spec presupposes a Spatial Viewport. VS Code demonstrates that the surrounding Anchor and Panel Group architecture of a Canvas application can exist independently of a Spatial Viewport — the composition is better understood as a structural pattern than a strict set of viewport modes.

---

## SDL description

> *Canvas pattern (adapted — main Viewport is Staged, not Spatial). Chrome Anchor Span: Top Anchor full-span, all other Anchors inset.*
>
> *Top Anchor: title bar / menu bar, fixed height, full-span.*
>
> *Leading Anchor: activity bar, fixed 48px, inset. Contains: tool selectors (Anchor-to-Panel-Group controller of Leading Panel Group — each tool selector icon opens the corresponding Panel; active icon reflects open state).*
>
> *Leading Panel Group, policy: Exclusive, resizable width (default ~240px). Contains: Explorer Panel (user-initiated via activity bar), Search Panel (user-initiated via activity bar), Source Control Panel (user-initiated via activity bar), Run and Debug Panel (user-initiated via activity bar), Extensions Panel (user-initiated via activity bar). All Panels in group: user-dismissible via activity bar re-click or close button.*
>
> *Secondary Side Bar (Trailing Panel Group, policy: Exclusive, resizable width, user-initiated toggle). Contains: any Panel dragged from Leading Panel Group by user.*
>
> *Main Viewport: Staged mode (open editor tabs), owned scroll per tab. Tab bar: Controller of main Viewport.*
>
> *Bottom Panel Group, policy: Tabbed, resizable height (default ~200px), user-initiated toggle (Ctrl+`). Contains: Terminal Panel (user-initiated, multiple terminal instances supported within Panel), Output Panel (state-initiated), Problems Panel (state-initiated), Debug Console Panel (state-initiated).*
>
> *Chrome Corner Ownership: Bottom Panel Group owns full width.*
>
> *Status bar: Bottom Anchor, fixed height, full-span, always visible.*

---

## Annotations

### Two Panel Groups, two policies

The most important structural feature of VS Code for SDL purposes is that it has two Panel Groups simultaneously active, using different policies:

**Leading Panel Group — Exclusive:** Only one Panel from the Explorer / Search / Source Control / Extensions set can be open at a time. Opening Search closes Explorer. The activity bar makes this natural — each icon corresponds to one Panel, and only one can be active.

**Bottom Panel Group — Tabbed:** The Terminal, Output, Problems, and Debug Console panels share a single fixed-size region. A tab bar within the region switches between them. The layout footprint is always one Panel tall regardless of how many tabs exist. This is the correct policy choice here because the Panels are all supplementary to the editing surface and benefit from being easily switchable, but each individually is not so important that users need to see them simultaneously.

The two groups requiring different policies is itself an important observation: Panel Policy is a per-group decision, not an application-wide one. A single application can have groups with different policies.

### Corner Ownership in practice

When both Panel Groups are open, they must negotiate the bottom-right corner of the Chrome interior. In VS Code, the **Bottom Panel Group owns full width** — the terminal strip runs edge to edge across the full width of the Chrome interior, and the Leading Panel Group stops at the top edge of the terminal strip.

The alternative — Leading Panel Group owns corner, running full height with the terminal filling only the remaining width — would produce a terminal that does not span the full width, which would be awkward for a horizontally scrolling terminal output.

Corner Ownership is not a visual preference here. It has a functional rationale: terminals and log outputs read left-to-right and benefit from the maximum available horizontal width.

### The activity bar as Anchor-to-Panel-Group controller

The activity bar is a narrow fixed Anchor that exclusively serves as a navigation control for the Leading Panel Group. This is an Anchor-to-Panel-Group controller relationship — clicking an icon in the Anchor opens the corresponding Panel in the group. The active icon reflects the currently open Panel.

This is structurally equivalent to an Anchor-to-Viewport controller relationship (like a tab bar driving a Staged Viewport), but the target is a Panel Group rather than a Viewport. The pattern is common enough in IDE-style applications to be worth naming explicitly in SDL descriptions.

### Staged Viewport vs Spatial

The main editing surface is **Staged mode**, not Spatial. Each open file is a tab; selecting a tab swaps the editor content without changing the Viewport's position in the layout. Each tab has its own scroll position, maintained independently.

This is worth noting because VS Code is visually and structurally similar to Canvas applications (surrounded by tool Anchors and Panel Groups) but does not use a Spatial Viewport. The distinction matters for implementation: Staged Viewports have routing and state concerns around tab management; Spatial Viewports have coordinate system and camera state concerns. VS Code has the former, not the latter.

### The status bar

VS Code has a persistent bottom Anchor below the Bottom Panel Group — the status bar, which displays branch name, errors and warnings, language mode, encoding, and other metadata. This is a **Bottom Anchor, full-span, always visible**, distinct from the Bottom Panel Group above it.

When the Bottom Panel Group is closed, the status bar remains. When it is open, the status bar sits below it. The status bar never participates in Panel Group compression — it is an Anchor with fixed dimension, not part of any Panel Group.

This produces a three-layer vertical structure at the bottom of the Chrome: Bottom Panel Group (optional, compresses Viewport when open) → Status Bar (always present, fixed height). The status bar must be specified separately from the Panel Group.

### The secondary side bar

Recent versions of VS Code added a Secondary Side Bar — a Trailing Panel Group where users can drag Panels from the Leading Panel Group. This is listed in the SDL description as a Trailing Panel Group with Exclusive policy. In practice its policy is configurable by the user's drag-and-drop actions rather than fixed at application level, which is an interesting edge case: Panel Groups whose contents are user-configurable rather than application-defined. The SDL description reflects the default state.

---

## What this example does not cover

- The breadcrumb bar above the main Viewport (an Anchored element between the tab bar and the editor surface)
- The minimap within the editor surface (a Viewport Fixture within a Staged Viewport — an unusual combination)
- Split editor behaviour, where the main Viewport can be divided into two Viewports side by side (a Split transition, producing a temporary Master-Detail-like configuration)
- The Command Palette (a Modal Overlay triggered by Ctrl+Shift+P, giving VS Code a secondary Command composition mode alongside its primary Canvas structure)
