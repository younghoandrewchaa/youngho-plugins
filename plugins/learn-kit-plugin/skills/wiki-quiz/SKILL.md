---
name: wiki-quiz
description: Quiz the user on the contents of their personal wiki to help them remember and deepen what they have already learned. Generates 10 multiple-choice recall questions sampled across the whole wiki, teaches through immediate feedback, and produces a learning note. Use when the user wants to review or revise their wiki. Triggers on quiz me on my wiki, test me on my wiki, wiki quiz, review my wiki, revise my wiki, help me remember my wiki, recall practice, /learn-kit:wiki-quiz.
---

# Wiki Quiz

A **retrieval-practice** tool. Quiz the user on material *already in their wiki* to fight
forgetting and deepen understanding. The quiz is not a grade — it is a memory device.

**Announce at start:** "Opening your wiki to build a recall quiz..."

## Why this works

This implements **active recall** over material the user has already studied — the
post-study counterpart to the pre-testing in `learning-quiz`. Where pre-testing primes
*unfamiliar* material, this skill strengthens the memory of material the user has *already*
read and filed away:

- **Testing Effect** — actively retrieving a fact strengthens the memory far more than
  re-reading it. Pulling the answer out of your own head is what makes it stick.
- **Active Recall** — the effort of recalling, even when it's hard or you get it wrong,
  is precisely what consolidates the memory.
- **Spaced Retrieval** — revisiting wiki content after time has passed interrupts the
  forgetting curve and resets it.
- **Desirable Difficulty** — a question you have to struggle with reinforces the knowledge
  more than one you answer instantly. Struggling to retrieve is a feature, not a failure.
- **Feedback as deepening** — because the user has seen this material before, the feedback
  is there to *re-explain and connect*, turning a half-remembered fact into a solid one.

Because this is review of known material, **every question is multiple choice** and the goal
of feedback is to *reinforce and deepen*, not to score.

## Step 1 — Discover the wiki path

This skill quizzes the same wiki that `wikify` maintains.

Check memory for a stored value under the key `wikify_wiki_path`.

**If found:** read `<path>/CLAUDE.md` to confirm the wiki is intact. If it's missing, the
wiki isn't set up — tell the user to run `/learn-kit:wikify` first to create and populate it,
then stop.

**If not found:** ask the user exactly this:

> "Where is your wiki directory? Enter an absolute path (e.g. ~/Documents/wiki)."

Save the entered path to memory as `wikify_wiki_path`. Then read `<path>/CLAUDE.md` — if it's
missing, tell the user the wiki isn't set up yet and to run `/learn-kit:wikify` first, then
stop. **Never fabricate questions from an empty or missing wiki.**

## Step 2 — Gather content

1. Read `<wiki_path>/index.md` for the full inventory of pages.
2. Read a **balanced spread** of pages across `wiki/sources/`, `wiki/pages/`, and
   `wiki/syntheses/` so the quiz covers the wiki broadly — not a single page. Favour the
   substantive, knowledge-rich pages; aim to touch a range of topics.

There is **no scope-selection step** — the quiz always samples the whole wiki for broad
coverage. Briefly tell the user which areas the quiz will span (a short list of the topics or
pages you'll draw from), then begin straight away.

If the wiki has too little content to support 10 good questions, say so honestly, generate as
many solid questions as the material allows, and don't pad with trivia.

## Step 3 — Interactive quiz (10 questions)

Generate **exactly 10 questions**, sampled across the topics gathered in Step 2.

Before the first question, set the tone: this is **recall practice**, not an exam. Remind the
user that having to think hard to remember something is exactly what makes it stick — a wrong
or shaky answer just marks a spot worth strengthening.

### Question Format

**All questions are multiple choice.** Use the `AskUserQuestion` tool to present them.
Include 3-4 options with one correct answer. Make distractors plausible — they should reflect
common confusions or near-miss details from the wiki, not obviously wrong answers, so that
picking one becomes a teaching moment.

**CRITICAL — Randomise the correct answer position.** Do NOT always place the correct answer
as the first option. For each question, rotate the position: if the correct answer was 1st on
question 1, put it 3rd on question 2, then 2nd on question 3, then 4th on question 4, and so
on. Never place the correct answer in the same position for two consecutive questions. Do NOT
use Bash or shell commands to generate randomness — just cycle through positions yourself.

Example question stems:
- "According to your wiki, which X does Y?"
- "What did the source on Z conclude about W?"
- "How does concept A relate to concept B in your notes?"

### Quiz Flow

1. Present one question at a time.
2. Wait for the user's answer.
3. Give immediate, **reinforcing** feedback on every answer:
   - **Correct:** confirm it, then add the *why* — restate the reasoning so the recall lands
     deeper, and connect it to related wiki pages where useful.
   - **Incorrect:** give the correct answer, re-explain *why* it's correct from the wiki, and
     briefly note why their pick was a plausible confusion.
   The feedback is the deepening moment — this is where review pays off.
4. Move to the next question.
5. After all 10 questions, show the revision guide and the learning note.

## Step 4 — Revision guide and Learning Note

### Revision Guide

Instead of a scoreboard, frame the result as a revision plan:

```
## Worth revisiting

- Topic B — you were shaky on [specific concept]; reread `wiki/pages/<slug>.md`
- Topic C — the detail about [X] tripped you up; it's in `wiki/sources/<slug>.md`
```

List the topics the user missed or hesitated on, framed as flags for targeted revision, with
a pointer back to the relevant wiki page so they can reread it. Keep any tally light and
de-emphasised — the deliverable is a revision list, not a grade.

### Learning Note

After the revision guide, write a persistent **Learning Note** the user can keep. The note
covers the **material quizzed**, not just the questions — a refreshed study summary, with the
missed areas deepened.

Build it in two passes:

1. **Refresh everything first.** For each topic the quiz drew on, write a concise summary of
   the key points worth remembering — the core facts, conclusions, and connections from the
   wiki pages. Do this regardless of how the user answered. This is the revision baseline.
2. **Then deepen where the user got it wrong.** For each topic where the user missed a
   question, add an extra **"Watch out"** sub-section: the question and correct answer, *why*
   it's correct, why their pick was a plausible confusion, and a link to the wiki page to
   reread.

Format it as a clean, self-contained markdown block grouped by topic, so it reads well out of
context:

```
## Learning Note

### Topic A
[Concise refresher of the key points for this topic — facts, conclusions, connections.]

### Topic B
[Concise refresher of the key points for this topic.]

**Watch out — you missed this:**
- **Q: [question]**
  Correct answer: [answer]
  Why: [the explanation given during feedback, including why the chosen distractor was a
  plausible confusion]
  Reread: `wiki/pages/<slug>.md`

### Topic C
[Concise refresher of the key points for this topic.]
```

This is the take-away artefact of the review session — a refreshed summary of the material,
with the weak spots surfaced and pointed back at the wiki for revision. If the user got
everything right, still write the full refresher and say no areas need the extra "Watch out"
detail.

## Rules

- **Exactly 10 questions** — unless the wiki has too little content, in which case generate
  fewer and say so rather than padding with trivia.
- **All questions are multiple choice.** Use `AskUserQuestion`.
- **One question at a time.** Never batch multiple questions.
- **Feedback is always immediate** and exists to reinforce and deepen, not to grade.
- **Every question must come from the wiki.** Do not quiz on facts that aren't in the user's
  wiki pages — this is review of *their* material.
- **Sample broadly.** Spread the 10 questions across the wiki; don't draw them all from one
  page.
- **Read-only.** Never write to the wiki — not to `raw/`, `wiki/`, `index.md`, or `log.md`.
- **Embrace the struggle.** Reassure the user that effortful recall and the odd wrong answer
  are how memory strengthens; never make them feel bad about a miss.
- **Be encouraging, not patronising.** Treat the user as a peer revising their own notes.
- **Keep feedback concise.** One or two sentences per answer, not a lecture.
- **Always end with the Learning Note**, pointing back to the relevant wiki pages.

## Example Trigger Phrases

> "/learn-kit:wiki-quiz"
> "Quiz me on my wiki"
> "Help me remember what's in my wiki"
> "Review my wiki with me"
