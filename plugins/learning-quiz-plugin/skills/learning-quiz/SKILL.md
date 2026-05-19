---
name: learning-quiz
description: Review the current conversation, summarise learning points, and quiz the user to verify understanding. Use when the user wants to review or test what they've learned in this session. Triggers on learning review, quiz me, test my understanding, what did I learn, session review, knowledge check.
---

# Learning Quiz

Review content to extract learning points, then quiz the user to deepen and verify understanding.

## Input Modes

This skill supports two modes based on whether the user provides additional text:

- **No additional text** (e.g. just `/learning-quiz`): Scan the current conversation context to extract learning points from what was discussed in this session.
- **With content** (e.g. `/learning-quiz <pasted text, URL, or topic>`): Use the provided content as the source material instead of the conversation context. Read, fetch, or interpret the provided content first, then extract learning points from it.

## Phase 1: Extract & Confirm Learning Points

Based on the input mode, scan either the conversation context or the provided content and extract learning points. A learning point is anything the user likely learned or should retain:

- **Technical concepts** — new APIs, language features, framework patterns
- **Architectural decisions** — why a particular approach was chosen over alternatives
- **Gotchas & pitfalls** — surprising behaviours, edge cases, bugs encountered
- **Tools & workflows** — new commands, tools, configurations discovered
- **Domain knowledge** — business logic, conventions, or context learned

### Presentation

Generate a styled HTML page listing the learning points and open it in the user's browser. Follow these three steps exactly — do not skip any.

**Step 1 — Generate learning points as markdown**

Compose the extracted learning points as a markdown document. This is an internal intermediate step — do not write this to a file. Use this structure:

```markdown
# Learning Points — {DATE}

## {TOPIC_NAME}

**{N}. {POINT_TITLE}**
{POINT_BODY}

**{N+1}. {POINT_TITLE}**
{POINT_BODY}

## {NEXT_TOPIC_NAME}

**{N+2}. {POINT_TITLE}**
{POINT_BODY}
```

Number learning points globally (1, 2, 3 … across all topics, not restarting per topic). Use `**bold**` for key terms within the body text. This markdown is the content source for the HTML in the next step.

**Step 2 — Build HTML from the markdown and write to file**

Convert the markdown learning points into a complete, self-contained HTML file using the template below. Substitute real values for all `{PLACEHOLDER}` tokens:

- `{DATE}` — today's date (e.g. "19 May 2026")
- `{TOPIC_COLOUR}` — next colour from the palette; assign in order and restart from `#4f86c6` on the sixth topic: `#4f86c6`, `#f0a500`, `#2ecc71`, `#e74c3c`, `#9b59b6`
- `{TOPIC_NAME}` — the topic heading text
- `{N}` — global learning point number
- `{POINT_TITLE}` — the learning point title
- `{POINT_BODY}` — the explanation; use `<strong>` for key terms, and `<span style="color:HEX;font-weight:600">` (substituting the same hex colour already assigned to this topic) for jargon

**HTML template** (fill in all `{PLACEHOLDER}` tokens with real content before writing):

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Learning Points — {DATE}</title>
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
    .point-content{flex:1;min-width:0}
    .point-title{font-weight:700;display:block;margin-bottom:.2rem}
    .point-body{color:#555;font-size:.95rem}
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
          <span class="point-body">{POINT_BODY}</span>
        </div>
      </div>
    </div>
  </section>
</main>
</body>
</html>
```

Use the Write tool to write the completed HTML to `/tmp/learning-quiz.html`.

**Step 3 — Open in browser and confirm**

Use the Bash tool to run (macOS):

```bash
open /tmp/learning-quiz.html
```

Then use `AskUserQuestion` to let the user choose how to proceed:

- Option 1: **Start the quiz** — proceed with all listed learning points
- Option 2: **Remove some** — "Tell me which numbers to remove (e.g. 3, 5)"
- Option 3: **Add more** — "Tell me what I missed"

The user can also type a custom response via "Other". If they choose to remove or add, apply the changes and then proceed straight to the quiz — don't ask again.

**Do NOT proceed to Phase 2 until the user confirms the learning points.**

## Phase 2: Interactive Quiz

Generate 1-2 questions per confirmed learning point. Aim for a total of 5-15 questions depending on how many learning points were confirmed.

### Question Format Mix

Use a mix of question types to test different levels of understanding:

**Multiple Choice (for factual recall):**
Use the `AskUserQuestion` tool to present these. Include 3-4 options with one correct answer. Make distractors plausible — they should reflect common misconceptions, not obviously wrong answers.

**CRITICAL — Randomise the correct answer position.** Do NOT always place the correct answer as the first option. For each question, rotate the position: if the correct answer was 1st on question 1, put it 3rd on question 2, then 2nd on question 3, then 4th on question 4, and so on. Never place the correct answer in the same position for two consecutive questions. Do NOT use Bash or shell commands to generate randomness — just cycle through positions yourself.

Example triggers for multiple choice:
- "Which method/flag/option does X use?"
- "What is the default value of Y?"
- "Which of these is true about Z?"

**Open-ended (for conceptual understanding):**
Present these as plain text questions. Ask the user to explain in their own words. After they respond, evaluate their answer: acknowledge what's correct, gently correct any misconceptions, and fill in anything they missed.

Example triggers for open-ended:
- "Why was approach X chosen over Y?"
- "Explain how Z works in your own words"
- "What would happen if you did X instead?"

### Quiz Flow

1. Present one question at a time
2. Wait for the user's answer
3. Give immediate feedback:
   - **Correct:** brief confirmation + optional extra context
   - **Partially correct:** acknowledge what's right, clarify what's missing
   - **Incorrect:** give the correct answer with a brief explanation of why
4. Move to the next question
5. After all questions, show the final score

### Final Score

Present a summary:

```
## Quiz Results: X/Y correct

### Strong areas
- Topic A — got all questions right

### Areas to revisit
- Topic B — missed the question about [specific concept]
  Brief reminder: [one-sentence refresher]
```

## Rules

- **Do NOT skip Phase 1.** Always show learning points and get confirmation first.
- **One question at a time.** Never batch multiple questions.
- **Be encouraging, not patronising.** Treat the user as a peer checking their understanding.
- **Keep feedback concise.** One or two sentences per answer, not a lecture.
- **If the source has no meaningful learning points** (e.g. conversation was just a quick file read, or the provided content is too brief), say so honestly instead of fabricating trivial questions.
- **When content is provided**, focus exclusively on that content for learning points — do not mix in unrelated conversation context.
- **When content is a URL**, fetch and read it before extracting learning points.
