---
name: reflect
description: Reflect on your most recent plan — review what you set out to do, capture how it went, what you learned, and whether the result matched expectations. Polishes reflections into natural British English. Triggers on reflect, end of day review, daily review, weekly review, monthly review, how did it go, what did I learn.
---

# My Day — Reflect

Review what you planned and reflect on how it went, with your words polished into natural British English.

**Announce at start:** "Using my-day:reflect to review your goals."

## Step 0: Resolve Storage Folder

Before doing anything else, determine where plan files are stored:

1. Check Claude memory for a saved `my-day-storage-path` preference
2. If found, verify the folder exists using Bash (`ls`). If it exists, proceed
3. If not found or the folder no longer exists, ask the user:

Use `AskUserQuestion`:
- Question: "Where are your plan files saved?"
- Options: `~/plans/`, `~/Documents/plans/`, plus "Other" for a custom path
- Save the chosen path to Claude memory (type: `user`, name: `my-day-storage-path`) so it persists

---

## Phase 1: Find the Plan

Read the most recent plan file from the storage folder (sort by filename, which uses `YYYY-MM-DD` format).

If no plan file exists, tell the user:

> "I couldn't find any plan files. Would you like to create one first with `/my-day:plan`?"

Then stop.

If a plan is found, show the user what was planned:

> **Here's what you planned on DD Month YYYY:**
>
> [display the plan contents]

---

## Phase 2: Gather Reflections

Ask the user these questions one at a time as plain text. Wait for each answer before asking the next:

1. "How did it go?"
2. "What did you learn?"
3. "Was the result what you expected?"

The user can answer briefly — a sentence or two is fine. They can also say "skip" for any question.

---

## Phase 3: Polish & Confirm

Rewrite the user's reflections into polished, natural British English:

- Use British spelling throughout: `-ise` not `-ize`, `-our` not `-or`, `-re` not `-er`
- Use British vocabulary: holiday (not vacation), autumn (not fall), fortnight, mobile (not cell phone), etc.
- Make it sound natural — not a word-for-word correction but a genuine rewrite that flows
- Favour contractions and natural phrasing where appropriate
- Remove expressions that sound translated or robotic
- Keep the user's intent and meaning intact

Present the polished version and ask: "Does this look right, or would you like to change anything?"

**Do NOT save until the user confirms.**

---

## Phase 4: Save

Append the reflection to the original plan file, separated by a horizontal rule:

```markdown
---

## Reflection — DD Month YYYY

### How did it go?
[polished reflection]

### What I Learned
[polished learnings]

### Was it what I expected?
[polished answer]
```

Use the British date format: `18 May 2026`. Only include sections the user answered — omit skipped questions.

After saving, confirm with the file path.

---

## Rules

- **Always polish into British English.** Do not show corrections or explanations — just present the clean version. This is a reflection tool, not a language lesson.
- **One question at a time.** Don't batch multiple questions.
- **Keep it concise.** Short, clear sentences.
- **Respect skipped questions.** If the user says "skip", don't include that section.
- **Confirm before saving.** Always show the polished version and get approval before writing to disk.
- **Don't create a new file.** Reflections are always appended to the existing plan file.
