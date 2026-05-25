# Hosted Tier

[usemeridian.us](https://usemeridian.us) is a fully managed Meridian instance — no infrastructure to run, no database to provision.

---

## Plans

| | Standard | Pro |
|---|---|---|
| **Price** | $20/mo | $49/mo (waitlist) |
| **Storage** | 1 GB included | 10 GB included |
| **Compute** | 2 CU · 100 hrs/mo | 4 CU · 300 hrs/mo |
| **Environments** | 1 | prod / staging / dev |
| **Bring your own Postgres** | ✓ | ✓ |
| **OAuth + email magic link** | ✓ | ✓ |
| **Extra storage** | $0.50 / GB-month | $0.50 / GB-month |
| **Support** | Email | Priority |

7-day free trial on Standard. Card required, no charge until day 8.

---

## Getting started

1. Sign up at [usemeridian.us](https://usemeridian.us)
2. Verify your email via magic link
3. Copy your bearer token from the dashboard

---

## Wire it into your AI client

```json
{
  "mcpServers": {
    "meridian": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://usemeridian.us/mcp"],
      "env": {
        "BEARER_TOKEN": "sk_meridian_..."
      }
    }
  }
}
```

Add this to:

- **Claude Code**: `.mcp.json` in your project root
- **Claude Desktop**: `claude_desktop_config.json` (Windows: `%APPDATA%\Claude\`, macOS: `~/Library/Application Support/Claude/`)
- **Cursor / Windsurf**: `.mcp.json` in project root

---

## Authentication methods

Meridian hosted supports two sign-in methods:

- **Email magic link** — enter your email, get a one-time link
- **Google OAuth** — one-click sign-in with your Google account
- **GitHub OAuth** — one-click sign-in with your GitHub account

---

## Database backend

Each Standard tenant gets a dedicated [Neon Postgres](https://neon.tech) database provisioned automatically at sign-up. Your data is isolated — no shared tables.

You can also bring your own Postgres URL in account settings.

---

## Try the demo

[usemeridian.us/demo](https://usemeridian.us/demo) — a read-only demo project showing what a live Meridian instance looks like with real data.

---

## Billing and cancellation

Billing is managed through Stripe. Cancel anytime from account settings. Data export is available on request at [hello@usemeridian.us](mailto:hello@usemeridian.us).
