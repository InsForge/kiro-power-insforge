# InsForge Kiro Power

The backend built for AI-assisted development. Manage PostgreSQL database, file storage, edge functions, and authentication through MCP.

## Installation

Add to your Kiro Powers configuration or install via the Kiro Powers UI.

## Configuration

Requires InsForge API key and instance URL:

```json
{
  "mcpServers": {
    "insforge": {
      "command": "npx",
      "args": ["-y", "@insforge/mcp@latest"],
      "env": {
        "API_KEY": "${INSFORGE_API_KEY}",
        "API_BASE_URL": "${INSFORGE_URL}"
      }
    }
  }
}
```

## Getting Started

1. Create an InsForge account at [insforge.dev](https://insforge.dev) or self-host
2. Get your API key from the dashboard
3. Install this power
4. Say "Get started with InsForge" to begin

## Documentation

- [POWER.md](POWER.md) - Full tool reference and workflows
- [steering/](steering/) - AI guidance files

## Links

- [InsForge Documentation](https://insforge.dev/docs)
- [GitHub](https://github.com/insforge/insforge)
- [Discord](https://discord.gg/insforge)

## License

MIT
