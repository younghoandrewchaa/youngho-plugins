# Learning Quiz HTML Output Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the Phase 1 markdown learning-points presentation in the `learning-quiz` skill with a styled HTML file that is auto-opened in the default browser.

**Architecture:** The SKILL.md Presentation section is updated to instruct Claude to generate a self-contained HTML file (inline CSS, Mermaid.js and highlight.js via CDN), write it to `/tmp/learning-quiz.html` using the Write tool, and open it with `open` via the Bash tool. Phase 2 (interactive quiz) is unchanged.

**Tech Stack:** HTML/CSS, Mermaid.js (CDN), highlight.js (CDN), macOS `open` command

---

### Task 1: Replace Phase 1 Presentation section in SKILL.md

**Files:**
- Modify: `plugins/learning-quiz-plugin/skills/learning-quiz/SKILL.md`

- [ ] **Step 1: Open SKILL.md and locate the Presentation section**

  The section to replace starts at `### Presentation` and ends just before `## Phase 2: Interactive Quiz`. The current content is:

  ```markdown
  ### Presentation

  Group learning points by topic. Present them as a numbered list with brief explanations:

  ```
  ## Learning Points from This Session

  ### Topic A
  1. **Point title** — one-sentence explanation
  2. **Point title** — one-sentence explanation

  ### Topic B
  3. **Point title** — one-sentence explanation
  ```

  After presenting, use `AskUserQuestion` to let the user choose how to proceed:

  - Option 1: **Start the quiz** — proceed with all listed learning points
  - Option 2: **Remove some** — "Tell me which numbers to remove (e.g. 3, 5)"
  - Option 3: **Add more** — "Tell me what I missed"

  The user can also type a custom response via "Other". If they choose to remove or add, apply the changes and then proceed straight to the quiz — don't ask again.

  **Do NOT proceed to Phase 2 until the user confirms the learning points.**
  ```

- [ ] **Step 2: Replace the Presentation section with the HTML generation instructions**

  Replace the entire block above with the following content (everything between the `---` markers, excluding the markers themselves):

  ---
  ### Presentation

  Generate a styled HTML page listing the learning points and open it in the user's browser. Follow these four steps exactly — do not skip any.

  **Step 1 — Build the HTML**

  Generate a complete, self-contained HTML file using the template below. Substitute real values for all `{PLACEHOLDER}` tokens:

  - `{DATE}` — today's date (e.g. "19 May 2026")
  - Cycle the topic colour through this palette in order, restarting if there are more than 5 topics: `#4f86c6`, `#f0a500`, `#2ecc71`, `#e74c3c`, `#9b59b6`
  - Number learning points globally (1, 2, 3 … across all topics, not restarting per topic)
  - Bold key terms and jargon inline with `<strong>` or `<span style="color:{TOPIC_COLOUR};font-weight:600">`
  - For each topic, assess whether a diagram would genuinely aid understanding. If yes, include one `.diagram` block using Mermaid syntax. If no topic benefits from a diagram, omit all `.diagram` blocks.

  Diagram type selection guide:

  | Topic type | Mermaid diagram type |
  |---|---|
  | Process / step sequence | `flowchart TD` |
  | Component relationships / architecture | `graph LR` |
  | Request / response flows | `sequenceDiagram` |
  | Concept hierarchy or groupings | `mindmap` |

  **HTML template** (fill in all `{PLACEHOLDER}` tokens with real content before writing):

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Learning Points — {DATE}</title>
    <script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/github-dark.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>
    <script>mermaid.initialize({startOnLoad:true,theme:'base'});document.addEventListener('DOMContentLoaded',()=>hljs.highlightAll());</script>
    <style>
      *{box-sizing:border-box;margin:0;padding:0}
      body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',system-ui,sans-serif;background:#f0f2f5;color:#1a1a2e;line-height:1.6}
      header{background:linear-gradient(135deg,#1a1a2e 0%,#16213e 60%,#0f3460 100%);color:#fff;padding:2.5rem 2rem}
      header h1{font-size:2rem;font-weight:700;margin-bottom:.25rem}
      header p{opacity:.7;font-size:.9rem}
      main{max-width:860px;margin:2rem auto;padding:0 1.5rem}
      .topic{margin-bottom:2.5rem}
      .topic-header{font-size:1.2rem;font-weight:700;margin-bottom:1rem;padding-bottom:.4rem;border-bottom:2px solid}
      .points{display:flex;flex-direction:column;gap:.75rem}
      .point{background:#fff;border-radius:10px;padding:1rem 1.25rem;box-shadow:0 1px 3px rgba(0,0,0,.08);display:flex;gap:1rem;border-left:4px solid}
      .point-num{font-size:1.1rem;font-weight:700;min-width:1.5rem}
      .point-content{}
      .point-title{font-weight:700;display:block;margin-bottom:.2rem}
      .point-body{color:#555;font-size:.95rem}
      .diagram{background:#fff;border-radius:10px;padding:1.5rem;margin-top:1rem;box-shadow:0 1px 3px rgba(0,0,0,.08)}
      .diagram-label{font-size:.75rem;font-weight:600;text-transform:uppercase;letter-spacing:.06em;color:#999;margin-bottom:.75rem}
      pre code.hljs{border-radius:8px;font-size:.88rem}
    </style>
  </head>
  <body>
  <header>
    <h1>Learning Points</h1>
    <p>{DATE}</p>
  </header>
  <main>
    <!-- Repeat this <section> block for each topic. Assign next palette colour to {TOPIC_COLOUR}. -->
    <section class="topic">
      <h2 class="topic-header" style="color:{TOPIC_COLOUR};border-bottom-color:{TOPIC_COLOUR}">{TOPIC_NAME}</h2>
      <div class="points">
        <!-- Repeat this .point div for each learning point in the topic. -->
        <div class="point" style="border-left-color:{TOPIC_COLOUR}">
          <span class="point-num" style="color:{TOPIC_COLOUR}">{N}</span>
          <div class="point-content">
            <span class="point-title">{POINT_TITLE}</span>
            <span class="point-body">{EXPLANATION — use <strong> for key terms, coloured spans for jargon}</span>
          </div>
        </div>
      </div>
      <!-- Include the .diagram block ONLY when a diagram genuinely helps; omit otherwise. -->
      <div class="diagram">
        <div class="diagram-label">{DIAGRAM_LABEL — e.g. "Request lifecycle"}</div>
        <div class="mermaid">
  {MERMAID_DIAGRAM_CODE}
        </div>
      </div>
    </section>
  </main>
  </body>
  </html>
  ```

  **Step 2 — Write the file**

  Use the Write tool to write the completed HTML to `/tmp/learning-quiz.html`.

  **Step 3 — Open in browser**

  Use the Bash tool to run:

  ```bash
  open /tmp/learning-quiz.html
  ```

  **Step 4 — Confirm with the user**

  After the browser opens, use `AskUserQuestion` to let the user choose how to proceed:

  - Option 1: **Start the quiz** — proceed with all listed learning points
  - Option 2: **Remove some** — "Tell me which numbers to remove (e.g. 3, 5)"
  - Option 3: **Add more** — "Tell me what I missed"

  The user can also type a custom response via "Other". If they choose to remove or add, apply the changes and then proceed straight to the quiz — don't ask again.

  **Do NOT proceed to Phase 2 until the user confirms the learning points.**

  ---

- [ ] **Step 3: Verify the edit**

  Read back `plugins/learning-quiz-plugin/skills/learning-quiz/SKILL.md` and confirm:
  - The old markdown code block presenting learning points as `###`/numbered list is gone
  - The new four-step Presentation section (Build HTML → Write → Open → Confirm) is present
  - `## Phase 2: Interactive Quiz` follows immediately after and is unchanged
  - The `## Rules` section at the bottom is unchanged

---

### Task 2: Bump the plugin version

**Files:**
- Modify: `plugins/learning-quiz-plugin/.claude-plugin/plugin.json`

- [ ] **Step 1: Update the version field**

  Change `"version": "1.0.5"` to `"version": "1.0.6"`.

  The full updated file should read:

  ```json
  {
    "name": "learning-quiz",
    "description": "Review the current conversation, summarise learning points, and quiz the user with a mix of multiple choice and open-ended questions to verify understanding",
    "version": "1.0.6",
    "author": {
      "name": "Youngho Chaa"
    },
    "homepage": "https://github.com/younghoandrewchaa/yh-plugins",
    "repository": "https://github.com/younghoandrewchaa/yh-plugins",
    "license": "MIT",
    "keywords": [
      "learning",
      "quiz",
      "review",
      "knowledge-check",
      "session-review"
    ]
  }
  ```

- [ ] **Step 2: Verify the edit**

  Confirm `"version"` reads `"1.0.6"` and no other fields were changed.
