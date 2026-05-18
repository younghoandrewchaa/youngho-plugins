---
name: my-day
description: Plan and reflect on your goals for today, tomorrow, this week, and this month. Polishes your answers into natural British English and surfaces relevant past learnings as advice. Use /my-day to plan, /my-day reflect to review. Triggers on plan my day, daily plan, what should I do today, reflect, end of day review, weekly review, monthly review.
---

# My Day — Planning & Reflection

Plan what you want to accomplish and reflect on how it went, with your words polished into natural British English.

**Announce at start:** "Using my-day to [plan/reflect on] your goals."

## Input Modes

- **No argument or `plan`** (e.g. `/my-day` or `/my-day plan`): Start planning mode
- **`reflect`** (e.g. `/my-day reflect`): Start reflection mode

---

## Step 0: Resolve Storage Folder

Before doing anything else, determine where to save plan and reflection files:

1. Check Claude memory for a saved `my-day-storage-path` preference
2. If found, verify the folder exists using Bash (`ls`). If it exists, proceed
3. If not found (first use) or the folder no longer exists, ask the user:

Use `AskUserQuestion`:
- Question: "Where would you like me to save your plans and reflections?"
- Options: `~/plans/`, `~/Documents/plans/`, plus "Other" for a custom path
- Create the folder if it doesn't exist
- Save the chosen path to Claude memory (type: `user`, name: `my-day-storage-path`) so it persists across sessions

---

## Plan Mode

### Phase 1: Gather Goals

Ask the user about each time horizon using `AskUserQuestion`. Present all four in a single question with free-text input. If the tool doesn't support free text for all four at once, ask them one at a time as plain text questions (not `AskUserQuestion`):

1. "What do you want to accomplish **today**?"
2. "What about **tomorrow**?"
3. "Any goals for **this week**?"
4. "And for **this month**?"

For each question, make it clear the user can say "skip" or "nothing" to skip that horizon.

### Phase 2: Surface Past Learnings

After collecting answers, scan existing reflection files in the storage folder for "What I Learned" sections. Look for entries that relate to the user's current goals.

If relevant learnings are found, present them as brief advice before the polished plan:

> **From your past reflections:**
> - On 12 May, you noted that [learning] — worth keeping in mind for [today's goal]

Keep this to 1-3 items maximum. If nothing relevant is found, skip this section entirely — don't force it.

### Phase 3: Polish & Confirm

Rewrite each of the user's answers into polished, natural British English:

- Use British spelling throughout: `-ise` not `-ize`, `-our` not `-or`, `-re` not `-er`
- Use British vocabulary: holiday (not vacation), autumn (not fall), fortnight, etc.
- Make it sound natural — not a word-for-word correction but a genuine rewrite that flows
- Favour contractions and natural phrasing where appropriate
- Remove expressions that sound translated or robotic
- Keep the user's intent and meaning intact

Present the polished version and ask: "Does this look right, or would you like to change anything?"

**Do NOT save until the user confirms.**

### Phase 4: Save

Write the plan to `<storage-folder>/YYYY-MM-DD-plan.md` using this format:

```markdown
# Plan — DD Month YYYY

## Today
- [polished goals]

## Tomorrow
- [polished goals]

## This Week
- [polished goals]

## This Month
- [polished goals]
```

Use the British date format: `18 May 2026`, not `May 18, 2026`.

Only include sections the user provided goals for — omit skipped horizons.

---

## Reflect Mode

### Phase 1: Find the Plan

Read the most recent plan file from the storage folder. If no plan file exists, tell the user and suggest running `/my-day plan` first.

Show the user what was planned:

> **Here's what you planned:**
> [display the plan contents]

### Phase 2: Gather Reflections

Ask the user these questions as plain text (one at a time, wait for each answer):

1. "How did it go?"
2. "What did you learn?"
3. "Was the result what you expected?"

The user can answer briefly — a sentence or two is fine.

### Phase 3: Polish & Confirm

Rewrite the user's reflections into polished British English using the same rules as Plan Mode. Present the polished version and ask for confirmation.

### Phase 4: Save

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

---

## Rules

- **Always polish into British English.** Do not show corrections or explanations — just present the clean version. This is not a language lesson; it's a planning tool.
- **One question at a time.** Don't batch multiple questions in a single message.
- **Keep it concise.** The user is planning, not writing an essay. Short, clear sentences.
- **Respect skipped horizons.** If the user skips "this month", don't include it in the file.
- **Don't invent learnings.** Only surface past reflections that genuinely relate to current goals.
- **Use British date format.** `18 May 2026`, not `May 18, 2026` or `2026-05-18` in displayed text. File names use `YYYY-MM-DD` for sorting.
- **Confirm before saving.** Always show the polished version and get a thumbs-up before writing to disk.
