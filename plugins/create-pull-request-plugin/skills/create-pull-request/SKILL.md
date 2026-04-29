---
name: create-pull-request
description: Use when creating a GitHub pull request for the current branch - asks the user for intent, analyses code changes, applies the repo's PR template, and improves descriptions over time by learning from corrections
---

# Create Pull Request

## Overview

Creates a GitHub pull request by combining user intent, code analysis, the repo's PR template, and preferences learned from past corrections.

**Announce at start:** "Using create-pull-request skill to prepare your PR."

## Step 1: Load Context

Run in parallel:

```bash
# Branch info and commits since diverging from main
git branch --show-current
git log --oneline $(git merge-base HEAD origin/HEAD)..HEAD

# What changed
git diff $(git merge-base HEAD origin/HEAD)..HEAD --stat
git diff $(git merge-base HEAD origin/HEAD)..HEAD
```

```bash
# PR template (try common locations)
cat .github/pull_request_template.md 2>/dev/null || \
  cat .github/PULL_REQUEST_TEMPLATE.md 2>/dev/null || \
  echo "NO_TEMPLATE"
```

```bash
# Lessons learned from past PRs in this repo
cat .claude/pr-history/history.json 2>/dev/null || echo "[]"
```

## Step 2: Extract Lessons from History

Parse `.claude/pr-history/history.json`. From each entry's `corrections` array, derive style rules:

- How the user phrases the "why" (business impact vs implementation detail)
- Sections they always trim or expand
- Anything they flag as obvious/redundant

Carry 3–5 concrete rules forward into the draft. If history is empty, note it and proceed.

## Step 3: Ask the User

Ask exactly this — nothing more:

```
What is the purpose of this pull request?
```

Wait for the answer before drafting.

## Step 4: Draft the Description

Combine:
1. **User's stated purpose** — keep their wording; add context around it, not instead of it
2. **Code analysis** — concrete changes from the diff and commit messages (files, functions, behaviour)
3. **PR template** — populate every section with real content; no placeholders
4. **Lessons from history** — apply style and content rules derived in Step 2

**If a template exists:** Fill every section. Infer answers from the diff — don't leave anything blank. Copy every heading from the template character-for-character — never rephrase, reorder, or remove headings. Only the body text beneath each heading may be written or changed.

**If no template:**
```
### Why are you making this change?
<user's purpose + context from code>

### What changed?
<bullet list of concrete changes from diff>

### Possible production impacts
<migrations, API changes, dependency updates, config changes — or "None" if truly none>

### Anything else reviewers should know?
<only non-obvious things — silent behaviour changes, surprising constraints, gotchas>
```

Show the draft and ask:

```
Does this look right, or would you like to adjust anything?
```

## Step 5: Handle Corrections

Apply corrections. For each one, note:
- Which section changed
- What the original said
- What it became
- The inferred lesson (e.g. "User removes structural summaries from the 'anything else' section — only non-obvious things belong there")

Ask: "Anything else, or shall I create the PR?" Repeat until approved.

## Step 6: Create the PR

```bash
gh pr create \
  --title "<concise title>" \
  --body "$(cat <<'EOF'
<approved description>
EOF
)"
```

Add `--draft` if requested. Output the PR URL when done.

## Step 7: Save to History

```bash
mkdir -p .claude/pr-history
```

Append to `.claude/pr-history/history.json` (read first, never overwrite):

```json
{
  "branch": "<branch-name>",
  "date": "<ISO date>",
  "purpose": "<user's stated purpose>",
  "generated": "<original draft>",
  "final": "<approved description>",
  "pr_url": "<url>",
  "corrections": [
    {
      "field": "<section name>",
      "original": "<what you wrote>",
      "corrected": "<what user changed it to>",
      "lesson": "<inferred preference>"
    }
  ]
}
```

## Quick Reference

| Step | Action |
|------|--------|
| 1 | Load git context, PR template, history — all in parallel |
| 2 | Derive style rules from past corrections |
| 3 | Ask user for the purpose |
| 4 | Draft: purpose + code analysis + template + rules |
| 5 | Show draft → corrections → record lessons |
| 6 | `gh pr create` with approved description |
| 7 | Append entry to `.claude/pr-history/history.json` |

## Common Mistakes

**Skipping history** — Repeats mistakes the user already corrected. Always read before drafting.

**Placeholder text in template sections** — Infer from the diff. A blank section is never acceptable.

**Altering template headings** — Copy headings exactly as they appear in the template. Changing even one word can break CI checks that match on heading text.

**Rewriting the user's words** — Their phrasing carries intent. Preserve it; add detail around it.

**Overwriting history** — Read the JSON array first, append, write back. Never truncate.

**"Anything else" section as a change log** — Only include things that are genuinely non-obvious or easy to miss. Structural changes visible in the diff don't belong there.

## Red Flags

**Never:**
- Create the PR without showing the draft first
- Leave template sections as placeholder text
- Overwrite `.claude/pr-history/history.json`
- List obvious changes in "Anything else reviewers should know"
- Alter template headings in any way — not the wording, not the capitalisation, not the order

**Always:**
- Read history before drafting
- Show draft and wait for approval
- Record the final description and corrections even when there are none
- Keep the PR URL in history
