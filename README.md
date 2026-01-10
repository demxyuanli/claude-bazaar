# Claude Bazaar

A bazaar of Claude Code plugins and curiosities.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Validate Plugins](https://github.com/svgsponer/claude-bazaar/actions/workflows/validate-plugins.yml/badge.svg)](https://github.com/svgsponer/claude-bazaar/actions/workflows/validate-plugins.yml)

## Installation

Add the marketplace to Claude Code:

```bash
/plugin marketplace add svgsponer/claude-bazaar
```

Then install plugins:

```bash
/plugin install plugin-development@claude-bazaar
```

## What's Included

| Component | Description |
|-----------|-------------|
| **Plugin Development Toolkit** | `plugin-development` plugin with 7 slash commands, a `plugin-authoring` skill for ambient guidance, and a reviewer agent |
| **CI/CD Workflows** | GitHub Actions for automated plugin validation on every push and PR |
| **Documentation** | Complete guides for plugins, hooks, settings, commands, skills, and sub-agents |

## Developing Plugins

### Install the Plugin Development Toolkit

```bash
# Start Claude Code
claude

# Add your local marketplace
/plugin marketplace add .

# Install the development toolkit
/plugin install plugin-development@claude-bazaar
```

### Create Your First Plugin

```bash
# Scaffold a new plugin
/plugin-development:init my-awesome-plugin

# Add components
/plugin-development:add-command my-command "Description of what it does"
/plugin-development:add-skill my-skill "Use when working with..."

# Validate before publishing
/plugin-development:validate
```

## Plugin Development Commands

The `plugin-development` plugin provides these commands:

| Command | Description |
|---------|-------------|
| `/plugin-development:init [name]` | Scaffold a new plugin with standard structure |
| `/plugin-development:add-command [name] [desc]` | Add a new slash command |
| `/plugin-development:add-skill [name] [desc]` | Add a new skill with SKILL.md |
| `/plugin-development:add-agent [name] [desc]` | Add a new sub-agent |
| `/plugin-development:add-hook [event] [matcher]` | Add a hook configuration |
| `/plugin-development:validate` | Validate plugin structure and configuration |
| `/plugin-development:test-local` | Create dev marketplace for local testing |

## Repository Structure

```
├── .claude-plugin/
│   └── marketplace.json          # Marketplace configuration
├── .github/
│   └── workflows/
│       └── validate-plugins.yml  # CI/CD validation
├── docs/                         # Comprehensive documentation
│   ├── plugins.md                # Plugin development guide
│   ├── plugins-reference.md      # Technical specifications
│   ├── plugin-marketplaces.md    # Marketplace management
│   ├── hooks.md                  # Event-driven automation
│   ├── settings.md               # Configuration options
│   ├── slash-commands.md         # Command system reference
│   ├── skills.md                 # Agent skills guide
│   └── sub-agents.md             # Sub-agent system
└── plugins/
    └── plugin-development/       # Development toolkit
```

## Team Distribution

Configure automatic marketplace installation for your team by adding `.claude/settings.json` to your projects:

```json
{
  "extraKnownMarketplaces": {
    "claude-bazaar": {
      "source": {
        "source": "github",
        "repo": "svgsponer/claude-bazaar"
      }
    }
  }
}
```

When team members trust the repository folder, Claude Code automatically installs the marketplace. See [Configure team marketplaces](https://code.claude.com/docs/en/plugin-marketplaces#configure-team-marketplaces) for details.

## Installing from GitHub

Once your marketplace is published to GitHub, users can install plugins with:

```bash
# Add the marketplace
/plugin marketplace add svgsponer/claude-bazaar

# Install a plugin
/plugin install plugin-name@claude-bazaar
```

## Local Testing

Test your plugins before publishing:

```bash
# Navigate to your marketplace
cd claude-bazaar

# Start Claude Code
claude

# Add local marketplace
/plugin marketplace add .

# Install and test a plugin
/plugin install plugin-development@claude-bazaar
```

For iterative development:

```bash
# After making changes
/plugin-development:validate
/plugin uninstall plugin-name@claude-bazaar
/plugin install plugin-name@claude-bazaar
```

## Uninstalling

Remove the marketplace and its plugins from Claude Code:

```bash
# Remove a specific plugin
/plugin uninstall plugin-name@claude-bazaar

# Remove the marketplace entirely
/plugin marketplace remove claude-bazaar
```

To completely remove, delete the cloned repository directory.

## Creating Plugins Manually

If you prefer manual setup over the scaffolding commands:

### 1. Create Plugin Directory

```bash
mkdir -p plugins/my-plugin/.claude-plugin
mkdir -p plugins/my-plugin/commands
```

### 2. Add Plugin Manifest

Create `plugins/my-plugin/.claude-plugin/plugin.json`:

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "Description of what your plugin does",
  "author": {
    "name": "Your Name",
    "email": "your-email@example.com"
  },
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"]
}
```

### 3. Create a Command

Create `plugins/my-plugin/commands/my-command.md`:

```markdown
---
description: Brief description of what the command does
argument-hint: [arg1] [arg2]
---

# My Command

Instructions for Claude on how to execute this command.

## Steps

1. First step
2. Second step
3. Third step
```

### 4. Register in Marketplace

Add to `.claude-plugin/marketplace.json`:

```json
{
  "plugins": [
    {
      "name": "my-plugin",
      "description": "Description of what your plugin does",
      "version": "1.0.0",
      "author": {
        "name": "Your Name"
      },
      "source": "./plugins/my-plugin",
      "category": "utilities",
      "tags": ["tag1", "tag2"]
    }
  ]
}
```

## Documentation

### Local Guides (in `docs/`)

| Guide | Description |
|-------|-------------|
| [Plugin Development](docs/plugins.md) | Complete guide to creating plugins |
| [Plugin Reference](docs/plugins-reference.md) | Technical specifications and schemas |
| [Plugin Marketplaces](docs/plugin-marketplaces.md) | Marketplace creation and management |
| [Hooks](docs/hooks.md) | Event-driven automation |
| [Settings](docs/settings.md) | Configuration and customization |
| [Slash Commands](docs/slash-commands.md) | Command system reference |
| [Skills](docs/skills.md) | Agent capabilities and expertise |
| [Sub-Agents](docs/sub-agents.md) | Specialized AI assistants |

### Official Claude Code Documentation

- [Plugins Overview](https://code.claude.com/docs/en/plugins) — Plugin development guide
- [Plugin Marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) — Marketplace management
- [Plugins Reference](https://code.claude.com/docs/en/plugins-reference) — Technical specifications
- [Slash Commands](https://code.claude.com/docs/en/slash-commands) — Command development

## Included Plugins

### plugin-development

A comprehensive plugin development toolkit:

- **7 slash commands** for scaffolding and validation
- **plugin-authoring skill** for ambient guidance
- **plugin-reviewer agent** for release readiness audits
- **Automated hooks** for validation and formatting

See the [plugin-development README](plugins/plugin-development/README.md) for complete documentation.

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-plugin`)
3. Make your changes
4. Run validation (`/plugin-development:validate`)
5. Commit your changes (`git commit -m 'Add amazing plugin'`)
6. Push to the branch (`git push origin feature/amazing-plugin`)
7. Open a Pull Request

## Acknowledgments

Built for [Claude Code](https://claude.com/claude-code) by [Anthropic](https://www.anthropic.com/).

## License

MIT License — see [LICENSE](LICENSE) for details.

## Resources

- [Claude Code Documentation](https://code.claude.com/docs)
- [Anthropic Discord](https://discord.com/invite/anthropic) — Community support
- [Claude Code GitHub](https://github.com/anthropics/claude-code) — Official repository
