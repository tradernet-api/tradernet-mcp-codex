<div align="center">

# Tradernet MCP for Codex

**Connect [OpenAI Codex](https://openai.com/codex) to the Tradernet API over [Model Context Protocol](https://modelcontextprotocol.io/).**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.0.0-green.svg)](.codex-plugin/plugin.json)
[![MCP](https://img.shields.io/badge/MCP-Streamable%20HTTP-0A84FF.svg)](https://modelcontextprotocol.io/)
[![Platform](https://img.shields.io/badge/platform-OpenAI%20Codex-0A84FF.svg)](https://openai.com/codex)
[![GitHub stars](https://img.shields.io/github/stars/tradernet-api/tradernet-mcp-codex?style=social)](https://github.com/tradernet-api/tradernet-mcp-codex/stargazers)

[Tradernet API docs](https://tradernet.com/tradernet-api/mcp) ·
[Report issue](https://github.com/tradernet-api/tradernet-mcp-codex/issues)

</div>

---

> **Warning:** Tradernet MCP operates against a **real Tradernet account**. Orders, tariffs, uploads, watchlist edits, alerts, and account-switching actions can affect real account state and money. Use a dedicated test account.

## Overview

Tradernet MCP for Codex connects Codex to the Tradernet API through Model Context Protocol over **Streamable HTTP** (`https://tradernet.com/mcp/tn`).

The plugin exposes ~**61 tools** for quotes, historical candles, market references, news, portfolio reads, orders, reports, tariffs, watchlists, alerts, and SID-based session utilities — with safety guidance baked into the bundled skill.

## Features

| Capability | Description |
|------------|-------------|
| **Codex plugin** | `.codex-plugin/plugin.json` manifest with MCP server reference |
| **MCP config** | `.mcp.json` — `TN` endpoint with `env_http_headers` (no hardcoded secrets) |
| **Skill** | `tradernet-mcp` — workflows, SID refresh, read vs write/trade safety |
| **Env-only secrets** | `TN_API_KEY`, `TN_API_SECRET`, optional `TN_LOGIN` / `TN_PASSWORD` / `TN_SID` |

## Table of contents

- [Contents](#contents)
- [Requirements](#requirements)
- [Secrets](#secrets)
- [SID cache](#sid-cache)
- [MCP configuration](#mcp-configuration)
- [Safety model](#safety-model)
- [Installation](#installation)
- [Development](#development)
- [Related integrations](#related-integrations)
- [License](#license)

## Contents

| File | Purpose |
|------|---------|
| `.codex-plugin/plugin.json` | Codex plugin manifest |
| `.mcp.json` | Codex MCP server config for `TN` |
| `skills/tradernet-mcp/SKILL.md` | Codex workflow, SID refresh, and safety guidance |

## Requirements

1. A Tradernet account with API access.
2. A Tradernet API key pair from [tradernet.com/tradernet-api/auth-api](https://tradernet.com/tradernet-api/auth-api).
3. [Codex](https://openai.com/codex) with plugin and MCP support.
4. Environment variables for credentials.

## Secrets

Do not put secrets in the repo or in plugin files. Use environment variables:

```bash
export TN_API_KEY="your-api-key"
export TN_API_SECRET="your-api-secret"
export TN_LOGIN="user@example.com"
export TN_PASSWORD="your-password"
```

Recommended local file:

```bash
mkdir -p ~/.config/tn-mcp
chmod 700 ~/.config/tn-mcp
```

`~/.config/tn-mcp/credentials.env`:

```bash
export TN_API_KEY="your-api-key"
export TN_API_SECRET="your-api-secret"
export TN_LOGIN="user@example.com"
export TN_PASSWORD="your-password"
```

Use mode `600` for the file:

```bash
chmod 600 ~/.config/tn-mcp/credentials.env
```

## SID cache

`TN_SID` is optional. Most HMAC tools use `TN_API_KEY` and `TN_API_SECRET`.

For Codex-local SID caching, use:

```bash
~/.codex/tn-mcp/session.env
```

with:

```bash
export TN_SID="session-id"
```

The SID is a live session token. Keep the file local and use mode `600`.

## MCP configuration

The plugin ships this Codex MCP server config:

```json
{
  "mcpServers": {
    "TN": {
      "url": "https://tradernet.com/mcp/tn",
      "env_http_headers": {
        "X-TN-API-Key": "TN_API_KEY",
        "X-TN-API-Secret": "TN_API_SECRET",
        "X-TN-SID": "TN_SID"
      }
    }
  }
}
```

The config contains only environment variable names. It does not contain credentials.

## Safety model

Read-only tools can run when requested:

- quotes, historical candles, news, market status
- portfolio reads, report reads, tariff reads

Write or trade tools require explicit confirmation:

- placing or canceling orders, changing tariffs
- uploading files, editing alerts or watchlists
- switching connected users, login or SMS authentication

See `skills/tradernet-mcp/SKILL.md` for the full runbook.

## Installation

```bash
git clone https://github.com/tradernet-api/tradernet-mcp-codex.git
# Install as a local Codex plugin (path depends on your Codex setup)
```

1. Export credentials (`~/.config/tn-mcp/credentials.env`).
2. Install/enable the plugin in Codex.
3. Verify the `TN` MCP server is connected.
4. Try a read-only call: `reference_get_market_status` → `quotes_get` → `portfolio_get`.

## Development

Validate the plugin before publishing or installing:

```bash
python3 ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py /path/to/tradernet-mcp-codex
```

## Related integrations

| Client | Repository | What you get |
|--------|------------|--------------|
| **Cursor** | [tradernet-mcp](https://github.com/tradernet-api/tradernet-mcp) | Full plugin: rules, commands, hook, skill |
| **Claude Desktop** | [tradernet-mcp-claude](https://github.com/tradernet-api/tradernet-mcp-claude) | `mcp-remote` config + optional SID skill |
| **Codex** (this repo) | [tradernet-mcp-codex](https://github.com/tradernet-api/tradernet-mcp-codex) | Codex plugin with MCP config and safety skill |

## License

[MIT](LICENSE) © Tradernet
