---
name: my-plan
description: Plan what you want to accomplish today, tomorrow, this week, and this month. Polishes your goals into natural British English and surfaces relevant past learnings as advice. Triggers on plan my day, daily plan, what should I do today, weekly plan, monthly plan, set goals.
---

# My Day — Plan

Plan what you want to accomplish, with your words polished into natural British English.

**Announce at start:** "Using my-day:my-plan to capture your goals."

## Step 0: Resolve Storage Folder

Before doing anything else, determine where to save plan files:

1. Check Claude memory for a saved `my-day-storage-path` preference
2. If found, verify the folder exists using Bash (`ls`). If it exists, proceed
3. If not found (first use) or the folder no longer exists, ask the user:

Use `AskUserQuestion`:
- Question: "Where would you like me to save your plans and reflections?"
- Options: `~/plans/`, `~/Documents/plans/`, plus "Other" for a custom path
- Create the folder if it doesn't exist (`mkdir -p`)
- Save the chosen path to Claude memory (type: `user`, name: `my-day-storage-path`) so it persists across sessions

---

## Phase 1: Gather Goals

Ask the user about each time horizon, one at a time as plain text questions. Wait for each answer before asking the next:

1. "What do you want to accomplish **today**?"
2. "What about **tomorrow**?"
3. "Any goals for **this week**?"
4. "And for **this month**?"

Make it clear the user can say "skip" or "nothing" to skip any horizon.

## Phase 2: Surface Past Learnings

After collecting answers, scan existing plan and reflection files in the storage folder. Look for files containing "What I Learned" sections whose content relates to the user's current goals.

If relevant learnings are found, present them as brief advice:

> **From your past reflections:**
> - On 12 May, you noted that [learning] — worth keeping in mind for [today's goal]

Keep this to 1–3 items maximum. If nothing relevant is found, skip this section entirely — don't force it.

## Phase 3: Polish & Confirm

Rewrite each of the user's answers into polished, natural British English:

- Use British spelling throughout: `-ise` not `-ize`, `-our` not `-or`, `-re` not `-er`
- Use British vocabulary: holiday (not vacation), autumn (not fall), fortnight, mobile (not cell phone), etc.
- Make it sound natural — not a word-for-word correction but a genuine rewrite that flows
- Favour contractions and natural phrasing where appropriate
- Remove expressions that sound translated or robotic
- Keep the user's intent and meaning intact

Present the polished version and ask: "Does this look right, or would you like to change anything?"

**Do NOT save until the user confirms.**

## Phase 4: Save

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

Use the British date format in headings: `18 May 2026`, not `May 18, 2026`. File names use `YYYY-MM-DD` for sorting.

Only include sections the user provided goals for — omit skipped horizons.

After saving, confirm with the file path.

---

## Rules

- **Always polish into British English.** Do not show corrections or explanations — just present the clean version. This is a planning tool, not a language lesson.
- **One question at a time.** Don't batch multiple questions.
- **Keep it concise.** Short, clear sentences. The user is planning, not writing an essay.
- **Respect skipped horizons.** If the user says "skip", don't include that section.
- **Don't invent learnings.** Only surface past reflections that genuinely relate to current goals.
- **Confirm before saving.** Always show the polished version and get approval before writing to disk.
