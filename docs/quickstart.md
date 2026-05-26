# Quickstart

Get Meridian running in 60 seconds.

## Local install (self-hosted)

### Prerequisites

- Python 3.12+
- [pixi](https://pixi.sh) (recommended) **or** pip

### Install with pixi

```bash
git clone https://github.com/meridianmcp/Meridian
cd Meridian
pixi run start
```

Dashboard opens at **http://localhost:7878**. Data persists in `./data/meridian.db`.

### Install with pip

```bash
git clone https://github.com/meridianmcp/Meridian
cd Meridian
pip install -e .
python -m meridian
```

### Windows (PowerShell)

```powershell
git clone https://github.com/meridianmcp/Meridian
cd Meridian
.\install.ps1
pixi run start
```

---

## Wire it into your AI client

### Claude Code

Create `.mcp.json` in your project root:

```json
{
  "mcpServers": {
    "meridian": {
      "command": "pixi",
      "args": ["run", "python", "-m", "meridian", "--mcp"],
      "cwd": "/absolute/path/to/Meridian"
    }
  }
}
```

Replace `/absolute/path/to/Meridian` with the actual path where you cloned the repo.

### Claude Desktop

Add to your Claude Desktop config:

- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "meridian": {
      "command": "pixi",
      "args": ["run", "python", "-m", "meridian", "--mcp"],
      "cwd": "/absolute/path/to/Meridian"
    }
  }
}
```

Restart Claude Desktop. New chats will have Meridian tools available.

### Cursor / Windsurf / Cline

Same `.mcp.json` snippet — all three clients read it from the project root.

### Hosted tier (no install)

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

Get your bearer token from [usemeridian.us/dashboard](https://usemeridian.us/dashboard) after sign-in.

---

## First session

Once Meridian is running and wired into your client:

1. **Create a project** in the dashboard at `http://localhost:7878`, or via MCP:

```
create_project(name="my-app")
```

2. **Start a session** at the beginning of every AI chat:

```
start_session(project_id="my-app", session_name="feature-auth", human_id="alice")
```

3. **Log what you do**:

```
log_task(project_id="my-app", session_id="...", description="Added JWT middleware", status="done")
```

4. **Generate a handoff** when context fills up:

```
generate_handoff(project_id="my-app", session_id="...")
```

5. **Resume** in the next session — `start_session` automatically loads the handoff.

---

## Verify it's working

```bash
curl http://localhost:7878/health
# {"status": "ok", "db": "sqlite"}
```

```bash
curl http://localhost:7878/projects
# [{"id": "...", "name": "my-app", ...}]
```

---

## Python SDK

Meridian includes a thin synchronous Python SDK — no extra install needed, it ships with the package.

```python
from meridian.sdk import Meridian

m = Meridian(
    api_key="sk_meridian_...",   # from Settings → MCP setup → Generate API key
    project_id="<uuid>",          # your project ID from the dashboard
    base_url="https://usemeridian.us",  # or your self-hosted URL
)

# Register a session and load shared state
session = m.start_session("my-feature-branch", human_id="alice")

# Log work
m.log_task("Refactored auth module", status="done")

# Get a context block to paste into a new Claude session
print(m.get_context())

# Pin an architectural decision
m.pin_decision("Use psycopg3", "asyncpg has DLL issues on Windows", category="TECHNICAL")

# Request human review
m.request_hitl("Should we add rate limiting here?", urgency="normal")

# Generate a session handoff
m.generate_handoff()
```

The SDK wraps all major MCP tools as Python method calls. For async code, use the MCP stdio/HTTP interface directly.
