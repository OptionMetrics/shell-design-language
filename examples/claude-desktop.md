# Claude Desktop

**SDL version:** Draft 1.2
**Composition:** Workspace
**URL:** https://claude.ai

---

## Overview

Claude Desktop is Anthropic's primary web interface for conversational AI. It is a useful SDL example for several reasons:

- It is a clean instance of the **Workspace composition** in a modern SaaS application
- The header runs the full Chrome width with the sidebar toggle inside it — the canonical example of an **Anchor-to-Anchor controller relationship** and the primary motivation for adding **Anchor Span** to SDL
- The artifact panel is a rare real-world example of a **content-initiated Panel trigger** — the panel appears not because the user opened it, but because the content in the Viewport is of a type (code, rendered output) that causes the shell to reconfigure around it
- The Anchor Footer drop-up is a clear example of an **Anchored Overlay with a preferred direction**

---

## SDL description

> *Workspace pattern with Controller. Chrome Anchor Span: Top Anchor full-span, Leading Anchor inset.*
>
> *Top Anchor: header, fixed height, full-span. Contains: sidebar toggle button (Anchor-to-Anchor controller of Leading Anchor collapse state, reflects collapse state visually). Contains: tab bar (Controller of main Viewport, three stages: Chat, Cowork, Code).*
>
> *Leading Anchor: sidebar, collapsible, inset (starts below header), breakpoint response: Lift. Anchor Header: workspace switcher. Anchor Body: conversation list, owned scroll. Anchor Footer: User Profile trigger (Anchored Overlay, preferred direction Up), Invite action (Modal Overlay).*
>
> *Main Viewport: Staged mode, three stages (Chat, Cowork, Code), owned vertical scroll per stage.*
>
> *Trailing Panel: content-initiated trigger (artifact content type), fixed width, compresses main Viewport on Inject, user-dismissible via Eject.*

---

## Annotations

### Anchor Span and the sidebar toggle

The top Anchor in Claude Desktop is **full-span** — it runs edge to edge across the entire Chrome, and the sidebar starts below it rather than beside it. This is the header-dominant pattern.

The consequence is that the sidebar toggle button naturally lives in the header and visually spans the full width of the application. This placement only works because the header is full-span. In a sidebar-dominant layout (where the sidebar runs the full height and the header starts to the right of it), there is no equivalent full-width location for the toggle.

The toggle button creates an **Anchor-to-Anchor controller relationship**: a control in the Top Anchor drives the collapse state of the Leading Anchor. The button icon changes to reflect current state — this visual reflection of state must be explicitly wired in the implementation and is easy to omit.

### Staged Viewport and the tab Controller

The main Viewport is in **Staged mode** with three stages: Chat, Cowork, and Code. The tab bar in the Top Anchor is the Controller — selecting a tab swaps the content of the Viewport without changing the Viewport's position or dimensions in the layout.

This is a standard Anchor-to-Viewport controller relationship. The important implementation detail is that all three stages must maintain their scroll positions independently — switching from Chat to Code and back should not reset the Chat scroll position.

### Content-initiated Panel trigger

The Trailing Panel is one of the few real-world examples of a **content-initiated trigger**. The Panel does not open because the user clicked a button — it opens because the content in the Viewport is of a type (a code block with rendered output, an artifact) that causes the shell to reconfigure around it.

This creates an architectural dependency that must be explicitly designed: the Viewport's content must be able to communicate its type upward to the shell, which must then decide whether to Inject the Panel. This is state management at the shell level, not the component level. In most framework implementations this requires either a context provider at the Chrome level or a global state store that the content and the shell both read.

### Anchor Footer and the drop-up Overlay

The User Profile control in the Anchor Footer is an **Anchored Overlay with a preferred direction of Up**. Because it is at the bottom of the sidebar, opening downward would take it off screen — the preferred direction tells the positioning logic which way to open first, with a fallback if space is insufficient.

The Anchor Footer itself is a distinct pattern worth noting: it is always visible regardless of how far the Anchor Body has scrolled. This requires the Anchor to be internally structured as a flex column with the Body as the flex-growing middle section — it cannot simply be implemented as a single scrolling list.

### Breakpoint response: Lift

At narrow viewport widths, the Leading Anchor lifts — it detaches from the layout and becomes a slide-in Anchored Overlay (a drawer). The underlying layout expands to fill the reclaimed space. This is a mode change, not a CSS reflow — it requires JavaScript state to track whether the sidebar is in its docked or lifted state, because the two states have different interaction behaviour (the lifted sidebar can be dismissed by clicking outside it; the docked sidebar cannot).

---

## What this example does not cover

- The behaviour of the Trailing Panel when the Viewport is in Cowork or Code stage (as distinct from Chat)
- The specifics of the workspace switcher in the Anchor Header (which would warrant its own Anchored Overlay description if fully specified)
- Mobile layout, which likely involves additional breakpoint responses beyond the Lift on the Leading Anchor
