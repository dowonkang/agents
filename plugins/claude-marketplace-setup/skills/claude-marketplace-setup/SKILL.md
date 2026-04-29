---
name: claude-marketplace-setup
description: Use when making a GitHub repo work as a Claude Code marketplace source — so users can add it and selectively install individual plugins from it.
---

# Claude Code Marketplace Setup

## Overview

A Claude Code marketplace is a GitHub repo with `.claude-plugin/marketplace.json`. Without it the repo is a single installable plugin (all-or-nothing). With it, users register it as a named source and install individual plugins from it.

## Two Models

| Model | When to use | Source field |
|-------|-------------|--------------|
| **Single plugin** | Everything installs together | `"source": "./"` |
| **Multi-plugin** | Selective install per skill/command | `"source": "./plugins/<name>"` |

## Single Plugin (Simple)

Add `.claude-plugin/marketplace.json`:

```json
{
  "name": "my-marketplace",
  "id": "my-marketplace",
  "owner": { "name": "username" },
  "metadata": { "description": "...", "version": "1.0.0" },
  "plugins": [
    {
      "name": "my-plugin",
      "source": "./",
      "description": "...",
      "version": "1.0.0",
      "author": { "name": "..." },
      "keywords": [],
      "category": "workflow"
    }
  ]
}
```

The existing `.claude-plugin/plugin.json` defines what gets installed.

## Multi-Plugin (Selective Install)

### Directory Structure

```
.claude-plugin/
├── plugin.json         (marketplace-level descriptor)
└── marketplace.json    (lists all installable plugins)

plugins/
├── skill-a/
│   ├── .claude-plugin/plugin.json
│   └── skills/skill-a/SKILL.md
├── skill-b/
│   ├── .claude-plugin/plugin.json
│   └── skills/skill-b/SKILL.md
└── my-command/
    ├── .claude-plugin/plugin.json   ← must have "commands": "./commands"
    └── commands/my-command.md
```

### Per-Plugin `plugin.json`

```json
{
  "name": "skill-a",
  "version": "1.0.0",
  "description": "...",
  "author": { "name": "..." },
  "license": "MIT"
}
```

For a commands-only plugin, add `"commands": "./commands"`.

### `marketplace.json` with Multiple Plugins

```json
{
  "plugins": [
    { "name": "skill-a", "source": "./plugins/skill-a", "description": "...", "version": "1.0.0", "author": { "name": "..." }, "keywords": [], "category": "workflow" },
    { "name": "my-command", "source": "./plugins/my-command", "description": "...", "version": "1.0.0", "author": { "name": "..." }, "keywords": [], "category": "workflow" }
  ]
}
```

### Moving Tracked Files

Use `git mv` to preserve history when reorganizing:

```sh
mkdir -p plugins/skill-a/skills plugins/skill-a/.claude-plugin
git mv skills/skill-a plugins/skill-a/skills/skill-a
git mv .agents/commands/my-command.md plugins/my-command/commands/my-command.md
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Using `manifest.json` | File must be `marketplace.json` |
| Listing skill paths inside `marketplace.json` | Each skill is a separate plugin directory with its own `plugin.json` |
| Forgetting `"commands": "./commands"` | Commands-only plugins need this in their `plugin.json` |
| Using `cp` instead of `git mv` | Breaks git history for tracked files |
