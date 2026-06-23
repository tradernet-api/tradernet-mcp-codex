# Tradernet MCP for Codex

Tradernet MCP for Codex connects Codex to the Tradernet API through Model Context Protocol over Streamable HTTP.

The plugin exposes Tradernet tools for quotes, historical candles, market references, news, portfolio reads, orders, reports, tariffs, watchlists, alerts, and SID-based session utilities.

> Tradernet MCP operates against a real Tradernet account. Orders, tariffs, uploads, watchlist edits, alerts, and account-switching actions can affect real account state.

## Contents

| File | Purpose |
|---|---|
| `.codex-plugin/plugin.json` | Codex plugin manifest |
| `.mcp.json` | Codex MCP server config for `TN` |
| `skills/tradernet-mcp/SKILL.md` | Codex workflow, SID refresh, and safety guidance |
| `assets/logo.svg` | Plugin logo |

## Requirements

1. A Tradernet account with API access.
2. A Tradernet API key pair from `https://tradernet.com/tradernet-api/auth-api`.
3. Codex with plugin and MCP support.
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

## SID Cache

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

## MCP Configuration

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

## Safety Model

Read-only tools can run when requested:

- quotes
- historical candles
- news
- market status
- portfolio reads
- report reads
- tariff reads

Write or trade tools require explicit confirmation:

- placing or canceling orders
- changing tariffs
- uploading files
- editing alerts
- editing watchlists
- switching connected users
- login or SMS authentication

## Development

Validate the plugin before publishing or installing:

```bash
python3 ~/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py /Users/morg01hgmail.com/PycharmProjects/tradernet-mcp-codex
```

## License

MIT.
