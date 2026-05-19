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
