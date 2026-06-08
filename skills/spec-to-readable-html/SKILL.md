---
name: spec-to-readable-html
description: Transform Markdown or text specifications into human-readable HTML documents with summaries, reordered structure, visual explanations, diagrams, charts, and traceability to the source. Use when asked to convert a spec, requirements document, API spec, product spec, design doc, or technical document into readable HTML, especially when the user wants summarization, restructuring, diagrams, graphs, images, or visual aids rather than a literal Markdown-to-HTML conversion.
argument-hint: "[lang]"
arguments: lang
license: MIT
---

# Spec to Readable HTML

This skill turns specifications into polished, human-readable HTML reports. It is not a simple Markdown renderer. It should analyze the source, summarize where helpful, reorganize content for comprehension, and add visual aids when they clarify the document.

When invoked, `$lang` sets the output language (default: `ja`). If omitted, Japanese is used.

## When to Use This Skill

Use this skill when the user asks to:

- Convert a specification, requirements document, design doc, API spec, PRD, RFC, or technical document into HTML
- Make a Markdown spec easier for humans to read
- Add summaries, diagrams, charts, screenshots, icons, or visual explanations to a document
- Turn dense requirements into a readable web page or report
- Produce executive summaries, implementation overviews, API maps, workflows, architecture diagrams, or decision summaries from a spec

Do **not** treat the task as a literal Markdown-to-HTML conversion unless the user explicitly asks for a faithful conversion.

## Core Principles

1. **Improve comprehension, not just formatting**
   - Summarize long sections.
   - Group related information.
   - Surface key decisions, risks, dependencies, and open questions.

2. **Preserve traceability**
   - Do not silently change meaning.
   - Mark inferred content as `Inferred` or `Assumption`.
   - Prefer linking or referencing original section names where possible.

3. **Use visuals only when they help**
   - Add diagrams for flows, relationships, architecture, state transitions, timelines, or data models.
   - Add charts only when the source contains comparable quantities, priorities, timelines, statuses, or categories.
   - Do not invent quantitative data.

4. **Make the output portable**
   - Prefer a single portable HTML file with embedded CSS.
   - The base template uses the Mermaid CDN by default for Mermaid diagrams.
   - Document the Mermaid CDN dependency in the footer or a comment when Mermaid is used.
   - If the user asks for offline, no-network, or fully self-contained output, replace Mermaid diagrams with inline SVG instead of loading the CDN.
   - Avoid additional external dependencies unless the user allows them.

## Input Handling

Accept any of these inputs:

- Markdown file
- Plain text specification
- Multiple related spec files
- API schema / OpenAPI fragments
- README-style product or technical notes
- Existing HTML that needs improvement

Before producing HTML, identify:

- Document type: PRD, API spec, technical spec, design doc, operations runbook, etc.
- Target audience: business, engineering, QA, operations, mixed audience
- Main entities: users, systems, APIs, screens, jobs, data models
- Main processes: workflows, sequences, state transitions
- Ambiguities, missing decisions, and risks

If the target audience or desired level of detail is unclear, make a reasonable default:

- Audience: mixed product + engineering
- Detail: readable but implementation-aware
- Output: single portable HTML document with embedded CSS; Mermaid diagrams may use the CDN unless offline/no-network output is requested

## Recommended Workflow

1. **Read and map the source**
   - Extract title, purpose, scope, background, requirements, flows, APIs, data, constraints, risks, and open questions.

2. **Create a reader-friendly structure**
   - Executive summary
   - What this document covers
   - Key concepts / glossary
   - Main workflows or user journeys
   - Functional requirements
   - Non-functional requirements
   - System / API / data overview
   - Visual diagrams
   - Risks, assumptions, open questions
   - Appendix: source-aligned details

3. **Summarize selectively**
   - Keep exact details for requirements, API contracts, constraints, and acceptance criteria.
   - Summarize background, motivation, repeated explanations, and long prose.
   - Use callouts for important notes.

4. **Choose visual aids**
   - Use the visual decision guide below.
   - Create diagrams as inline SVG, Mermaid source, or image files depending on the environment and user preference.
   - Always add captions and alt text.

5. **Generate polished HTML**
   - Use semantic HTML: `section`, `article`, `nav`, `table`, `figure`, `figcaption`.
   - Include a sticky or top table of contents for long docs.
   - Use readable typography, spacing, and contrast.
   - Include print-friendly CSS when useful.

6. **Validate output**
   - Check that source meaning is preserved.
   - Ensure assumptions are labeled.
   - Ensure visuals match the source.
   - Ensure the HTML opens standalone unless dependencies were explicitly chosen.

## Visual Decision Guide

Choose visuals based on the content:

| Source content | Best visual |
| --- | --- |
| Step-by-step business or user process | Flowchart |
| API calls between systems | Sequence diagram |
| System components and dependencies | Architecture diagram |
| Status lifecycle | State diagram |
| Tables with entities and relationships | ER diagram / relationship map |
| Milestones or phased rollout | Timeline |
| Priority, status, category counts | Bar chart / stacked chart |
| Decision alternatives | Comparison matrix |
| Screen transitions | User journey / sitemap |
| Long section structure | Summary cards |

Preferred formats:

1. **Inline SVG** for standalone visual diagrams and charts, especially when fully self-contained or offline output is required.
2. **Mermaid** for architecture, flowchart, sequence, state, and ER diagrams when source code readability matters. In the base template, Mermaid uses a CDN dependency.
3. **HTML/CSS cards and tables** for summaries and comparisons.
4. **Raster images** only if image generation or screenshots are explicitly requested or available.

If using Mermaid, include the Mermaid source in the HTML or appendix so it can be edited later, and document the CDN dependency unless Mermaid has been pre-rendered to inline SVG.

## HTML Output Requirements

Use `references/template.html` as the base HTML template and `references/html-output-template.md` as the component guide when generating a complete HTML file. Copy the full `<style>` block and section structure from the template, then replace `{{PLACEHOLDER}}` tokens with content derived from the source.

The HTML should usually include:

- Document title and short subtitle
- Generated date or source version if known
- Executive summary
- Table of contents
- Summary cards for key points
- Main sections with concise headings
- Tables for requirements, APIs, data fields, decisions, risks, and open questions
- Visual diagrams with captions
- Source traceability notes
- Print-friendly styling

For dense technical specs, include badges such as:

- `Must`, `Should`, `Could`
- `Confirmed`, `Inferred`, `Assumption`, `Open Question`
- `High Risk`, `Medium Risk`, `Low Risk`

## HTML Safety and Escaping

- Treat source-derived prose, table cell content, captions, metadata, and placeholder replacements as untrusted text by default.
- HTML-escape `&`, `<`, `>`, `"`, and `'` before inserting source text into text nodes or attributes, unless deliberately emitting generated, safe markup.
- Do not pass raw source HTML through by default. If limited HTML is required, use a small allowlist of semantic tags and safe attributes.
- Keep code blocks and source excerpts as escaped text inside `<pre><code>` or equivalent containers.
- When source content is used inside Mermaid labels, quote or escape labels so user-provided HTML, event handlers, and `javascript:` URLs are not emitted into the page.
- Inline SVG may be generated by the agent, but SVG or HTML copied from the source must be sanitized or replaced with a safe generated diagram.

## Rules for Summarization and Inference

- Never remove contractual or normative requirements without indicating they were summarized.
- Preserve keywords such as MUST, SHOULD, SHALL, required, optional, deprecated.
- Keep IDs, endpoint paths, field names, status names, enum values, error codes, and limits exact.
- If a diagram requires interpretation, label it as an inferred view.
- If the source is ambiguous, create an `Open Questions` section instead of guessing.

## Language

Default output language is Japanese (`<html lang="ja">`). If the user requests a different language, use that language for all headings, badges, labels, and body text. Keep technical terms (API names, field names, paths, code identifiers) in their original language regardless.

## Quality Checklist

Before finalizing, verify:

- [ ] The output is more readable than the source, not merely converted.
- [ ] Important requirements and constraints are preserved.
- [ ] Summaries do not change meaning.
- [ ] Visuals are accurate and useful.
- [ ] Inferred content and assumptions are labeled.
- [ ] Tables are used for structured details.
- [ ] The HTML has a table of contents.
- [ ] The file is standalone, or dependencies such as Mermaid CDN are documented.
- [ ] Accessibility basics are covered: headings, contrast, alt text/captions.
