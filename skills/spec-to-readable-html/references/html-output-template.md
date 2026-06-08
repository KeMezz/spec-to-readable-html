# HTML Output Template Guide

Reference: `references/template.html`

Use `template.html` as the structural and visual foundation for every generated HTML file. Copy its CSS verbatim and follow the section order below. Add or remove sections based on the source material, but keep the overall layout, class names, and styling consistent.

## Section Order

1. **Header** — title, subtitle, metadata bar (doc type, version, date, audience, source)
2. **TOC sidebar** — auto-generated from h2/h3 headings; sticky on desktop, inline on mobile
3. **Executive Summary** — 2-3 paragraphs + summary cards grid
4. **Key Concepts** — glossary grid of terms and definitions
5. **Workflows** — Mermaid diagrams with captions + step lists
6. **Functional Requirements** — tables grouped by feature, with priority/status badges
7. **Non-Functional Requirements** — table by category (performance, security, reliability, ...)
8. **System / API Overview** — architecture diagram, endpoint table, data model diagram
9. **Risks & Open Questions** — risk cards with severity, open questions list, assumptions callout
10. **Appendix** — source traceability table mapping output sections to original sections
11. **Footer** — source attribution and generation date

Omit sections that have no corresponding content in the source. Never generate empty sections.

## Component Reference

### Summary Cards `.card-grid > .card`

Use for quantitative highlights in the executive summary. Each card has:
- `.card-label` — category name (uppercase, muted)
- `.card-value` — primary number or short value
- `.card-detail` — one-line breakdown

Accent variants: `.card--accent-blue`, `.card--accent-green`, `.card--accent-amber`, `.card--accent-red`.

### Badges `.badge`

Priority: `.badge-must`, `.badge-should`, `.badge-could`, `.badge-wont`
Status: `.badge-confirmed`, `.badge-inferred`, `.badge-assumption`, `.badge-open`
Risk: `.badge-risk-high`, `.badge-risk-medium`, `.badge-risk-low`

Use badges inline in table cells and risk card headers.

### Tables `.spec-table`

Standard table for structured data. Keep columns narrow and scannable. Use `<code>` for identifiers, paths, field names. Place badges in dedicated Priority/Status columns.

### Callouts `.callout`

Variants: `.callout-info`, `.callout-warning`, `.callout-danger`, `.callout-success`

Each callout contains a `.callout-title` and body content. Use sparingly for cross-cutting notes, caveats, and assumptions.

### Diagrams `.diagram-container`

Wrap Mermaid blocks in `<figure class="diagram-container"><div class="mermaid">...</div><figcaption>...</figcaption></figure>`.

Supported Mermaid types: `graph`, `sequenceDiagram`, `stateDiagram-v2`, `erDiagram`, `gantt`, `pie`, `flowchart`.

The base template loads Mermaid from a CDN. If the output must be fully self-contained or usable offline, replace Mermaid blocks with inline `<svg>` elements instead of including the CDN script.

### Glossary `.glossary`

Grid of `.glossary-item` cards, each with `.glossary-term` and `.glossary-def`. Use for key concepts, domain terms, abbreviations.

### Risk Cards `.risk-card`

Variants: `.risk-card--high`, `.risk-card--medium`, `.risk-card--low`. Each contains `.risk-header` (badge + title) and `.risk-body` (description + mitigation).

### Open Questions `.question-list`

Unordered list with `?` indicator. Use for ambiguities the spec does not resolve.

### Directory Tree `.tree-view`

Use for file and directory structure displays. The `white-space: pre` rule preserves newlines and indentation — **do not** wrap content in `<pre>` (the class alone handles formatting).

```html
<div class="tree-view">
<span class="dir">project/</span>
├── <span class="dir">src/</span>
│   ├── index.ts          <span class="comment">← entry point</span>
│   └── <span class="dir">lib/</span>
│       └── utils.ts
└── package.json
</div>
```

- Wrap directory names in `<span class="dir">`
- Wrap annotations in `<span class="comment">`
- Place the opening tag and content flush-left (no leading whitespace before tree lines)

### Pyramid Chart `.pyramid`

Stacked horizontal bars for proportional data (e.g., test pyramid, cost breakdown).

```html
<div class="pyramid">
  <div class="pyramid-level">
    <div class="pyramid-bar" style="width:30%;background:#e03131;">E2E <span class="count">(5)</span></div>
    <div class="pyramid-detail">Description of this level</div>
  </div>
  <!-- wider bars toward the bottom -->
  <div class="pyramid-level">
    <div class="pyramid-bar" style="width:100%;background:#16a34a;">Unit <span class="count">(42)</span></div>
    <div class="pyramid-detail">Description of this level</div>
  </div>
</div>
```

- Set `width` (30%–100%) and `background` inline on `.pyramid-bar`
- The bar occupies a 60% column; the detail always has a 40% column beside it
- Widths are relative to the bar column, so proportions are preserved even at 100%

## Rules

- Copy the full `<style>` block from `template.html` into every generated file. Do not subset it.
- Replace all `{{PLACEHOLDER}}` tokens with real content derived from the source, escaping source-derived text by default.
- Use semantic HTML elements (`section`, `figure`, `figcaption`, `table`, `nav`, `code`).
- Number figures sequentially: Fig 1, Fig 2, ... (or 図1, 図2, ... for `--lang=ja`).
- Every diagram must have a `<figcaption>`.
- The traceability table in the Appendix must map each output section to its source section and indicate whether content was Preserved, Summarized, or Inferred.
- Mermaid CDN script goes at the bottom of `<body>` when Mermaid blocks are used. Note this external dependency in the footer or a comment. For fully self-contained/offline output, use inline SVG and omit the Mermaid CDN script.
