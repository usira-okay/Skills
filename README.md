# Skills Marketplace

A Claude Code plugin marketplace.

## Add this marketplace

```
/plugin marketplace add usira-okay/Skills
```

## Install a plugin

```
/plugin install hello-world@skills
```

## Available plugins

| Plugin | Description |
| --- | --- |
| [hello-world](plugins/hello-world) | Minimal example plugin (`/hello` command) used to verify the marketplace setup. |
| [dev-workflow](plugins/dev-workflow) | Skills for everyday development workflow, such as commit message conventions (`branch-aware-commits`). Install with `/plugin install dev-workflow@skills`. |

## Repo layout

```
.claude-plugin/marketplace.json   # marketplace manifest
plugins/
  hello-world/
    .claude-plugin/plugin.json    # plugin manifest
    commands/hello.md             # example command
  dev-workflow/
    .claude-plugin/plugin.json    # plugin manifest
    skills/
      branch-aware-commits/
        SKILL.md                  # commit message convention skill
```

Each plugin lives under `plugins/<name>/` with its own `.claude-plugin/plugin.json`,
and is registered in `.claude-plugin/marketplace.json`.
