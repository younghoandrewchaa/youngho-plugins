# yh-plugins

A collection of Claude Code plugins.

---

## create-pull-request

Creates GitHub pull requests the right way: asks for your intent, analyses the code changes, fills in the repo's PR template, and gets smarter over time by learning from your corrections.

### What it does

1. **Asks for purpose** — one question: what is this PR for?
2. **Analyses the diff** — reads every changed file and commit message to add concrete detail
3. **Fills the PR template** — uses your repo's `.github/pull_request_template.md` if it exists
4. **Polishes for clarity** — reviews the draft to correct grammar, refine technical terminology, and ensure a professional tone
5. **Learns from corrections** — stores a per-repo history in `.claude/pr-history/history.json` and applies your style preferences to future drafts
6. **Creates the PR** — runs `gh pr create` with the approved description

### Requirements

- [GitHub CLI (`gh`)](https://cli.github.com/) installed and authenticated
- Claude Code

### Installation

```
/plugin marketplace add younghoandrewchaa/yh-plugins
/plugin install create-pull-request@yh-plugins
/reload-plugins
```

### Usage

> "create a PR"
> "open a pull request"
> "make a PR for this branch"

---

## polish-this

A British English Language Coach. Paste any text and get a natural, fluent rewrite as a native UK speaker would write it — along with a vocabulary spotlight and a plain-English explanation of every correction made.

### What it does

1. **Polished Version** — rewrites your text into natural British English (professional or social register)
2. **Vocabulary Spotlight** — highlights useful idioms, phrasal verbs, and distinctly British expressions used in the rewrite
3. **The 'Why'** — explains every change: grammar fixes, tone improvements, and British-specific spelling or vocabulary choices

### Installation

```
/plugin marketplace add younghoandrewchaa/yh-plugins
/plugin install polish-this@yh-plugins
/reload-plugins
```

### Usage

> "Polish this: [your text]"
> "Make this sound more British"
> "Is this natural British English?"
> "How would a British person say this?"

---

## learning-quiz

Review the current conversation, summarise learning points, and quiz yourself to verify understanding. Extracts what you learned during a session, lets you confirm the list, then runs an interactive quiz with a mix of multiple choice and open-ended questions.

### What it does

1. **Extracts learning points** — scans the conversation for technical concepts, architectural decisions, gotchas, tools, and domain knowledge
2. **Lets you confirm** — presents a numbered list grouped by topic; you can remove, add, or approve before the quiz starts
3. **Quizzes you** — generates 1-2 questions per learning point, one at a time, mixing multiple choice (factual recall) and open-ended (conceptual understanding)
4. **Gives feedback** — immediate feedback after each answer, then a final score with strong areas and topics to revisit

### Installation

```
/plugin marketplace add younghoandrewchaa/yh-plugins
/plugin install learning-quiz@yh-plugins
/reload-plugins
```

### Usage

> "quiz me on what I learned"
> "review this session"
> "test my understanding"

---

## License

MIT
