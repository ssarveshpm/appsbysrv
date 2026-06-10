# feat: Simplify App Suite Launcher to Link Portal

Refactor the launcher dashboard into a single-page portal. The complex dashboard panels (telemetry graphs, notes, checkmarks, customizer, modals, and left sidebar navigation) will be removed. The interface will be refactored into a top-level header with search, and a card grid displaying tool cards that directly hyperlink to external or custom web tools in a new tab.

## Problem Frame & Scope Boundary

*   **Current State**: The dashboard includes heavy widgets (CPU charts, scratchpad notes, todo trackers, custom accessibility options, modal utilities) and a left sidebar menu which are overly complex for a simple launcher list.
*   **Proposed State**: A clean, high-fidelity landing grid.
    *   **Included**: Top header with logo, system online state, clock, center-aligned search input, and a responsive grid of card hyperlinks.
    *   **Excluded**: Sidebar menu, modals, telemetry canvas animation loops, scratchpad notes, customizer themes, and task checklist entries.
*   **Traceability**: Simplifies the dashboard as requested: "this app is only for listing all the apps and links with small descriptions. user will see it and can click hyperlink to open the apps."

## Key Technical Decisions & Rationale

*   **Theme Continuity**: Retain the dark glassmorphic styling system (Obsidian/Neon aesthetics) to keep the portal premium and high-fidelity, but modify the layout from multi-column grid areas to a single-column container structure.
*   **Static vs. Dynamic Cards**: Tool link cards will be statically written in `index.html` for SEO, rapid loading, and no-JS accessibility.
*   **JS Role Reduction**: `index.js` will only handle the global search input filter and the live clock update. Telemetry intervals and canvas operations will be completely removed to eliminate main-thread overhead.

---

## Proposed Changes

### Refactoring Components

#### [MODIFY] [index.html](file:///index.html)
*   Remove the `<aside>` sidebar menu element.
*   Update layout structure: wrap header and main content in a single-column wrapper.
*   Remove modal popup window container markup.
*   Simplify card links: change the `.tool-card` container to an `<a>` anchor tag that targets `_blank` for external navigation.
*   Pre-define a rich list of developer/designer tools:
    *   *StackEdit Markdown Editor*: `https://stackedit.io/app`
    *   *Coolors Palette Generator*: `https://coolors.co`
    *   *TinyPNG Image Compressor*: `https://tinypng.com`
    *   *JSONLint Validator*: `https://jsonlint.com`
    *   *RegEx101 Tester*: `https://regex101.com`
    *   *CSS Grid Generator*: `https://cssgrid-generator.netlify.app`

#### [MODIFY] [index.css](file:///index.css)
*   Change `.app-container` from grid layout (`"header header" "sidebar main"`) to a flexbox/block layout with centered max-width constraints (e.g., `max-w-6xl`).
*   Remove all classes associated with the sidebar (`.app-sidebar`, `.menu-btn`, etc.).
*   Remove widget-specific styling (notes textarea, checklist items, customizer pills, canvas containers).
*   Remove modal styles (`.modal-overlay`, `.modal-window`, etc.).
*   Update hover behaviors to style anchor cards (`a.tool-card`).

#### [MODIFY] [index.js](file:///index.js)
*   Delete code for canvas drawing, modal overlays, checklist, notes persistence, customizer, and keyboard numbers routing.
*   Keep clock rendering logic.
*   Keep search filter query checking: on input, toggle `display: none`/`flex` on `.tool-card` elements based on titles/descriptions.

---

## Implementation Units

### U1. Simplify HTML Structure
*   **Goal**: Streamline structural components.
*   **Files**: `index.html`
*   **Approach**: Remove the sidebar, widgets section, and modal HTML. Redefine cards as anchor tags wrapping title, description, and launch indicators.
*   **Test Scenarios**:
    *   Verify only header, search input, and launcher card grid render on the page.
    *   Ensure cards are correctly formatted as `<a>` tags with `target="_blank"`.

### U2. Update Layout Styles
*   **Goal**: Adjust layout rules to single-column.
*   **Files**: `index.css`
*   **Approach**: Purge unused sidebar, widget, and modal classes. Update `.app-container` to block-style layout centered at `max-width: 1200px` with responsive padding.
*   **Test Scenarios**:
    *   Check page reflow on desktop and mobile. No horizontal scrollbars.
    *   Verify glassmorphism hovers operate cleanly on anchor links.

### U3. Rewrite Script Logic
*   **Goal**: Purge heavy components and optimize JS runtime.
*   **Files**: `index.js`
*   **Approach**: Strip out telemetry draw loop, local storage notes/todos syncing, and customizer theme changes. Retain search filter and clock logic.
*   **Test Scenarios**:
    *   Verify search bar correctly filters card grid based on user typing.
    *   Ensure the digital clock continues updating.
