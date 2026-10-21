# Rendering rules (interactive site, `docs/shared/app.js`)

One renderer (`renderTopic`) serves pages, subtopics and called procedures alike. It follows three rules
that hold across the whole manual, with no per-chapter cases.

## The three rules

1. **Parts.**
   - A block may start with a part marker: the XML `<title>` "Removing" (anchor 0) or "Refitting"
     (anchor 1). It is always the block's first element, and a block never has more than one.
   - An unmarked block continues the part of the block before it.
   - Blocks before the first marker belong to no part.
   - Markers occur only in Procedures (918 topics) and one Test topic.
   - Repeated Removing/Refitting units in one topic are engine or body variants.
   - A page shows Removing/Refitting sections **only** if its visible blocks have markers.
2. **Calls.**
   - Every link to a topic in the Procedures section is a call ("Op. …"), wherever it stands: on its own
     line, inside a sentence, or in a table cell. This covers Procedures (including Maintenance), Test,
     fault diagnosis tables, electrical component tables and Descriptions.
   - Links to other sections (such as "See E3040 …" or connector pages) stay ordinary links.
3. **Applicability.** Every block has its own engine and period masks. Hidden blocks and calls to
   procedures that don't apply to the car are dropped.

## How a topic renders

- Unpartitioned blocks come first, then a **Removing** and a **Refitting** section for whichever parts
  the visible blocks have.
- Each call becomes a closed dropdown with ↗ (open as page; Back works) and ⧉ (new tab).
  - A call that fills its paragraph replaces the paragraph.
  - A call inside a sentence or table cell replaces just the link; its paragraph becomes a `div.para`, so
    the dropdown can sit inside it.
- A called procedure runs in the part it is called from. Its unpartitioned blocks inherit that part. It
  opens its matching part, and its other part stays collapsed until opened. Called from no part, both of
  its parts stay collapsed. On a page, both parts are open.
- **Loading:**
  - Calls inside a part are expanded right away, because repeats are folded in reading order.
  - Calls outside a part load when first opened. That keeps pages with many calls fast, such as component
    tables.
- **Folding repeats, inside a part only.** A call becomes an "already removed/refitted in this procedure"
  line when either:
  - the same procedure was already expanded earlier in that part, or
  - an enclosing procedure lists it itself in that part (it does it there, in its own order; this keeps
    the main Refitting list's order).

  Both checks apply only if the car could need both calls, meaning their engine and period masks
  overlap. So different engine variants keep their own calls.
- **Outside a part, repeats are kept.** There a repeated call is usually the reverse action (disconnect,
  later reconnect).
- A call that would loop is always shown as a reference. Consecutive references are grouped with a count.

## Other site behaviour

- **Filters:**
  - Engine and production period hide everything that does not apply.
  - Filters and the current topic are kept in the URL (`#/id?engine=..&period=..&options=..`) and in
    localStorage.
- **Equipment highlighting:**
  - A title is green when every option it requires is selected (AND), and grey otherwise.
  - Only titles and sidebar rows are tinted, and options never hide anything.
- **Sidebar:**
  - codes and titles in manual order
  - variant labels on same-named siblings
  - clicking a title or the triangle collapses or expands it
  - Expand all / Collapse all buttons
  - navigating opens only the path to the current topic
  - the sidebar can be resized (drag the edge, double-click resets) and hidden (« button)
- **Search:**
  - *Chapters* searches codes and titles; *Content* searches the full text.
  - Sidebar labels are CSS-generated (`data-label`), so the browser's Ctrl+F only searches the page.
- **Subtopics:** dropdowns that start collapsed; each opens inline or as its own page (↗).
- **Images:**
  - Diagrams default to the width of the table below them.
  - Drag anywhere on an image to resize it. A click without movement opens the zoom/pan viewer, and
    closing it (Esc or ×) resets the size.
- **Theme:** System / Light / Dark, with high contrast for workshop use.
- **Text version:** each topic links to its text version (`ai/t/<id>.html`).
- **Home** (top of the sidebar, `#/`): what the site does, then nested dropdowns that open in place:
  - a *Reference* list of topics found by section and title (`REFERENCE` in `app.js`): fuses and
    junction units, wire colour key, torques, products, tools, identification, maintenance plan,
    safety/battery disconnection
  - the most-referenced procedures, components and checks (`popular` in `tree.json`)
  - the section cards
