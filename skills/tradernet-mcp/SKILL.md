---
name: tradernet-mcp
description: Use Tradernet MCP tools from Codex safely, including quote and news lookups, portfolio reads, SID refresh, and explicit confirmation for real-account write or trade operations.
---

# Tradernet MCP for Codex

Tradernet MCP gives Codex access to Tradernet API tools over Streamable HTTP. Use this skill whenever the user asks for Tradernet market data, quotes, portfolio reads, orders, reports, tariffs, watchlists, alerts, SID refresh, or MCP connection troubleshooting.

## Safety Defaults

- Treat every Tradernet MCP call as operating against a real Tradernet account.
- Never print, store in the repo, or commit `TN_API_KEY`, `TN_API_SECRET`, `TN_LOGIN`, `TN_PASSWORD`, or `TN_SID`.
- Keep secrets in the user's shell environment or in a local file outside the repo, such as `~/.config/tn-mcp/credentials.env` with mode `600`.
- Before any write, trade, tariff, upload, alert, watchlist, or account-switch action, confirm the exact tool name and material parameters with the user.
- Read-only tools such as quotes, news, market status, historical candles, portfolio reads, and reports can be used directly when the user asks for them.

## Environment

The plugin MCP config reads credentials from HTTP headers populated by environment variables:

```bash
export TN_API_KEY="your-api-key"
export TN_API_SECRET="your-api-secret"
export TN_LOGIN="user@example.com"
export TN_PASSWORD="your-password"
```

`TN_SID` is optional. HMAC tools such as quotes, portfolio, order history, and many reports work with `TN_API_KEY` and `TN_API_SECRET`.

For Codex-local SID caching, prefer:

```bash
~/.codex/tn-mcp/session.env
```

with mode `600` and content:

```bash
export TN_SID="session-id"
```

## SID Refresh Workflow

Use this workflow when the user asks to refresh SID, when a SID tool returns `401 Invalid credentials`, or when `get_sid_info` fails.

1. Verify that `TN_LOGIN` and `TN_PASSWORD` are present in the current environment without printing their values.
2. Call `auth_by_login` with `remember_me: 1`.
3. Validate the returned SID with `get_sid_info`.
4. Store the SID in `~/.codex/tn-mcp/session.env` with directory mode `700` and file mode `600` if the user has approved local SID caching.
5. For the current thread, pass the SID explicitly to SID-based tools when needed.

## Common Read Workflows

For a market snapshot:

- Use `quotes_get` for current quotes.
- Use `quotes_get_hloc` for daily or intraday candles.
- Use `quotes_get_news_list` for ticker news.
- Use `reference_get_options` for the option chain.

For historical candles, remember that Tradernet returns each candle as:

```text
[high, low, open, close]
```

Convert it before presenting OHLC tables.

## Write And Trade Tools

The following tools require explicit user confirmation immediately before use:

- `orders_put`
- `orders_delete`
- `orders_set_stop_loss`
- `tariff_select`
- `alerts_add`
- `alerts_delete`
- `quotes_add_list`
- `quotes_add_list_ticker`
- `quotes_delete_list`
- `quotes_delete_list_ticker`
- `quotes_update_list`
- `quotes_make_list_selected`
- `reports_upload_file`
- `auth_by_login`
- `auth_by_sms`
- `switch_to_connected_user`

For linked-account selectors, verify the target with `list_connected_users_assets` before any write or trade tool. Do not rely on a typo-prone selector.
