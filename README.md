# Claude Code LSP Plugins

A collection of Language Server Protocol (LSP) plugins for [Claude Code](https://claude.com/claude-code), Anthropic's official CLI tool.

## What is LSP?

Language Server Protocol (LSP) provides Claude Code with deep code intelligence capabilities, similar to what an IDE offers. Instead of searching your codebase with grep/glob, Claude can:

- **Go to Definition**: Jump directly to where a function/variable is defined
- **Find References**: Locate all usages of a symbol across your entire project
- **Get Type Information**: Understand types, interfaces, and class hierarchies
- **Real-time Diagnostics**: See errors and warnings immediately

### Before vs After LSP

| Task | Without LSP | With LSP |
|------|-------------|----------|
| "Where is `handleSubmit` defined?" | Grep through all files | Direct jump to exact file and line |
| "What uses this function?" | Text search (may miss imports) | Complete list of all references |
| "What type does this return?" | Guess from context | Exact type information |

## Available Plugins

| Plugin | Languages | Required Server |
|--------|-----------|-----------------|
| `typescript-lsp` | TypeScript, JavaScript, TSX, JSX | `typescript-language-server` |

## Installation

### Prerequisites

1. **Claude Code 2.0.74+** - Check with `claude --version`
2. **Language Server** - Install the required server for your language

#### Install TypeScript Language Server

```bash
npm install -g typescript-language-server typescript
```

### Install the Plugin

#### Option 1: Via CLI (Recommended)

```bash
claude plugin install typescript-lsp@rodrigo-kiko/claude-lsp-plugins --scope user
```

#### Option 2: Manual Configuration

Add to your `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "rodrigo-lsp": {
      "source": {
        "source": "github",
        "repo": "rodrigo-kiko/claude-lsp-plugins"
      }
    }
  },
  "enabledPlugins": {
    "typescript-lsp@rodrigo-lsp": true
  }
}
```

### Verify Installation

After restarting Claude Code, the LSP plugin will be loaded automatically. You can test it by asking Claude:

> "Find all references to [function name] in my project"

Claude should use LSP to find references precisely instead of using grep.

## How It Works

```
┌─────────────────┐                      ┌──────────────────────────┐
│   Claude Code   │ ←── LSP Protocol ──→ │ typescript-language-server│
│                 │                      │                          │
│  "Find where    │                      │  "handleSubmit is at     │
│   handleSubmit  │                      │   src/components/Form.tsx │
│   is defined"   │                      │   line 42, column 10"    │
└─────────────────┘                      └──────────────────────────┘
```

The plugin acts as a bridge between Claude Code and the language server. It doesn't contain any executable code - just configuration files that tell Claude Code:

1. **Which command to run** (`typescript-language-server`)
2. **How to communicate** (`--stdio`)
3. **Which files it handles** (`.ts`, `.tsx`, `.js`, `.jsx`)

## Plugin Structure

```
claude-lsp-plugins/
├── README.md
├── LICENSE
├── .claude-plugin/
│   └── marketplace.json       # Plugin registry
└── typescript-lsp/
    ├── .claude-plugin/
    │   └── plugin.json        # Plugin metadata
    └── .lsp.json              # LSP server configuration
```

## Creating Your Own LSP Plugin

You can add support for other languages by creating a new plugin folder:

```
my-language-lsp/
├── .claude-plugin/
│   └── plugin.json
└── .lsp.json
```

### plugin.json

```json
{
  "name": "my-language-lsp",
  "description": "LSP support for My Language",
  "version": "1.0.0"
}
```

### .lsp.json

```json
{
  "mylang": {
    "command": "my-language-server",
    "args": ["--stdio"],
    "extensionToLanguage": {
      ".mylang": "mylanguage"
    }
  }
}
```

## Troubleshooting

### "Executable not found in $PATH"

The language server binary is not installed. Install it first:

```bash
# For TypeScript
npm install -g typescript-language-server typescript

# For Python (Pyright)
npm install -g pyright

# For Go
go install golang.org/x/tools/gopls@latest
```

### Plugin not loading

1. Verify Claude Code version: `claude --version` (needs 2.0.74+)
2. Restart Claude Code after configuration changes
3. Check `~/.claude/settings.json` for syntax errors

### LSP not being used

Enable LSP logging to debug:

```bash
claude --enable-lsp-logging
```

Logs will be saved to `~/.claude/debug/`

## Contributing

Contributions are welcome! Feel free to:

- Add new language plugins
- Improve documentation
- Report issues

## License

MIT License - See [LICENSE](LICENSE) file for details.

## Author

Created by [Rodrigo Pinto](https://github.com/rodrigo-kiko)

---

**Note**: This is a community plugin, not officially maintained by Anthropic.
