---
name: wikify
description: Ingest a source into your personal LLM wiki. On first use, asks where your wiki lives (or should be created) and remembers it. Use when you want to add a document, file path, or pasted content to your wiki, or when the user says /learn-kit:wikify.
---

# Wikify

## Overview

Entry point for your personal LLM wiki. Discovers (or creates) your wiki directory, reads its `CLAUDE.md` schema, then runs the Ingest workflow.

**Announce at start:** "Opening your wiki..."

---

## Step 1 — Discover the wiki path

Check memory for a stored value under the key `wikify_wiki_path`.

**If found:** read `<path>/CLAUDE.md` to confirm the wiki is intact. If `CLAUDE.md` is missing, treat this as a new wiki and jump to **Step 2b**.

**If not found:** ask the user exactly this:

> "Where is your wiki directory? Enter an absolute path (e.g. ~/Documents/wiki). If it doesn't exist yet, I'll create it."

Save the entered path to memory as `wikify_wiki_path`. Then read `<path>/CLAUDE.md` — if missing, go to **Step 2b**.

---

## Step 2a — Wiki exists

Read `<wiki_path>/CLAUDE.md`. This is the authoritative schema and workflow for this wiki. Then continue to **Step 2c** to sync from the remote before following its **Ingest** instructions from Step 3 onwards — do not substitute your own logic.

---

## Step 2b — New wiki: bootstrap it

The path has no `CLAUDE.md`. Create the full wiki structure:

1. Create `<wiki_path>/` if it doesn't exist
2. Create subdirectories:
   - `<wiki_path>/wiki/sources/`
   - `<wiki_path>/wiki/pages/`
   - `<wiki_path>/wiki/syntheses/`
   - `<wiki_path>/raw/assets/`
3. Create `<wiki_path>/index.md`:

```markdown
# Wiki Index

## Sources

## Pages

## Syntheses & Comparisons

_(none yet)_
```

4. Create `<wiki_path>/log.md`:

```markdown
## [<today's date>] init — Wiki initialized
```

5. Create `<wiki_path>/CLAUDE.md` using the **canonical template** in the section below.
6. Tell the user: "Your wiki has been created at `<wiki_path>`. Ready to ingest your first source."

Then continue to Step 2c.

---

## Step 2c — Sync from remote first (git-based wikis)

Before reading or writing any wiki pages, pull the latest from the remote so you work against current content:

1. **Check for git**: run `git -C <wiki_path> rev-parse --is-inside-work-tree`. If the exit code is non-zero the wiki is not a git repo — skip this step entirely and continue to Step 3.

2. **Check for a remote**: run `git -C <wiki_path> remote`. If there is no remote configured, skip the pull and continue.

3. **Pull with rebase**: `git -C <wiki_path> pull --rebase`.
   - If the working tree has uncommitted local changes that block the rebase, stash them first (`git -C <wiki_path> stash`), pull, then pop the stash (`git -C <wiki_path> stash pop`).
   - If a merge/rebase conflict arises, resolve it preserving the wiki's semantic intent (spawn a sub-agent with full conflict context if needed). Never abort or reset.

4. Continue to Step 3.

---

## Step 3 — Run the Ingest workflow

Follow the **Ingest** operation from the wiki's `CLAUDE.md`. Concretely:

Ask:

> "What would you like to add to the wiki? You can paste content, give me a file path, or describe a source."

Then follow the Ingest steps from `CLAUDE.md` exactly — summarise, extract entities, update `index.md`, append to `log.md`.

---

## Canonical CLAUDE.md Template

Use this verbatim when bootstrapping a new wiki in Step 2b.

```markdown
# LLM Wiki — Schema & Workflow

This directory is a personal knowledge base maintained by the LLM. You (Claude) write and maintain the wiki. The user curates sources, asks questions, and guides the analysis.

## Directory Structure

\```
wiki/
  CLAUDE.md          # This file — schema and workflow instructions
  index.md           # Content index: every wiki page with a one-line summary
  log.md             # Append-only chronological log of ingests, queries, lint passes
  wiki/              # LLM-generated markdown pages
    sources/         # Source summary pages
    pages/           # Entity and concept pages
    syntheses/       # Comparison and synthesis pages
  raw/               # User-provided source documents — NEVER MODIFY THESE
    assets/          # Downloaded images referenced by source documents
\```

## Layers

- **raw/** — Immutable source documents. Read from here; never write here.
- **wiki/** — All LLM-generated pages live here. You own this layer entirely.
- **index.md / log.md** — Two special root-level files you maintain on every operation.

---

## Operations

### Ingest

When the user provides a new source (file path or pasted content):

1. Read the source thoroughly.
2. Discuss key takeaways with the user if needed. Ask what angle to emphasise.
3. Write a summary page at `wiki/sources/<slug>.md`.
4. Identify all entities (people, organisations, tools, concepts) and update or create their pages in `wiki/pages/`.
5. Update `index.md` — add the new summary page and any new entity/concept pages.
6. Append an entry to `log.md` in the format:
   \```
   ## [YYYY-MM-DD] ingest | <Source Title>
   - Summary: `wiki/sources/<slug>.md`
   - Pages updated: <list>
   \```

A single ingest may touch 5–15 wiki pages. That's expected and correct.

### Query

When the user asks a question:

1. Read `index.md` to find relevant pages.
2. Read those pages.
3. Synthesise an answer with citations (link to wiki pages, not raw sources).
4. If the answer is a useful, reusable artefact (comparison, analysis, synthesis), offer to save it as a new wiki page and add it to `index.md`.
5. Append to `log.md`:
   \```
   ## [YYYY-MM-DD] query | <Short question summary>
   - Pages consulted: <list>
   - Output filed: <page path or "none">
   \```

### Lint

When the user asks to health-check the wiki:

1. Read `index.md` for a full inventory.
2. Sample pages to check for: contradictions, stale claims, orphan pages (no inbound links), important concepts without their own page, missing cross-references.
3. Report issues and suggested actions.
4. Optionally suggest new sources to seek out or new questions to investigate.
5. Append to `log.md`:
   \```
   ## [YYYY-MM-DD] lint
   - Issues found: <count>
   - Actions taken: <list or "none — reported only">
   \```

---

## Page Conventions

### Wiki pages (`wiki/*.md`)

Each page should have YAML frontmatter:

\```yaml
---
title: <Page title>
type: source | entity | concept | synthesis | comparison
tags: [<relevant tags>]
sources: [<list of raw source slugs this page draws from>]
updated: YYYY-MM-DD
---
\```

`entity` and `concept` pages both live in `wiki/pages/` — the `type` field distinguishes them for querying and filtering.

- Use standard markdown links for internal cross-references: `[Link Text](./relative/path.md)`. Do NOT use `[[WikiLinks]]` — VS Code does not support that syntax.
- Paths are relative to the file being written. From `wiki/sources/`, topic links are `../pages/name.md`. From root (`index.md`), all wiki links are `./wiki/pages/name.md` (or `./wiki/sources/name.md` / `./wiki/syntheses/name.md`).
- Keep pages focused — one entity or concept per page.
- Flag contradictions inline: `> **Contradiction:** This claim conflicts with [OtherPage](../path/to/other-page.md)`.

### index.md

Organised by type. Each entry: `- [display text](./wiki/type/slug.md) — one-line summary`.

Example structure:
\```markdown
# Wiki Index

## Sources
- [sources/some-article](./wiki/sources/some-article.md) — Summary of article X by Author Y (2025)

## Pages
- [pages/person-name](./wiki/pages/person-name.md) — Researcher at Lab X, known for contribution Y
- [pages/some-concept](./wiki/pages/some-concept.md) — Core idea in domain Z

## Syntheses & Comparisons
- [syntheses/comparison-of-x-and-y](./wiki/syntheses/comparison-of-x-and-y.md) — Side-by-side of X vs Y across dimensions A, B, C
\```

### log.md

Append-only. Newest entries at the bottom. Each entry starts with `## [YYYY-MM-DD]` so it's grep-parseable:

\```bash
grep "^## \[" log.md | tail -5   # last 5 log entries
\```

---

## Output Formats

Depending on the question, answers may be delivered as:
- Markdown page (default)
- Comparison table (markdown)
- Marp slide deck (for presentation requests)
- Data summary (if sources contain structured data)

Offer to file any substantive output back into `wiki/` as a new page.

---

## Cross-Referencing Rules

- When you create or update a page, scan `index.md` for related pages and add links.
- Every entity mentioned in a source summary should link to (or create) its entity page.
- Contradiction flagging is mandatory — never silently overwrite a claim that conflicts with an existing page.

---

## First-Run Checklist

On first use of this wiki (when `index.md` doesn't exist yet):

1. Create `index.md` with the structure above but empty sections.
2. Create `log.md` with a single entry: `## [YYYY-MM-DD] init — Wiki initialized`.
3. Create subdirectories: `wiki/sources/`, `wiki/pages/`, `wiki/syntheses/`, `raw/assets/`.
4. Confirm setup to the user and ask for the first source to ingest.
```

---

## Git Sync (run after every file creation or update)

> The pre-operation pull happens in **Step 2c**; this section handles the commit + push afterwards.

After completing any operation that writes or modifies files in the wiki (Ingest, Lint, Query when a page is saved):

1. **Check for git**: run `git -C <wiki_path> rev-parse --is-inside-work-tree` (or equivalent). If the exit code is non-zero the directory is not tracked by git — skip the remaining steps and carry on.

2. **Stage all changes**: `git -C <wiki_path> add -A`

3. **Compose a commit message** that summarises what changed — e.g. `"ingest: <Source Title>"`, `"lint: <N> issues fixed"`, `"query: filed <page-slug>"`, or `"init: wiki bootstrapped"`.

4. **Commit**: `git -C <wiki_path> commit -m "<message>"`. If there is nothing to commit (working tree clean) skip to step 6.

5. **Conflict handling**: if a merge conflict arises at any point, spawn a sub-agent with the full conflict context and instruct it to resolve the conflict, preserving the wiki's semantic intent. Never abort or reset — always resolve and commit.

6. **Push**: `git -C <wiki_path> push`. If the push is rejected (non-fast-forward), pull with rebase first (`git -C <wiki_path> pull --rebase`), resolve any conflicts as above, then push again.

7. Confirm to the user: "Changes synced to git." (one line, no extra detail unless there was a conflict).

---

## Red Flags

**Never** hardcode the wiki path — always read from memory and confirm on disk before proceeding.

**Never** skip reading `CLAUDE.md` — it is the authoritative workflow; do not substitute your own ingestion logic.

**Never** write to `raw/` — that directory is for immutable user-provided source documents only.

**Never** overwrite an existing `CLAUDE.md` — only create it when the file is genuinely absent.

**Never** start an ingest/query/lint on a git-based wiki without first pulling from the remote (Step 2c) — stale local pages cause avoidable merge conflicts and outdated analysis.

## Example Trigger Phrases

> "/learn-kit:wikify"
> "Add this to my wiki"
> "Ingest this document"
> "Save this to the wiki"
