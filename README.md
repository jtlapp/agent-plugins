# Agent Plugins

A marketplace for AI agent plugins, including Claude Code skills, MCP servers, and other agent extensions.

## Structure

Each plugin lives in its own directory under `plugins/`:

```
plugins/
  my-plugin/
    README.md        # Description, usage, and installation instructions
    plugin.json      # Plugin metadata
    ...              # Plugin files (skills, configs, scripts, etc.)
```

## Plugin Metadata

Each plugin includes a `plugin.json` with the following fields:

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "Short description of what this plugin does",
  "author": "Author Name",
  "license": "MIT",
  "platform": "claude-code",
  "type": "skill",
  "tags": ["productivity", "code-review"]
}
```

### Platform values

| Platform | Description |
|----------|-------------|
| `claude-code` | Claude Code CLI plugins (skills, hooks, MCP configs) |
| `generic` | Platform-agnostic agent plugins |

### Type values

| Type | Description |
|------|-------------|
| `skill` | A slash-command skill for Claude Code |
| `hook` | A hook script triggered by agent events |
| `mcp-server` | An MCP server configuration |
| `bundle` | A collection of multiple plugin types |

## Installation

Refer to each plugin's README for installation instructions.

## Contributing

1. Create a directory under `plugins/` with your plugin name
2. Add a `plugin.json` with the required metadata
3. Add a `README.md` with description and installation instructions
4. Submit a pull request

## License

MIT
