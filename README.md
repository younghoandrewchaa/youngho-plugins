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

## learn-kit

A learning toolkit with three skills: a pre-testing quiz to prime your reading, a recall quiz over your personal wiki to fight forgetting, and a personal wiki manager.

### `/learn-kit:learning-quiz`

Pre-test yourself on a topic *before* you study it, to prime your reading and surface knowledge gaps. Based on the **Pre-testing strategy** — attempting questions on unfamiliar material activates prior knowledge and primes attention, so the real information lands far more readily when you read it (it works even when you guess every answer wrong). Works from any content you're about to study — a block of text, a URL, or a topic — or from the current conversation. Every question is multiple choice, and the answers teach.

1. **Confirms the scope** — lists the topics it will quiz on (no answers given away, which would spoil the priming); you can remove, add, or approve before the pre-test starts
2. **Pre-tests you** — generates 1-2 multiple-choice questions per topic, one at a time, with plausible, misconception-based distractors
3. **Teaches through feedback** — immediate feedback on every answer explaining *why* it's correct; guessing is encouraged and low scores are expected and useful
4. **Leaves you a learning note** — a reading-focus guide plus a keepable note capturing every missed question, its correct answer, and the explanation, ready to reinforce when you read

### `/learn-kit:wiki-quiz`

Quiz yourself on the contents of your personal wiki to remember and deepen what you've already learned. The post-study counterpart to `learning-quiz` — based on the **testing effect** and **active recall**: retrieving a fact from memory strengthens it far more than re-reading, and revisiting wiki content after time has passed interrupts the forgetting curve. Samples 10 questions broadly across your whole wiki. Every question is multiple choice, and the answers reinforce.

1. **Opens your wiki** — finds your wiki path (the same one `wikify` maintains) and reads the index and pages; if the wiki isn't set up, it points you to `/learn-kit:wikify` first
2. **Samples broadly** — draws 10 questions across sources, pages, and syntheses for whole-wiki coverage, and tells you which areas it will span
3. **Quizzes one at a time** — multiple-choice questions with plausible, confusion-based distractors and immediate reinforcing feedback explaining *why*
4. **Leaves you a revision plan** — a strong/weak-areas summary and a "worth revisiting" list pointing back to the relevant wiki pages, plus a keepable learning note refreshing the material and deepening the spots you missed
5. **Tracks your progress** — records each result with a timestamp to `quiz-history/history.jsonl` and regenerates a standalone `report.html` with a score-over-time chart, a session table, and a per-topic strength summary so you can see persistent weak spots (and offers to open it)

### `/learn-kit:wikify`

Personal LLM wiki manager. Ingest documents, URLs, and pasted content into a structured knowledge base. On first use, asks where your wiki lives (or should be created) and remembers it across sessions.

1. **Discovers your wiki** — checks memory for a stored wiki path; if not found, asks you where to create one
2. **Bootstraps if needed** — creates the full directory structure, index, log, and schema on first use
3. **Ingests sources** — reads the content, writes a summary page, extracts entities, updates the index and log
4. **Queries your wiki** — answers questions by reading and synthesising from your wiki pages
5. **Lints for health** — checks for contradictions, orphan pages, and missing cross-references

### Installation

```
/plugin marketplace add younghoandrewchaa/yh-plugins
/plugin install learn-kit@yh-plugins
/reload-plugins
```

### Usage

> "/learn-kit:learning-quiz" — pre-test yourself before studying a topic
> "prime me on <topic> before I read about it"
> "quiz me before I read X"
> "/learn-kit:wiki-quiz" — quiz yourself on your wiki to remember what you've learned
> "help me remember what's in my wiki"
> "/learn-kit:wikify" — open your wiki and ingest a source
> "add this to my wiki"
> "ingest this document"

---

## my-day

Plan and reflect on your goals for today, tomorrow, this week, and this month. Your answers are polished into natural British English, and relevant past learnings are surfaced as advice when you plan.

This plugin has two skills:

### `/my-day:my-plan`

1. **Asks about your goals** — today, tomorrow, this week, this month (skip any you like)
2. **Surfaces past learnings** — scans your previous reflections for advice relevant to today's goals
3. **Polishes into British English** — rewrites your answers into natural, fluent UK English
4. **Saves to a file** — stores the plan as a dated markdown file in your chosen folder

### `/my-day:my-reflect`

1. **Reads your latest plan** — shows what you set out to do
2. **Asks how it went** — three questions: how did it go, what did you learn, was it what you expected
3. **Polishes into British English** — rewrites your reflections into natural UK English
4. **Appends to the plan file** — adds the reflection to the same file for a complete record

On first use, the skill asks where to save files and remembers your choice.

### Installation

```
/plugin marketplace add younghoandrewchaa/yh-plugins
/plugin install my-day@yh-plugins
/reload-plugins
```

### Usage

> "/my-day:my-plan" — plan your day, week, or month
> "/my-day:my-reflect" — reflect on how it went

---

## License

MIT
