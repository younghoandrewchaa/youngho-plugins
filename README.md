# create-pull-request

A Claude Code plugin that creates GitHub pull requests the right way: asks for your intent, analyses the code changes, fills in the repo's PR template, and gets smarter over time by learning from your corrections.

## What it does

1. **Asks for purpose** — one question: what is this PR for?
2. **Analyses the diff** — reads every changed file and commit message to add concrete detail
3. **Fills the PR template** — uses your repo's `.github/pull_request_template.md` if it exists
4. **Polishes for clarity** — reviews the draft to correct grammar, refine technical terminology, and ensure a professional tone (perfect for non-native speakers).
5. **Learns from corrections** — stores a per-repo history in `.claude/pr-history/history.json` and applies your style preferences to future drafts
6. **Creates the PR** — runs `gh pr create` with the approved description

## Requirements

- [GitHub CLI (`gh`)](https://cli.github.com/) installed and authenticated
- Claude Code

## Installation

```
/plugin install create-pull-request@<marketplace>
```

Or install directly from this repository URL in Claude Code's plugin browser.

## Usage

Just tell Claude to create a PR:

> "create a PR"
> "open a pull request"
> "make a PR for this branch"

Claude will invoke the skill automatically. It will ask you one question (the purpose), show you a draft, and create the PR once you approve.

## How learning works

After each PR, the skill saves the final description and any corrections you made to `.claude/pr-history/history.json` in your repo. On the next PR, it reads those corrections and applies the inferred style rules — so if you always trim the "anything else" section down to just the non-obvious things, it will do that from the start next time.

The history file is repo-local and gitignored by default (add `.claude/` to your `.gitignore`).

## License

MIT
