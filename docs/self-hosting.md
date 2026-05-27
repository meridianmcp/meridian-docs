# Self-Hosting

Run Meridian on your own machine or server. Your data never leaves your infrastructure.

---

## Local development (pixi)

The fastest path — pixi manages the Python environment automatically.

```bash
git clone https://github.com/meridianmcp/Meridian
cd Meridian
pixi run start
```

Dashboard at **http://localhost:7878**. Data in `./data/meridian.db`.

To run the MCP stdio server (for Claude Code / Claude Desktop / Codex CLI):

```bash
pixi run python -m meridian --mcp
```

---

## Docker Compose

```bash
git clone https://github.com/meridianmcp/Meridian
cd Meridian
docker compose up
```

The `docker-compose.yml` mounts `./data` for persistence and exposes port 7878.

To use Postgres instead of SQLite, set `MERIDIAN_DB_URL` in `docker-compose.yml`:

```yaml
environment:
  MERIDIAN_DB_URL: postgresql://user:pass@host/meridian
```

---

## Manual install (pip)

```bash
git clone https://github.com/meridianmcp/Meridian
cd Meridian
pip install -e .
python -m meridian
```

Or with a custom port:

```bash
python -m meridian --port 8080
```

---

## Using Postgres (team setup)

Point multiple team members at a shared Postgres database:

```bash
export MERIDIAN_DB_URL=postgresql://user:pass@your-host/meridian
python -m meridian
```

Each teammate runs their own local Meridian server pointing at the same DB. Sessions, tasks, and decisions are instantly shared — no Meridian server in the cloud required.

[Neon free tier](https://neon.tech) works great for this.

---

## Production deployment

### Fly.io

The Meridian repo includes `fly.toml`. To deploy:

```bash
fly launch --no-deploy
fly secrets set MERIDIAN_DB_URL=postgresql://...
fly secrets set ANTHROPIC_API_KEY=sk-ant-...
fly deploy
```

### Environment variables

See the [Configuration reference](configuration.md) for all available options.

---

## Windows notes

Meridian fully supports Windows. The server uses `SelectorEventLoop` (required for psycopg3) and avoids `asyncio.run()` on Windows. No special setup needed — just run the commands above in PowerShell.

The MCP stdio server also works on Windows via Claude Code:

```json
{
  "mcpServers": {
    "meridian": {
      "command": "pixi",
      "args": ["run", "python", "-m", "meridian", "--mcp"],
      "cwd": "C:\\path\\to\\Meridian"
    }
  }
}
```
