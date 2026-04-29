# Release Notes

## v1.0.0 (2026-04-29)

Initial release.

- `create-pull-request` skill: asks for the PR purpose, analyses the diff, populates the repo's PR template, and creates the PR via `gh pr create`
- Per-repo history stored in `.claude/pr-history/history.json`
- Learns from corrections: style preferences are applied to future drafts in the same repo
