---
title: "Use with AI"
description: "Connect BRC20 docs to your AI tools for free via MCP"
---

The BRC20 documentation exposes an [MCP (Model Context Protocol)](https://modelcontextprotocol.io) server, allowing AI tools like Claude, Cursor, and ChatGPT to query the docs directly — no per-message fees.

## Claude Desktop

Add the following to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "brc20-docs": {
      "type": "http",
      "url": "https://docs.brc20.build/mcp"
    }
  }
}
```

## Cursor

Open the command palette, search for **Open MCP settings**, and add:

```json
{
  "mcpServers": {
    "brc20-docs": {
      "url": "https://docs.brc20.build/mcp"
    }
  }
}
```

## VS Code

Add the following to `.vscode/mcp.json` in your project:

```json
{
  "servers": {
    "brc20-docs": {
      "type": "http",
      "url": "https://docs.brc20.build/mcp"
    }
  }
}
```

## Claude Code

```bash
claude mcp add --transport http brc20-docs https://docs.brc20.build/mcp
```

Once connected, your AI assistant can search and reference the BRC20 documentation in real-time.
