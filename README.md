# juce-docs-mcp-server

An [MCP](https://modelcontextprotocol.io/) server that gives AI coding assistants access to [JUCE](https://juce.com/) Framework C++ class documentation. Ask your assistant about any JUCE class and it can look up the real docs instead of guessing.

Fork of [josmithiii/mcp-servers-jos](https://github.com/josmithiii/mcp-servers-jos) with runtime docs-source switching and local JUCE docs support.

## What it does

- **Search** JUCE classes by name (`search-juce-classes`)
- **Fetch** full class documentation with methods, parameters, and descriptions (`get-juce-class-docs`)
- **Browse** all available classes (`juce://classes` resource)
- **Switch docs sources** at runtime — official hosted docs (master/develop), a custom URL, or a local JUCE checkout
- **Explore** JUCE interactively with the `explore-juce` prompt (e.g. `explore-juce audio`)

## Quick setup

```bash
# 1. Clone and build
git clone https://github.com/danielraffel/juce-docs-mcp-server.git
cd juce-docs-mcp-server
npm install && npm run build

# 2. Register with your AI client (auto-starts the server when needed)
# Claude Code:
claude mcp add --scope user juce-docs -- node "$(pwd)/dist/index.js"
# Codex:
codex mcp add juce-docs -- node "$(pwd)/dist/index.js"
```

No manual server process needed — the client starts and stops the server automatically.

You only need to run `npm run build` again after pulling or changing the source.

## Docs source options

By default the server fetches from the official JUCE **master** docs. You can switch at any time using MCP tools:

| Source | Tool call |
|---|---|
| Stable (master) | `set-juce-docs-source` with `{ "source": "master" }` |
| Development | `set-juce-docs-source` with `{ "source": "develop" }` |
| Custom URL | `set-juce-docs-source` with `{ "source": "custom-url", "url": "https://..." }` |
| Local JUCE checkout | `setup-local-juce-docs` with `{ "jucePath": "~/Code/JUCE" }` |

Local docs are faster (no network fetches) and follow whatever branch your JUCE checkout is on. Your choice is persisted across sessions in `~/.juce-docs-mcp-server/config.json`.

### Setting the source via environment variables

You can also set the docs source when registering the server, instead of calling tools later:

```bash
# Claude Code — local docs
claude mcp add --scope user juce-docs \
  -e JUCE_DOCS_SOURCE=local-path \
  -e JUCE_DOCS_LOCAL_PATH="$HOME/Code/JUCE/docs/doxygen/doc" \
  -- node "$(pwd)/dist/index.js"

# Codex — local docs
codex mcp add juce-docs \
  --env JUCE_DOCS_SOURCE=local-path \
  --env JUCE_DOCS_LOCAL_PATH="$HOME/Code/JUCE/docs/doxygen/doc" \
  -- node "$(pwd)/dist/index.js"
```

Available environment variables:

| Variable | Description |
|---|---|
| `JUCE_DOCS_SOURCE` | `master`, `develop`, `custom-url`, or `local-path` |
| `JUCE_DOCS_BASE_URL` | URL for `custom-url` source |
| `JUCE_DOCS_LOCAL_PATH` | Path for `local-path` source |
| `JUCE_DOCS_CONFIG_PATH` | Custom config file location (default `~/.juce-docs-mcp-server/config.json`) |

## Cursor setup

1. Open Cursor Settings > MCP
2. Set the **Name** to `JUCE Docs` and **Type** to `Command`
3. Set **Command** to `node /path/to/juce-docs-mcp-server/dist/index.js`
4. Restart Cursor

Cursor starts the server automatically — no separate process needed.

## Visual Studio setup

Visual Studio 2022/2026 with GitHub Copilot supports MCP. In the Copilot Chat window, click the wrench icon, then "+" to add an MCP server. Use this config in `.mcp.json`:

```json
{
  "servers": {
    "juce-docs": {
      "type": "stdio",
      "command": "node",
      "args": ["C:/path/to/juce-docs-mcp-server/dist/index.js"]
    }
  }
}
```

## Available tools

| Tool | Description |
|---|---|
| `search-juce-classes` | Search for JUCE classes by name |
| `get-juce-class-docs` | Get documentation for a specific class |
| `get-juce-docs-config` | Show current docs source configuration |
| `set-juce-docs-source` | Switch docs source (master/develop/custom/local) |
| `setup-local-juce-docs` | Point to a local JUCE checkout and optionally generate docs |

## Development

```bash
# Watch mode (auto-recompile on changes)
npm run dev

# Run tests
npm test

# Start server manually (for debugging)
npm start
```

See [README-DEV.md](./README-DEV.md) for developer notes.

## License

MIT
