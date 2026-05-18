# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A Claude Code plugin marketplace (`yh-plugins`) containing multiple plugins, each with one or more skills. Plugins are installed by users via `/plugin marketplace add younghoandrewchaa/yh-plugins`.

## Architecture

```
.claude-plugin/marketplace.json    ← Registry: lists all plugins with name, description, source path
plugins/<name>-plugin/
  .claude-plugin/plugin.json       ← Plugin metadata: name (used as skill namespace), version, author
  skills/<skill-name>/SKILL.md     ← Skill definition: YAML frontmatter + markdown instructions
```

**Naming convention**: The plugin `name` in `plugin.json` becomes the namespace prefix. The skill directory name becomes the suffix. A plugin named `my-day` with `skills/my-plan/SKILL.md` produces the skill `/my-day:my-plan`. Single-skill plugins (where the skill name matches the plugin name) are invoked without a namespace, e.g. `/polish-this`.

**SKILL.md frontmatter** must include `name` (matching the directory name) and `description` (including trigger phrases for auto-detection). The body contains the full instructions Claude follows when the skill is invoked.

## Adding a New Plugin

1. Create `plugins/<name>-plugin/.claude-plugin/plugin.json` with name, description, version, author
2. Create `plugins/<name>-plugin/skills/<skill-name>/SKILL.md` with frontmatter and instructions
3. Add the plugin entry to `.claude-plugin/marketplace.json`
4. Add a section to `README.md`

## Conventions

- Use British English in all user-facing text (spelling, vocabulary, tone) — this applies to skill instructions, README, and descriptions
- **Always bump the `version` in `plugin.json` when any change is made to a plugin** (skills, metadata, etc.). Users receive updates via `/plugin update` which checks version numbers — if the version isn't bumped, changes won't propagate.
- Each plugin is self-contained — no shared code or cross-plugin dependencies
