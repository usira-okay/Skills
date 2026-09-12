# Skills Marketplace

A Claude Code plugin marketplace.

## Add this marketplace

```
/plugin marketplace add usira-okay/Skills
```

## Install a plugin

```
/plugin install dev-workflow@skills
```

## Available plugins

| Plugin | Description |
| --- | --- |
| [dev-workflow](plugins/dev-workflow) | Skills for everyday development workflow, such as commit message conventions (`branch-aware-commits`). Install with `/plugin install dev-workflow@skills`. |

## Repo layout

```
.claude-plugin/marketplace.json   # marketplace manifest
plugins/
  dev-workflow/
    .claude-plugin/plugin.json    # plugin manifest
    skills/
      branch-aware-commits/
        SKILL.md                  # commit message convention skill
```

Each plugin lives under `plugins/<name>/` with its own `.claude-plugin/plugin.json`,
and is registered in `.claude-plugin/marketplace.json`.
