---
name: learning-quiz
description: Pre-test the user on a topic BEFORE they study it, to prime their reading and surface knowledge gaps. Generates multiple-choice questions, teaches through immediate feedback, and produces a learning note at the end. Use when the user wants to prime their reading or review a session. Triggers on pre-test, pretest, prime me, prime my reading, quiz before I read, prepare to learn, generate questions before I study, learning review, quiz me, test my understanding, what did I learn, session review, knowledge check.
---

# Learning Quiz

A **pre-testing** tool. Quiz the user *before* they study a topic to prime their reading,
then teach through the answers. The quiz is not a grade — it is a learning device.

## Why this works

This implements the **Pre-testing strategy**, a scientifically validated approach. Quizzing
before studying works even when the user guesses every answer wrong:

- **Pre-testing Effect** — attempting questions on unfamiliar material activates prior
  knowledge and primes attention, creating cognitive "hooks" the new information sticks to
  when the user later reads it.
- **Generation Effect** — actively producing an answer (even a blind guess) embeds the
  concept far deeper than passive reading ever could.
- **Hypercorrection Effect** — a confident wrong guess makes the correction the most
  memorable of all once the user sees the right answer.
- **Feedback immediacy** — the priming relies on getting the correct answer *right after*
  the guess, so feedback here is always immediate.

Because the user has no prior knowledge of the material, **every question is multiple
choice** and the goal of feedback is to *teach*, not to score.

## Input Modes

This skill supports two modes based on whether the user provides additional text:

- **With content** (e.g. `/learning-quiz <pasted text, URL, or topic the user is about to study>`):
  The primary pre-test flow. Read, fetch, or interpret the provided content first, then
  generate priming questions from it to focus the user's upcoming reading.
- **No additional text** (e.g. just `/learning-quiz`): Scan the current conversation context
  and generate priming questions from the topics discussed, still framed as teaching through
  feedback.

## Phase 1: Confirm Scope

Based on the input mode, scan either the provided content or the conversation context and
identify the topics worth priming. Topics may cover:

- **Technical concepts** — new APIs, language features, framework patterns
- **Architectural decisions** — why a particular approach was chosen over alternatives
- **Gotchas & pitfalls** — surprising behaviours, edge cases, bugs
- **Tools & workflows** — new commands, tools, configurations
- **Domain knowledge** — business logic, conventions, or context

### Presentation

Present the **topic headings only** — a short numbered list of the areas you will quiz on.

```
## I'll pre-test you on these topics

1. Topic A
2. Topic B
3. Topic C
```

**CRITICAL — do NOT reveal answers, explanations, or the learning points themselves in
Phase 1.** Listing the answers upfront spoils the priming. Show only the scope.

After presenting, use `AskUserQuestion` to let the user choose how to proceed:

- Option 1: **Start the pre-test** — proceed with all listed topics
- Option 2: **Remove some** — "Tell me which numbers to remove (e.g. 3, 5)"
- Option 3: **Add more** — "Tell me what else to cover"

The user can also type a custom response via "Other". If they choose to remove or add, apply
the changes and then proceed straight to the quiz — don't ask again.

**Do NOT proceed to Phase 2 until the user confirms the scope.**

## Phase 2: Interactive Pre-Test

Generate 1-2 questions per confirmed topic. Aim for a total of 5-15 questions depending on
how many topics were confirmed.

Before the first question, reassure the user: **guessing is the point.** A low score is
expected and useful — the harder their brain works to figure out each question, the wider it
opens the door for the real information to land when they read. Tell them to embrace the
struggle.

### Question Format

**All questions are multiple choice.** Use the `AskUserQuestion` tool to present them.
Include 3-4 options with one correct answer. Make distractors plausible — they should reflect
common misconceptions, not obviously wrong answers, so that picking one becomes a teaching
moment.

**CRITICAL — Randomise the correct answer position.** Do NOT always place the correct answer
as the first option. For each question, rotate the position: if the correct answer was 1st on
question 1, put it 3rd on question 2, then 2nd on question 3, then 4th on question 4, and so
on. Never place the correct answer in the same position for two consecutive questions. Do NOT
use Bash or shell commands to generate randomness — just cycle through positions yourself.

Example question stems:
- "Which method/flag/option does X use?"
- "What is the default value of Y?"
- "Which of these is true about Z?"

### Quiz Flow

1. Present one question at a time
2. Wait for the user's answer
3. Give immediate, **teaching** feedback on every answer:
   - **Correct:** confirm it, then add the *why* — the reasoning behind the correct answer.
   - **Incorrect:** give the correct answer, explain *why* it's correct, and briefly note
     why their pick was a plausible misconception.
   The feedback is the teaching moment — this is where the priming happens.
4. Move to the next question
5. After all questions, show the reading-focus guide and the learning note

### Reading-Focus Guide

Instead of a scoreboard, frame the result as preparation for reading:

```
## Read with these in mind

- Topic B — you weren't sure about [specific concept]; watch for it
- Topic C — the answer about [X] may surprise you; look out for the detail
```

List the topics and answers that surprised the user or they missed, framed as flags for
hyper-focused reading. Keep any tally light and de-emphasised — the deliverable is a focus
list, not a grade.

### Learning Note

After the focus guide, write a persistent **Learning Note** the user can keep or paste into
their notes. The note covers the **whole content**, not just the questions — it is a complete
study summary, with the missed areas deepened.

Build it in two passes:

1. **Cover everything first.** For **every confirmed topic**, write a concise summary of the
   key points worth knowing — the core concepts, defaults, gotchas, and reasoning from the
   source material. Do this for all topics, regardless of how the user answered. This is the
   baseline study note.
2. **Then deepen where the user got it wrong.** For each topic where the user missed a
   question, add an extra **"Watch out"** sub-section going into more detail: the question and
   correct answer, *why* it's correct, and why their pick was a plausible misconception. This
   is where the priming pays off, so make these the richest parts of the note.

Format it as a clean, self-contained markdown block grouped by topic, so it reads well out of
context:

```
## Learning Note

### Topic A
[Concise summary of the key points for this topic — concepts, defaults, gotchas.]

### Topic B
[Concise summary of the key points for this topic.]

**Watch out — you missed this:**
- **Q: [question]**
  Correct answer: [answer]
  Why: [the explanation given during feedback, including why the chosen distractor was a
  common misconception]

### Topic C
[Concise summary of the key points for this topic.]

**Watch out — you missed this:**
- **Q: [question]**
  Correct answer: [answer]
  Why: [explanation]
```

This is the take-away artefact of the priming session — a full study summary of the material,
with the gaps surfaced and explained in extra depth, ready to reinforce when the user reads.
If the user got everything right, still write the full summary and say no areas need the extra
"Watch out" detail.

## Rules

- **Do NOT skip Phase 1.** Always confirm the scope first — but never reveal answers or
  explanations in Phase 1.
- **All questions are multiple choice.** No open-ended questions — the user has no prior
  knowledge to answer them.
- **One question at a time.** Never batch multiple questions.
- **Feedback is always immediate** and exists to teach, not to grade.
- **Embrace the struggle.** Reassure the user that guessing and low scores are expected and
  useful; never make them feel bad about wrong answers.
- **Be encouraging, not patronising.** Treat the user as a peer preparing to learn.
- **Keep feedback concise.** One or two sentences per answer, not a lecture.
- **Always end with the Learning Note.** It must summarise the *whole* content for every
  topic first, then add deeper "Watch out" detail for the areas the user got wrong.
- **If the source has no meaningful topics** (e.g. conversation was just a quick file read,
  or the provided content is too brief), say so honestly instead of fabricating trivial
  questions.
- **When content is provided**, focus exclusively on that content — do not mix in unrelated
  conversation context.
- **When content is a URL**, fetch and read it before generating questions.
