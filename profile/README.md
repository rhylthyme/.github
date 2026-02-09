# Rhylthyme

Rhylthyme is a language and runner for complex schedules

**Live demo:** [rhylthyme.com](https://www.rhylthyme.com)

## Rhylthyme Repositories

| Repository | Description |
|------------|-------------|
| [rhylthyme-spec](https://github.com/rhylthyme/rhylthyme-spec) | JSON Schema specification for Rhylthyme programs |
| [rhylthyme-cli](https://github.com/rhylthyme/rhylthyme-cli) | Command-line tools for validating and running programs |
| [rhylthyme-examples](https://github.com/rhylthyme/rhylthyme-examples) | Example programs (kitchen, lab, manufacturing, events) |

## Features

- **Upload** a local .json or .yaml program file
- **Load from URL** to visualize remote programs
- **AI Assistant** - describe your schedule in plain English and generate a program
- **Interactive timeline** with resource constraints and dependencies

## Installation

```bash
pip install rhylthyme-cli
```

## Usage

### Command Line

```bash
# Generate visualization (opens in browser)
rhylthyme-visualize program.json

# Save to file
rhylthyme-visualize program.json -o output.html --no-browser
```

### MCP Server

Rhylthyme can be used as an MCP tool in Claude Desktop:

```json
{
  "mcpServers": {
    "rhylthyme": {
      "command": "python",
      "args": ["-m", "rhylthyme_web.mcp.server"]
    }
  }
}
```

## License

MIT
