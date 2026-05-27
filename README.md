# tenomcp-deploy

Public deploy manifest for Tenoro's Reevo MCP service.

This repo holds **only the Docker Compose file** Hostinger Docker Manager fetches to deploy the Reevo MCP. The actual source code is private at <https://github.com/valenzuela-gtm/tenoro-hq>. The built container image lives at <https://ghcr.io/valenzuela-gtm/reevo-mcp> (also public — image bytes only).

## What's here

- `compose.yml` — Docker Compose for Reevo MCP + Nginx Proxy Manager.

## Deploy

In Hostinger Docker Manager → Compose → **Compose from URL**, paste:

```text
https://raw.githubusercontent.com/valenzuela-gtm/tenomcp-deploy/main/compose.yml
```

Then set environment variables `REEVO_API_KEY` and `MCP_BEARER_TOKEN` in the Hostinger UI before deploying.

### Optional: OAuth login (WorkOS AuthKit)

To let Claude web/mobile, ChatGPT, and other clients connect with a login flow
instead of pasting the bearer token, set these in the Hostinger UI as well:

```text
OAUTH_ENABLED=true
AUTHKIT_DOMAIN=https://<your-app>.authkit.app   # from WorkOS dashboard
WORKOS_CLIENT_ID=client_...                       # from WorkOS dashboard
MCP_RESOURCE_URL=https://reevomcp.galapago.cloud/mcp
```

No WorkOS API key is required — tokens are verified locally against AuthKit's
public keys. The static `MCP_BEARER_TOKEN` keeps working in parallel for CLI
clients. Leave `OAUTH_ENABLED` unset (or `false`) to run bearer-token-only.

Full deploy runbook lives in the private tenoro-hq repo at `13-services/hostinger-stack/README.md`.

## Updating

When MCP code changes on `main` in `tenoro-hq`:

1. GitHub Actions builds and publishes a new image to `ghcr.io/valenzuela-gtm/reevo-mcp:latest` automatically.
2. Click **Redeploy** in Hostinger Docker Manager to pull the new image.

If `compose.yml` itself needs to change, update it in `tenoro-hq/13-services/hostinger-stack/compose.yml` first (so the canonical version stays in sync), then copy the new version here.
