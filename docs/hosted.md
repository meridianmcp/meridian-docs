# Hosted Tier

[usemeridian.us](https://usemeridian.us) is a fully managed Meridian instance — no infrastructure to run, no database to provision.

---

## Plans

| | Standard | Pro |
|---|---|---|
| **Price** | $20/mo | $49/mo |
| **Storage** | 1 GB included | 10 GB included |
| **Compute** | 50 CU-hours/mo included | 200 CU-hours/mo included |
| **Team members** | Up to 25 | Up to 50 |
| **Environments** | 1 | prod / staging / dev |
| **Bring your own Postgres** | ✓ | ✓ |
| **OAuth + email magic link** | ✓ | ✓ |
| **Compute overage** | $0.16/CU-hr | $0.16/CU-hr |
| **Storage overage** | $0.50/GB-month | $0.50/GB-month |
| **Support** | Email | Priority |

7-day free trial on both plans. Card required, no charge until day 8.

---

## Resource Limits & Overage

### Compute (CU-hours)

| Plan | Included | Grace Period | Overage Rate |
|------|----------|--------------|--------------|
| Standard | 50 CU-hours/month | +20 CU-hours | $0.16/CU-hour |
| Pro | 200 CU-hours/month | +20 CU-hours | $0.16/CU-hour |

**What is a CU-hour?**
1 CU = 1 vCPU + 4 GB RAM. Neon autoscales your database between 0.25 CU (minimum)
and 2 CU (maximum) based on load. You're charged per CU-hour of actual usage,
not provisioned capacity. When your database is idle (sleeping), you're charged $0.

**The flow when you hit your limit:**

1. At 50 CU-hours (Standard): database throttled to 0.25 CU maximum. Warning email sent.
2. At 70 CU-hours (after 20-hour grace period): overage billing starts at $0.16/CU-hour
3. Overage charged on your next monthly invoice via Stripe

**At 0.25 CU, how long until overage kicks in?**

- 0.25 CU × 80 hours = 20 CU-hours of grace
- Running 24/7 at 0.25 CU: ~11.7 days before overage billing starts
- Typical developer (4 active hours/day at 0.25 CU): months before hitting overage

**Optional overage cap:**

Set a monthly overage budget in Settings → Usage. We never charge more than your cap.
Default: no cap (pay what you use). Set to $0 to prevent any overage charges
(database stays throttled at 0.25 CU instead).

**Note:** $0.25/month minimum compute charge applies even for idle databases on the
Standard plan (0.25 CU × ~2 hours/month average wake time). This is Neon's
minimum billing, not a Meridian fee.

### Storage

| Plan | Included | Overage Rate |
|------|----------|--------------|
| Standard | 1 GB/month | $0.50/GB-month |
| Pro | 10 GB/month | $0.50/GB-month |

At the storage limit with no cap set: database becomes read-only (writes return 507).
With a cap set: billed up to cap, then read-only.

### Monitoring your usage

Dashboard → Settings → Usage tab shows:

- Current month compute usage vs limit (progress bar)
- Current month storage usage vs limit (progress bar)
- Your overage caps (editable)
- Estimated overage charge for current month

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
