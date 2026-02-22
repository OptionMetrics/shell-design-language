# Shell Design Language (SDL)

## Introduction

When building web applications, the industry has well-established languages for two layers of design:

- **Component design** has atomic design — atoms, molecules, organisms — a structured vocabulary for building UI elements
- **Visual design** has design tokens, design systems, and tools like Figma

But there has been no equivalent formal language for the **application shell** — the structural architecture of how an application is laid out, how regions relate to each other, and how the layout changes in response to user interaction, content, and screen size.

The Shell Design Language (SDL) fills that gap. It gives designers, developers, and product teams a shared vocabulary to describe, discuss, and specify the structural layer of an application before a line of code is written — or to document an existing application's architecture precisely.

SDL operates at the layer *above* components and *below* visual design. It is framework-agnostic, tool-agnostic, and technology-agnostic.

---

## Core Concepts

SDL is built from four layers:

1. **Region Types** — what structural elements exist
2. **Region Behaviours** — how each region acts
3. **Anchor Zones** — internal structure within regions
4. **Named Compositions** — common patterns built from regions

Two cross-cutting concepts apply across all layers:

- **Transitions** — how the shell changes over time
- **Panel Groups** — how multiple simultaneous Panels coexist

---

## Layer 1: Region Types

Every structural element in an application shell is one of five region types.

### Chrome

The outermost container of the application. The Chrome is the root of the shell — it frames everything else and defines the absolute boundary of the application.

- Always occupies the full viewport (`100vh`, `100vw`)
- Never scrolls itself (`overflow: hidden`)
- Contains all other regions
- Typically implemented as a CSS Grid at the root level

The Chrome is not usually visible as a distinct element — it is the invisible container that makes independent scrolling in child regions possible. Without `overflow: hidden` on the Chrome, child regions cannot have their own independent scroll contexts.

### Anchor

A fixed-dimension region inside the Chrome. Anchors provide persistent navigation, toolbars, status bars, and utility controls.

**Properties:**
- **Axis** — horizontal (top/bottom) or vertical (left/right)
- **Position** — leading (left/top), trailing (right/bottom)
- **Dimension** — fixed size, collapsible (snaps between two sizes), or resizable (user-draggable)
- **Persistence** — always visible, or conditionally visible
- **Span** — full-span or inset (see Anchor Span below)

**Examples:** sidebar navigation, top header bar, bottom status bar, toolbar

### Viewport

A flexible region that fills remaining space after Anchors are placed. A Viewport owns its own scroll context — it scrolls independently of all other regions. An application shell can contain multiple Viewports simultaneously.

**Properties:**
- **Scroll behaviour** — vertical, horizontal, spatial (pan/zoom), or none
- **Viewport Mode** — how its content is navigated (see Layer 2)

The Viewport is where application content lives. Everything inside a Viewport — components, lists, forms, conversations — belongs to that Viewport's scroll context.

### Panel

A region that is dynamically inserted into the layout in response to content or application state. Unlike an Anchor, a Panel is not structurally present in the layout at all times — it appears and disappears, changing the layout when it does.

**Properties:**
- **Position** — leading or trailing relative to the adjacent Viewport
- **Dimension** — fixed width/height, or resizable
- **Trigger** — what causes it to appear (see Triggers, below)
- **Dismissal** — how it closes (user action, state change, or both)

**Critical distinction from Overlay:** A Panel *participates in the layout* — it compresses adjacent Viewports when it appears. An Overlay floats above the layout and leaves it unchanged.

**Examples:** an artifact/preview panel that opens when editing code, a detail panel in a master-detail view, a properties panel in a design tool

### Overlay

A region that appears above the layout temporarily without affecting the layout of other regions beneath it.

Overlays come in two distinct subtypes:

**Modal Overlay**
- Positionally independent — centred relative to the Chrome regardless of where it was triggered
- Dims or blocks interaction with the rest of the shell
- Requires explicit dismissal before the user can continue
- Examples: confirmation dialogs, onboarding flows, settings screens

**Anchored Overlay**
- Positionally dependent — anchors itself to the element that triggered it
- Does not dim the shell; does not block interaction elsewhere
- Has a preferred direction (up, down, left, right) but detects available space and flips if needed
- Examples: dropdown menus, drop-up menus, tooltips, context menus, date pickers, command palettes

> **Note on Anchored Overlays and CSS:** The CSS Anchor Positioning API, now landing in modern browsers, provides native browser support for the positioning logic Anchored Overlays require.

---

## Layer 2: Region Behaviours

Each region has a set of behaviours that define how it acts at runtime.

### Viewport Mode

A Viewport's mode describes how its content is navigated:

| Mode | Description | Examples |
|------|-------------|---------|
| **Linear** | Content scrolls continuously in one direction. No switching between states. | A conversation thread, an article, a feed |
| **Staged** | Content swaps between discrete states, driven by a navigation control. | Tab bars, segmented controls, wizard steps |
| **Routed** | Content is driven by the URL. Typically involves a full region transition on navigation. | Standard page routing in most web applications |
| **Spatial** | Content exists in a two-dimensional coordinate space navigated by panning and zooming. The Viewport is a camera over a canvas, not a window over a flow. | Design tools (Figma, Miro), maps, node editors |

### Anchor Dimension Behaviour

| Behaviour | Description |
|-----------|-------------|
| **Fixed** | The Anchor has a defined size that does not change |
| **Collapsible** | The Anchor snaps between two sizes — expanded and collapsed. In collapsed state it may show only icons, abbreviated labels, or nothing |
| **Resizable** | The user can drag the Anchor boundary to any size within defined min/max constraints |

### Breakpoint Response

How a region responds when the viewport narrows below a defined breakpoint. This is a *mode change*, not a CSS reflow — it often requires JavaScript state, not just CSS.

| Response | Description |
|----------|-------------|
| **Persist** | Region remains visible and unchanged |
| **Collapse** | Region collapses to minimum dimension |
| **Lift** | Region detaches from the layout and becomes an Anchored Overlay (e.g. a sidebar becomes a drawer) |
| **Disappear** | Region is removed from the layout entirely |

### Trigger Types (for Panels)

What causes a Panel to appear:

| Trigger | Description | Example |
|---------|-------------|---------|
| **User-initiated** | Explicit user action — a button click, menu selection | Opening a settings panel |
| **Content-initiated** | The Panel appears because of the type or state of content in an adjacent Viewport | A code preview panel appearing when the user writes a code block |
| **State-initiated** | Application state causes the Panel to appear | A notification panel appearing when a background task completes |

**Content-initiated triggers** deserve special architectural attention: they create a dependency between a Viewport's content and the surrounding shell structure. The content is effectively reaching up and changing the layout around itself. This has significant implications for state management — the wiring between content state and shell structure must be explicitly designed.

### Controller Relationship

A controller relationship exists when an Anchor contains a navigation control that drives the content of a Viewport. The Anchor and the Viewport it controls are linked — the state of the navigation control in the Anchor determines which content mode the Viewport is in.

This relationship must be explicitly identified during design and explicitly implemented in code. It is a common source of bugs when left implicit.

### Anchor-to-Anchor Controller Relationship

A controller relationship may also exist between two Anchors. This occurs when a control in one Anchor drives the state of a different Anchor — most commonly, a toggle button in the top Anchor that collapses or expands the leading Anchor (the sidebar toggle / hamburger pattern).

This is one of the most common patterns in web applications and is almost always left implicit in design documentation, which is precisely why it causes bugs. The wiring between the toggle button and the Anchor it controls must cross region boundaries in the application state, and without naming it, that crossing is often forgotten or implemented inconsistently.

**An Anchor-to-Anchor controller relationship must specify:**
- Which Anchor contains the control (the *controller Anchor*)
- Which Anchor is being controlled (the *target Anchor*)
- What state the control drives (typically: collapsed / expanded)
- Whether the control reflects current state visually (i.e. does the button icon change when the sidebar is collapsed?)

**In an SDL description**, write: *Top Anchor contains sidebar toggle, Anchor-to-Anchor controller of Leading Anchor collapse state.*

### Anchor Span

Anchor Span describes how an Anchor relates spatially to perpendicular Anchors — specifically, whether it runs the full dimension of the Chrome on its axis or whether it yields space to Anchors on the perpendicular axis.

| Span | Description | CSS Grid implication |
|------|-------------|---------------------|
| **Full-span** | The Anchor runs the full width or height of the Chrome, regardless of what other Anchors are present on the perpendicular axis. | The full-span Anchor occupies an entire grid row or column at the Chrome level. Perpendicular Anchors start inside it. |
| **Inset** | The Anchor runs only the portion of its axis not occupied by full-span perpendicular Anchors. | The inset Anchor shares its grid row or column with the full-span Anchor's allocated space. |

Anchor Span is a property of the Chrome, not of the individual Anchor, because it describes a relationship between Anchors. Like Corner Ownership for Panel Groups, it must be specified before the root CSS Grid is defined — changing it later requires reworking the Chrome layout.

**Span must be specified for every Anchor in the shell.** When two Anchors are on perpendicular axes (e.g. a top Anchor and a leading Anchor), exactly one of them must be full-span and the other inset. When both claim full-span, the result is a conflict that must be resolved explicitly.

**Common patterns:**

| Pattern | Top Anchor | Leading Anchor | Example |
|---------|-----------|---------------|---------|
| Header-dominant | Full-span | Inset (starts below header) | Claude Desktop, most SaaS apps |
| Sidebar-dominant | Inset (fills remaining width) | Full-span (runs full height) | Classic email clients, some IDEs |
| Both inset | Neither full-span — they share the corner | — | Rare, requires explicit corner treatment |

**Why it matters:** A full-span top Anchor means the sidebar toggle button can live in the header and visually span the entire width of the application — the most common placement. An inset top Anchor means the header starts only at the right edge of the sidebar, and there is no natural place in the header for a sidebar toggle that spans the full width.

### Bidirectional Controller Relationship

In most shells the controller relationship is one-directional: an Anchor drives a Viewport. In Canvas applications this relationship runs both ways. The Viewport's selection state drives what the surrounding Panels and Anchors display — selecting an object on the canvas changes the properties Panel, the layers Panel, and the active tool state simultaneously.

This is architecturally distinct from a standard controller relationship and must be named explicitly when present. It has significant state management implications: the Viewport is no longer a passive recipient of navigation commands but an active emitter of selection events that reshape the surrounding shell.

**Bidirectional controller relationships should be specified as:**
- Which Panels and Anchors are driven by Viewport selection state
- What selection states trigger which changes (single object selected, multiple selected, nothing selected)
- What the default state of those Panels is when nothing is selected

### Spatial Viewport Properties

A Spatial Viewport has properties and behaviours that do not apply to other Viewport modes. When a Viewport is declared as Spatial, the following must also be specified:

**Navigation model** — how the user moves around the canvas:

| Navigation | Description |
|-----------|-------------|
| **Pan** | Translate the camera in X and Y. Typically two-finger drag, middle-click drag, or spacebar + drag. |
| **Zoom** | Scale the camera. Typically pinch gesture or scroll wheel. |
| **Fit to screen** | Reset the camera so all content is visible. Typically a keyboard shortcut. |
| **Zoom to selection** | Move the camera to frame the currently selected objects. |
| **Reset** | Return to origin at 100% zoom. |

Not all navigation modes need to be supported. Which ones are available must be specified.

**Coordinate system** — Spatial Viewports maintain two coordinate systems simultaneously:

- *World coordinates* — the position of objects in the canvas space, independent of zoom and pan
- *Screen coordinates* — the position of objects on the user's display, dependent on current camera state

This distinction is not merely technical. It affects how hit testing works, how snapping and alignment tools operate, and how the minimap represents position. The SDL does not specify implementation, but noting that a Spatial Viewport operates in world coordinates is important for communicating intent.

**Object model** — unlike Linear Viewports where content flows in document order, a Spatial Viewport contains positioned objects in a coordinate space. The SDL should note the object model type:

| Object Model | Description | Examples |
|-------------|-------------|---------|
| **Free-form** | Objects may be placed anywhere in the coordinate space with no constraints | Whiteboards (Miro), illustration tools |
| **Node graph** | Objects are nodes connected by edges. Position is meaningful but constrained by connection topology | Node editors, flow builders, pipeline tools |
| **Frame-based** | Objects exist within named frames or artboards that provide organisational structure | Design tools (Figma), presentation tools |
| **Tile-based** | The coordinate space is divided into a discrete grid. Objects occupy cells | Maps, strategy games, grid editors |

**Selection model** — in a Spatial Viewport, selection is a shell-level concern, not a component-level concern. The selection model must be specified:

| Selection Type | Description |
|---------------|-------------|
| **Single** | One object may be selected at a time |
| **Multi** | Multiple objects may be selected simultaneously via shift-click or marquee |
| **Hierarchical** | Objects may be selected at different levels of nesting (e.g. a group, then a child within the group) |

### Minimap

The minimap is a region type unique to Spatial Viewports. It is a small thumbnail representation of the entire canvas that shows the current camera position and allows direct navigation by clicking or dragging within the thumbnail.

The minimap does not fit cleanly into existing SDL region types:
- It is not an Anchor — it has no fixed axis and does not compress the Viewport
- It is not an Overlay — it is persistent, not triggered
- It is not a Panel — it participates in the Viewport visually but not structurally

The minimap is therefore defined as a **Viewport Fixture** — a persistent, non-structural element that is positionally fixed within a Viewport (typically a corner) and serves both display and navigation purposes.

**Minimap properties:**
- **Position** — which corner of the Viewport it occupies (typically bottom-right)
- **Default visibility** — visible or hidden until triggered
- **Toggle** — whether the user can show/hide it
- **Navigation** — whether clicking/dragging the minimap moves the camera (most do)

---

## Layer 3: Anchor Zones

Anchors are not flat — they have internal structure. Most real Anchors have distinct zones with different layout rules and persistence behaviour.

### The Three Zones

| Zone | Description | Typical Contents |
|------|-------------|-----------------|
| **Anchor Header** | The top portion of an Anchor. Fixed position within the Anchor. | Workspace/org switcher, app logo, search bar |
| **Anchor Body** | The middle portion of an Anchor. This zone may itself scroll independently if its content exceeds available space. | Navigation items, conversation list, file tree |
| **Anchor Footer** | The bottom portion of an Anchor. Fixed position within the Anchor, always visible regardless of Anchor Body scroll position. | User profile, account controls, utility actions |

The Anchor Footer is a particularly important pattern: it provides persistent utility controls that must always be accessible, regardless of how far the user has scrolled through the Anchor Body.

Elements in the Anchor Footer typically trigger Overlays — either Anchored Overlays (for quick access menus like a user profile drop-up) or Modal Overlays (for heavier flows like inviting team members).

---

## Layer 4: Named Compositions

Named compositions are common shell patterns built from the region types above. Naming them allows teams to communicate the overall structure of an application in a single term.

### Workspace

**Structure:** One leading Anchor (sidebar) + one Viewport

The dominant pattern for productivity applications and tools. The sidebar provides navigation or context; the Viewport shows content.

**Typical breakpoint response for the Anchor:** Collapse or Lift

**Examples:** Claude, VS Code, Slack, Notion, Gmail

**Variant — Workspace with Controller:** When the Viewport operates in Staged mode, driven by a tab bar in the top Anchor, this is a Workspace with a Controller relationship between the header Anchor and the main Viewport.

### Master-Detail

**Structure:** Two Viewports side by side, where selection in the left Viewport drives content in the right Viewport

The left Viewport acts as a navigable list; the right Viewport shows the detail of whatever is selected. A controller relationship exists between the two Viewports.

**Typical breakpoint response:** At narrow widths, the left Viewport becomes an Anchor or disappears, and the right Viewport fills the Chrome.

**Examples:** Email clients, file browsers, settings screens with category lists

### Canvas

**Structure:** One Spatial Viewport surrounded by Anchors and Panel Groups containing tools, properties, and organisational controls

The Canvas composition is the most structurally complex of the named compositions because it involves a Bidirectional Controller Relationship — the surrounding Anchors and Panels both drive the Viewport and are driven by it.

**Typical region arrangement:**

The activity bar (a narrow leading Anchor) contains tool selectors. Selecting a tool changes the active tool state in the Viewport. The properties Panel (typically trailing) displays properties of whatever is selected in the Viewport — its content is entirely driven by Viewport selection state. The layers or objects Panel (typically leading, in a Panel Group with the activity bar or as a separate Panel) shows the object hierarchy of the canvas and reflects selection state bidirectionally — selecting in the layers Panel selects on the canvas, and vice versa.

**The Bidirectional Controller Relationship in Canvas:**

| Anchor / Panel | Drives Viewport | Driven by Viewport |
|---------------|----------------|-------------------|
| Activity bar (tool selector) | Yes — sets active tool | No |
| Layers / Objects Panel | Yes — selecting a layer selects the object | Yes — selecting an object highlights its layer |
| Properties Panel | No | Yes — displays properties of selected object |
| Zoom control (in header Anchor) | Yes — sets camera zoom | Yes — reflects current zoom level |

**Selection states that drive shell changes:**

| Selection State | Effect on Shell |
|----------------|----------------|
| Nothing selected | Properties Panel shows default / empty state |
| Single object selected | Properties Panel shows that object's properties |
| Multiple objects selected | Properties Panel shows shared/common properties only |
| Group selected | Properties Panel shows group properties; layers Panel expands group |

**Minimap:** Canvas applications almost always include a Minimap Viewport Fixture, positioned in the bottom-right corner of the Spatial Viewport.

**Examples:** Figma, Miro, Google Maps, Lucidchart, draw.io, node-based editors (Node-RED, Unreal Blueprint)

**Example SDL description — Figma:**

```
Canvas pattern.
Chrome Anchor Span: Top Anchor full-span, all other Anchors inset.

Leading Anchor: activity bar, fixed 48px, drives Viewport tool state → only.

Leading Panel Group: policy: Exclusive, resizable width.
  Layers Panel — bidirectional controller
                 drives and is driven by Viewport selection
  Assets Panel — user-initiated

Top Anchor: toolbar, fixed height.
  zoom control — bidirectional controller
                 drives and reflects Viewport camera state

Main Viewport: Spatial mode.
  Object model:    frame-based
  Selection model: multi + hierarchical
  Navigation:      pan, zoom, fit-to-screen, zoom-to-selection
  Viewport Fixture: Minimap, bottom-right corner, user-toggleable

Trailing Panel Group: policy: Exclusive, fixed width 240px.
  Design Panel    — driven by Viewport selection only
  Prototype Panel — driven by Viewport selection only
  Inspect Panel   — driven by Viewport selection only

Bidirectional controllers:
  Layers Panel ↔ Viewport selection
  zoom control ↔ Viewport camera state

Chrome corner ownership: Top Anchor owns full width.
```

### Dashboard

**Structure:** One Viewport containing a grid of independent sub-Viewports (cards or widgets)

Each widget is visually independent and may contain its own content, data, and scroll context. The outer Viewport provides the overall scroll context.

**Examples:** Analytics dashboards, monitoring tools, home screens

### Staged (standalone)

**Structure:** One Viewport in Staged mode, no persistent Anchors

The entire application surface swaps between discrete states. Common in onboarding flows, wizards, and step-by-step tools.

**Examples:** Setup wizards, multi-step forms, slide presentations

### Command

**Structure:** No persistent Anchors; one full-screen Viewport; one triggered Anchored Overlay (the command palette)

The application surface is minimal. Primary navigation happens through a keyboard-triggered command palette Overlay.

**Examples:** Linear (command mode), Raycast, Alfred

---

## Layer 5: Transitions

How the shell *changes* is as important as how it is structured at any given moment. SDL defines a vocabulary for shell transitions.

| Transition | Description | Trigger |
|------------|-------------|---------|
| **Collapse** | An Anchor shrinks to its minimum dimension. Adjacent Viewports expand to fill reclaimed space. | User-initiated |
| **Lift** | An Anchor detaches from the layout and becomes an Anchored Overlay — typically a slide-in drawer. The underlying layout expands to fill the space. | Breakpoint response |
| **Swap** | One Viewport's content replaces another within the same layout region. The region itself does not change size or position. | Routing or Staged navigation |
| **Split** | One Viewport divides into two, creating a new scroll context alongside the original. | User-initiated or content-initiated |
| **Merge** | Two Viewports merge back into one, dissolving one scroll context. | User-initiated or dismissal |
| **Inject** | A Panel is inserted into the layout, compressing adjacent Viewports. | Trigger (user, content, or state) |
| **Eject** | A Panel is removed from the layout, and adjacent Viewports expand to fill reclaimed space. | Dismissal |
| **Lift Overlay** | An Anchored or Modal Overlay appears above the layout. The layout beneath is unchanged. | User-initiated or state-initiated |
| **Dismiss Overlay** | An Overlay closes. The layout beneath is unchanged. | User action or state change |

---

## Writing an SDL Description

An SDL description expresses the full shell architecture of an application in a compact, precise form. It should be readable by both designers and developers and should convey enough information to begin implementation without reference to visual assets.

### Format

Start with the named composition, then describe each region in order (top Anchor → leading Anchor → main Viewport → trailing Panel → any Overlays), stating its type, dimension behaviour, Viewport mode if applicable, and controller relationships.

### Example: Claude Desktop

```
Workspace pattern with Controller.
Chrome Anchor Span: Top Anchor full-span, Leading Anchor inset.

Top Anchor: header, fixed height, full-span.
  sidebar toggle — Anchor-to-Anchor controller of Leading Anchor
                   collapse state, reflects collapse state visually
  tab bar        — Controller of main Viewport

Leading Anchor: sidebar, collapsible, inset, breakpoint response: Lift.
  Anchor Header: workspace switcher
  Anchor Body:   conversation list, owned scroll
  Anchor Footer: User Profile trigger (Anchored Overlay, preferred direction Up)
                 Invite action (Modal Overlay)

Main Viewport: Staged mode, three stages (Chat, Cowork, Code),
               owned vertical scroll per stage.

Trailing Panel: content-initiated trigger (artifact content type),
                fixed width, compresses main Viewport on Inject,
                user-dismissible.
```

This description conveys:
- The overall pattern
- Anchor Span — the header is full-span, the sidebar is inset
- The Anchor-to-Anchor controller relationship between the sidebar toggle and the sidebar
- The internal zone structure of the sidebar
- The two Overlay types and their triggers
- The Anchor-to-Viewport controller relationship between the tab bar and main Viewport
- The Panel trigger type and its layout impact

A developer reading this has a clear architectural picture before writing a line of code. A designer reading this can verify it matches their intent. A product manager reading this can understand the structural implications of adding or changing a region.

---

## SDL and the Broader Design Process

SDL sits within a broader workflow for application design:

1. **App Shell (SDL)** — define the structural architecture first, prototype directly in the browser with placeholder content. This is the one layer where designing in a visual tool alone is insufficient — the shell must be experienced in a real browser to understand scroll behaviour, resize behaviour, and responsive mode changes.

2. **Design Language** — define tokens (colour, spacing, typography, elevation) in parallel. Tokens flow from design tools into SCSS variables, creating a shared vocabulary between design and code.

3. **Components in Storybook** — build and style individual components in isolation. Components depend on the design language (tokens) and live inside the shell's regions. Storybook bridges component design and component code.

4. **Routing and State Architecture** — once the shell and key components exist, define how navigation works, how state is shared between regions, and how data flows. Controller relationships identified in the SDL directly inform state architecture decisions.

5. **Full Application Assembly** — wire components into the shell with real data and real routing. Because the shell is already stable and the components already exist, this is assembly rather than creation.

---

## Quick Reference

### Region Types

| Type | Scrolls | Affects Layout | Persistent | Triggered By |
|------|---------|---------------|------------|-------------|
| Chrome | Never | — | Always | — |
| Anchor | Body zone only | Yes | Usually | — |
| Viewport | Yes (owns context) | Yes | Always | — |
| Panel | Optional | Yes (compresses Viewports) | No | Trigger |
| Modal Overlay | Optional | No | No | User/State |
| Anchored Overlay | Optional | No | No | User/State |

### Named Compositions at a Glance

| Name | Anchors | Viewports | Primary Use |
|------|---------|-----------|-------------|
| Workspace | Leading (sidebar) + optional Top | 1 | Productivity tools |
| Master-Detail | Optional | 2 (linked) | List + detail views |
| Canvas | Multiple (tools) | 1 Spatial | Creative tools |
| Dashboard | Optional | 1 + sub-Viewports | Analytics, monitoring |
| Staged | Optional | 1 Staged | Wizards, onboarding |
| Command | None persistent | 1 + Command Overlay | Keyboard-first tools |

---

*Shell Design Language — Draft 1.0*
*Developed through conversation with Claude, February 2026*

---

## Panel Groups

The SDL's Panel definition works cleanly when a single Panel is present. When multiple Panels can be open simultaneously — on different edges, or multiple Panels on the same edge — three additional concepts are needed: **Panel Group**, **Panel Policy**, and **Corner Ownership**.

### Panel Group

A Panel Group is a named set of Panels that share a layout axis. All Panels within a group compress the Viewport along the same axis (horizontal or vertical).

**Properties:**
- **Axis** — horizontal (top/bottom edge) or vertical (leading/trailing edge)
- **Position** — which edge the group occupies (leading, trailing, top, bottom)
- **Policy** — how multiple Panels within the group coexist
- **Minimum Viewport dimension** — the smallest the Viewport may be compressed to before the policy changes or Panels are forcibly ejected

An application may have multiple Panel Groups on different edges simultaneously. This is where Corner Ownership becomes necessary.

### Panel Policy

Panel Policy governs how multiple Panels within a single Panel Group coexist:

| Policy | Description | When to use |
|--------|-------------|-------------|
| **Exclusive** | Only one Panel may be open at a time. Opening a second automatically ejects the first. | Panels serve similar purposes and shouldn't compete for space. Simplest to implement. |
| **Additive** | Multiple Panels may be open simultaneously, each taking its defined size. Viewport is compressed by the sum. Requires a minimum Viewport dimension — below which the policy falls back to Exclusive. | Panels show genuinely different content users need in parallel. |
| **Tabbed** | Multiple Panels share one fixed-size layout region with a tab bar to switch between them. Layout footprint is always one Panel wide/tall regardless of how many are available. | Many Panels are possible but screen space is limited. Common in IDEs. |

### Corner Ownership

When Panel Groups on two different axes are simultaneously active, they meet at a corner of the Viewport. That corner must be owned by exactly one group. **Corner Ownership** must be explicitly specified — it is not a visual preference but a structural decision with CSS Grid implementation consequences.

| Owner | Result |
|-------|--------|
| **Horizontal group owns corner** | Bottom (or top) Panel Group runs the full width inside the Anchors. Vertical Panel Group stops at the edge of the horizontal group. |
| **Vertical group owns corner** | Trailing (or leading) Panel Group runs the full height inside the Anchors. Horizontal Panel Group fills only the remaining width. |

Corner Ownership is a property of the **Chrome**, not of the Panel Groups. It is specified once and must be decided before building the root grid. Changing it later requires reworking the Chrome layout.

### Viewport Dimension Formula

When multiple Panel Groups are active, the Viewport's available dimensions are:

```
Viewport width  = Chrome width  − leading Anchor − leading Panel Group − trailing Panel Group
Viewport height = Chrome height − top Anchor − bottom Anchor − bottom Panel Group
```

These calculations must be maintained in application state, because they inform when to trigger policy fallbacks, when to warn about insufficient space, and how to restore Panel states on reopen.

### Example: VS Code

```
Canvas pattern (adapted — main Viewport is Staged, not Spatial).
Chrome Anchor Span: Top Anchor full-span, all other Anchors inset.

Leading Anchor: activity bar, fixed 48px.
  tool selectors — Anchor-to-Panel-Group controller of Leading Panel Group

Leading Panel Group: policy: Exclusive, resizable width.
  Explorer       — user-initiated via activity bar
  Search         — user-initiated via activity bar
  Source Control — user-initiated via activity bar
  Extensions     — user-initiated via activity bar

Top Anchor: tab bar, fixed height.

Main Viewport: Staged mode (open editor tabs), owned scroll per tab.

Bottom Panel Group: policy: Tabbed, resizable height.
  Terminal — user-initiated
  Output   — state-initiated
  Problems — state-initiated

Chrome corner ownership: Bottom Panel Group owns full width.
```

### Writing Panel Groups in an SDL Description

```
Trailing Panel Group: policy: Additive, minimum Viewport width: 400px.
  Artifact Panel   — content-initiated, fixed 380px
  References Panel — user-initiated,    fixed 280px

Bottom Panel Group: policy: Exclusive.
  Terminal Panel — user-initiated,  resizable 200–400px
  Output Panel   — state-initiated, fixed 240px

Chrome corner ownership: Bottom Panel Group owns full width.
```

---

*Shell Design Language — Draft 1.2*
*Anchor Span and Anchor-to-Anchor Controller Relationship added February 2026*
