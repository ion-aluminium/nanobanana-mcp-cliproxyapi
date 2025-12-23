# CLIProxyAPI Backend Guide

This fork adds **CLIProxyAPI** support so the MCP server can call Gemini-compatible
endpoints through a local proxy (no Google SDK required).

## When to Use CLIProxyAPI

- You run CLIProxyAPI locally and want to route Gemini requests through it.
- You prefer to manage keys in CLIProxyAPI rather than setting `GEMINI_API_KEY`.

## Requirements

- CLIProxyAPI running and reachable (e.g. `http://127.0.0.1:8318`).
- An API key configured in CLIProxyAPI.

## Environment Variables

Required:

- `CLIPROXY_BASE_URL` - Base URL of CLIProxyAPI (for example: `http://127.0.0.1:8318`).

Optional (one of these is recommended):

- `CLIPROXY_API_KEY` - API key used in the `Authorization: Bearer` header.
- `CLIPROXY_CONFIG` - Path to CLIProxyAPI `config.yaml`; the server will read
  the first key from `api-keys`.

Other useful settings:

- `NANOBANANA_MODEL=auto|flash|pro`
- `IMAGE_OUTPUT_DIR=/path/to/output`
- `NO_PROXY=127.0.0.1,localhost` (recommended when CLIProxyAPI is local)

## Example: Claude Desktop

```json
{
  "mcpServers": {
    "nanobanana": {
      "command": "uvx",
      "args": ["nanobanana-mcp-server@latest"],
      "env": {
        "CLIPROXY_BASE_URL": "http://127.0.0.1:8318",
        "CLIPROXY_API_KEY": "sk-your-cli-proxy-key",
        "NANOBANANA_MODEL": "pro",
        "NO_PROXY": "127.0.0.1,localhost"
      }
    }
  }
}
```

## Example: Local Source Run

```bash
export CLIPROXY_BASE_URL="http://127.0.0.1:8318"
export CLIPROXY_API_KEY="sk-your-cli-proxy-key"
export NANOBANANA_MODEL="pro"
export NO_PROXY="127.0.0.1,localhost"

uv run python -m nanobanana_mcp_server.server
```

## Behavior Differences (CLIProxyAPI Mode)

- Uses CLIProxyAPI's Gemini-compatible `v1beta/models/{model}:generateContent`.
- `upload_file` and Gemini Files API features are **disabled** in this mode.
- Local image editing via `input_image_path_*` still works (images are inlined).

## Troubleshooting

- **401 Unauthorized**: CLIProxy API key missing/invalid.
- **404 / Connection Errors**: CLIProxyAPI not running or wrong base URL.
- **Proxy Loop**: Add `NO_PROXY=127.0.0.1,localhost` when CLIProxyAPI is local.
