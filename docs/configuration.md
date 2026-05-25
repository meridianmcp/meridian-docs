# Configuration Reference

All configuration is done through environment variables. No config files are required for basic use.

---

## Core settings

| Variable | Default | Description |
|---|---|---|
| `MERIDIAN_HOST` | `127.0.0.1` | HTTP server bind address |
| `MERIDIAN_PORT` | `7878` | HTTP server port |
| `MERIDIAN_DATA_DIR` | `./data` | Directory for SQLite DB and handoff files |
| `MERIDIAN_VERSION` | (from package) | Version string shown in dashboard header |

---

## Database

| Variable | Default | Description |
|---|---|---|
| `MERIDIAN_DB_URL` | _(none)_ | Postgres connection URL. When set, Postgres is used instead of SQLite. Format: `postgresql://user:pass@host/dbname` |
| `MERIDIAN_DB` | `data/meridian.db` | SQLite database path (used when `MERIDIAN_DB_URL` is not set) |

**SQLite** is the default and works well for single-user local installs.

**Postgres** is required for team setups (multiple users sharing a DB) and for the hosted tier.

---

## AI integration

| Variable | Default | Description |
|---|---|---|
| `ANTHROPIC_API_KEY` | _(none)_ | Enables AI-generated handoff summaries and chat. Without this, handoffs still generate but without the L2 AI summary layer. |

---

## Access control

| Variable | Default | Description |
|---|---|---|
| `SITE_PASSWORD` | _(none)_ | When set, a password gate protects the dashboard. Users must enter this password before accessing any project data. Remove on launch day. |
| `SESSION_SECRET` | _(auto-generated)_ | Secret key for signing session cookies. Set explicitly in production to survive restarts. |

---

## Demo mode

| Variable | Default | Description |
|---|---|---|
| `MERIDIAN_DEMO` | `0` | Set to `1` to enable demo mode UI indicators. |
| `MERIDIAN_DEMO_DB_URL` | _(none)_ | Postgres URL for the read-only demo database. When set, requests with the `meridian_demo` cookie are routed here. |

---

## Hosted tier (self-hosted Meridian-as-a-service)

These are only needed if you are running Meridian as a hosted multi-tenant service (like usemeridian.us). For personal or team use, ignore these.

| Variable | Default | Description |
|---|---|---|
| `MERIDIAN_HOSTED` | `0` | Enable hosted-mode features (OAuth, Stripe, per-tenant DB provisioning) |
| `STRIPE_API_KEY` | _(none)_ | Stripe secret key for billing |
| `STRIPE_WEBHOOK_SECRET` | _(none)_ | Stripe webhook signing secret |
| `MERIDIAN_SERVER_URL` | _(none)_ | Public URL of the Meridian instance (used in emails and OAuth redirects) |
| `ADMIN_EMAIL` | _(none)_ | Admin email for receiving error alerts |

---

## Example: local development

No env vars needed — just run:

```bash
pixi run start
```

---

## Example: team Postgres setup

```bash
export MERIDIAN_DB_URL=postgresql://alice:pass@neon.tech/meridian
export ANTHROPIC_API_KEY=sk-ant-...
python -m meridian
```

---

## Example: production (Fly.io secrets)

```bash
fly secrets set MERIDIAN_DB_URL=postgresql://...
fly secrets set ANTHROPIC_API_KEY=sk-ant-...
fly secrets set SESSION_SECRET=$(openssl rand -hex 32)
```
