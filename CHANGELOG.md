# Changelog

All changes to the Shell Design Language specification are recorded here.

The versioning scheme is described in [CONTRIBUTING.md](contributing/CONTRIBUTING.md). Version numbers follow the pattern **Draft X.Y**, where the minor number increments for any change that adds, modifies, or removes a concept. Corrections that do not affect the meaning of any definition are noted here but do not change the version number.

---

## Draft 1.2 — February 2026

### Added

**Anchor Span** — a new property on the Anchor region type that describes how an Anchor relates spatially to perpendicular Anchors. Two values: `full-span` (the Anchor runs the full width or height of the Chrome) and `inset` (the Anchor fills only the space not occupied by a full-span perpendicular Anchor). Anchor Span is a Chrome-level property with direct CSS Grid implications — it determines which Anchor occupies a full grid row or column at the root layout level and must be declared before the Chrome is built.

Anchor Span resolves a gap exposed by the observation that the top Anchor in Claude Desktop spans the full Chrome width, with the sidebar starting below it — a structural fact with significant implementation consequences that the previous vocabulary left unnamed.

**Anchor-to-Anchor Controller Relationship** — an extension of the existing Controller Relationship concept to cover the case where a control in one Anchor drives the state of a different Anchor. The canonical example is the sidebar toggle button in a header Anchor that collapses and expands the leading Anchor. This pattern appears in the majority of web applications and was consistently left implicit in design documentation despite being a frequent source of implementation bugs. The new concept requires specifying: which Anchor contains the control, which Anchor is the target, what state is being driven, and whether the control reflects current state visually.

**Common Anchor Span patterns table** — a reference table of three common Anchor Span configurations: header-dominant (Top Anchor full-span, Leading Anchor inset), sidebar-dominant (Leading Anchor full-span, Top Anchor inset), and both-inset.

### Changed

**Anchor region type definition** — `Span` added to the list of Anchor properties.

**Claude Desktop SDL example** — updated throughout to include `Chrome Anchor Span: Top Anchor full-span, Leading Anchor inset` and the Anchor-to-Anchor controller declaration for the sidebar toggle button. The previous version described the sidebar toggle implicitly through the collapsible dimension behaviour; the new version names the cross-Anchor wiring explicitly.

---

## Draft 1.1 — February 2026

### Added

**Panel Group** — a named set of Panels that share a layout axis. All Panels within a group compress the Viewport along the same axis. Introduced to address the gap between SDL's single-Panel definition and real applications (notably VS Code) where multiple Panels can be active simultaneously on the same or different edges.

**Panel Policy** — a property of a Panel Group governing how multiple Panels within the group coexist. Three values: `Exclusive` (only one Panel open at a time; opening a second ejects the first), `Additive` (multiple Panels open simultaneously, each taking its defined size, with a required minimum Viewport dimension), and `Tabbed` (multiple Panels share one fixed-size layout region with a tab bar to switch between them).

**Corner Ownership** — a Chrome-level property specifying which Panel Group owns the corner when Panel Groups on two perpendicular axes are simultaneously active. Two values: horizontal group owns corner, or vertical group owns corner. Corner Ownership is structurally significant — it determines the CSS Grid layout of the Chrome at the root level and must be decided before the shell is built.

**Viewport dimension formula** — a reference formula for calculating available Viewport dimensions when multiple Panel Groups are active: `Viewport width = Chrome width − leading Anchor − leading Panel Group − trailing Panel Group`, and equivalently for height.

**VS Code SDL example** — a complete SDL description of VS Code demonstrating two simultaneous Panel Groups with different policies (Exclusive on the leading edge, Tabbed on the bottom edge) and explicit Corner Ownership.

### Changed

**Core Concepts section** — updated to reflect that Panel Groups join Transitions as a cross-cutting concept applying across all layers, rather than being a numbered layer in the hierarchy.

---

## Draft 1.0 — February 2026

### Initial release

The first complete draft of the Shell Design Language, developed through iterative dialogue between the author and Claude (Anthropic) in February 2026.

**Layer 1: Region Types**

Five region types defined: Chrome, Anchor, Viewport, Panel, Modal Overlay, and Anchored Overlay. Each type defined with properties, behaviour, and examples.

**Layer 2: Region Behaviours**

Viewport Mode defined with four values: Linear, Staged, Routed, and Spatial. Anchor Dimension Behaviour defined with three values: Fixed, Collapsible, and Resizable. Breakpoint Response defined with four values: Persist, Collapse, Lift, and Disappear. Trigger Types for Panels defined: User-initiated, Content-initiated, and State-initiated. Controller Relationship defined for Anchor-to-Viewport navigation wiring.

Spatial Viewport Properties defined as a subsection covering: navigation model (Pan, Zoom, Fit to screen, Zoom to selection, Reset), coordinate system (world vs screen coordinates), object model (Free-form, Node graph, Frame-based, Tile-based), and selection model (Single, Multi, Hierarchical).

Bidirectional Controller Relationship defined for Canvas applications where the Viewport both receives commands from and emits selection state to surrounding Anchors and Panels.

Minimap defined as a Viewport Fixture — a persistent, non-structural element positionally fixed within a Spatial Viewport.

**Layer 3: Anchor Zones**

Three zones defined: Anchor Header (fixed at top), Anchor Body (scrollable middle), Anchor Footer (fixed at bottom, always visible regardless of Body scroll position).

**Layer 4: Named Compositions**

Six named compositions defined: Workspace, Master-Detail, Canvas, Dashboard, Staged, Command. Each composition includes structure, typical region arrangement, breakpoint behaviour, and examples.

**Layer 5: Transitions**

Nine transitions defined: Collapse, Lift, Swap, Split, Merge, Inject, Eject, Lift Overlay, Dismiss Overlay.

**SDL Description Format**

Notation format defined. Worked example: Claude Desktop.
