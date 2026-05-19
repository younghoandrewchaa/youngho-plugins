# Design: Learning Quiz — HTML Learning Points Output

**Date:** 2026-05-19
**Plugin:** `learning-quiz` (`plugins/learning-quiz-plugin`)
**Scope:** Phase 1 (learning points summary) only — Phase 2 (interactive quiz) unchanged

---

## Problem

The current skill presents learning points as plain markdown in the terminal. This is functional but misses an opportunity to support learning through visual hierarchy, colour, and diagrams.

## Goal

Generate Phase 1 learning points as a styled HTML file, open it automatically in the user's default browser, then continue the interactive quiz in the terminal as normal.

---

## Architecture

### File output

- Claude generates a **single self-contained HTML file**
- Written to `/tmp/learning-quiz.html` via the **Write tool** (fixed path; each invocation overwrites the previous session intentionally)
- Opened with `open /tmp/learning-quiz.html` via the **Bash tool** (macOS default browser)

### CDN dependencies (no installation required)

- **Mermaid.js** — renders diagrams from inline text definitions
- **highlight.js** — syntax-highlights code blocks in learning points

---

## HTML Structure

### Header band
- Dark navy gradient background
- Session title and current date

### Topic sections
- Each topic group is a labelled section
- Distinct left-border colour, cycling through a palette of 5 colours

### Learning point cards
- Numbered cards on a light background
- Title **bolded**, key terms highlighted in colour
- Code snippets in a monospace dark-background block (highlight.js)

### Diagrams
- Rendered inline after the learning points for a topic group, where a diagram genuinely aids understanding
- Clearly labelled
- No diagram is forced — if no topic benefits from one, none are added

---

## Diagram Strategy

Claude assesses each topic group and selects the appropriate Mermaid diagram type:

| Topic type | Mermaid type |
|---|---|
| Process / step sequence | `flowchart TD` |
| Component relationships / architecture | `graph LR` |
| Request/response or message flows | `sequenceDiagram` |
| Concept hierarchy or groupings | `mindmap` |

If no topic benefits from a diagram, the skill instructs Claude to omit them entirely.

---

## Phase Flow (updated)

1. **Extract learning points** — same logic as before
2. **Generate HTML** — Claude builds the full HTML string
3. **Write file** — Write tool → `/tmp/learning-quiz.html`
4. **Open browser** — Bash tool → `open /tmp/learning-quiz.html`
5. **Terminal confirmation** — `AskUserQuestion` (start quiz / remove some / add more) — unchanged
6. **Interactive quiz** — Phase 2 unchanged; stays entirely in the terminal

---

## SKILL.md Changes

The Phase 1 **Presentation** section is replaced with:

- Instructions to generate a complete HTML file (inline CSS, CDN scripts, topic sections, numbered cards, code blocks, diagrams)
- Instructions to use the Write tool to write to `/tmp/learning-quiz.html`
- Instructions to use the Bash tool to run `open /tmp/learning-quiz.html`
- A note that the terminal `AskUserQuestion` confirmation follows immediately after the browser opens

The quiz (Phase 2) and all other rules are unchanged.

---

## Out of Scope

- Quiz question rendering in HTML
- Quiz results summary in HTML
- Linux / Windows browser-open compatibility (macOS `open` only)
- Saving the HTML file to a permanent user-chosen location
