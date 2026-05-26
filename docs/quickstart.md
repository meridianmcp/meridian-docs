# Quickstart

Get Meridian running in 60 seconds.

## Installation

### Option A — Hosted (recommended)

Skip installation entirely. Sign up at [usemeridian.us](https://usemeridian.us) for a
7-day free trial. Managed Postgres, hosted dashboard, nothing to install.

### Option B — Self-hosted

#### Prerequisites

**Using pixi (recommended):** No Python version requirement — pixi installs
and manages its own isolated Python environment. Just needs pixi itself.

**Using pip:** Python 3.10+ required (3.12 recommended).

That's it. No other system dependencies needed on macOS or Windows.
Linux users may need `libpq-dev` for Postgres support (see Linux notes below).

#### 1. Install Pixi (package manager)

**macOS / Linux:**

```bash
curl -fsSL https://pixi.sh/install.sh | bash
# Restart your terminal after install
```

**Windows (PowerShell):**

```powershell
iwr -useb https://pixi.sh/install.ps1 | iex
# Restart PowerShell after install
```

Verify:

```bash
pixi --version  # should print pixi 0.x.x
```

#### 2. Clone and start Meridian

```bash
git clone https://github.com/meridianmcp/Meridian.git
cd Meridian
pixi install        # installs all dependencies (Python, psycopg, etc.)
pixi run start      # starts server on localhost:7878
```

Dashboard: **http://localhost:7878/dashboard**  
Data persists in `./data/meridian.db`.

#### 3. Alternative — pip install (no Pixi)

```bash
pip install meridian-mcp
meridian start
```

### Linux notes

On Ubuntu/Debian, you may need:

```bash
sudo apt-get install libpq-dev python3-dev
```

On headless servers (no browser), set:

```bash
export MERIDIAN_HEADLESS=true
pixi run start
# Dashboard available at http://YOUR_SERVER_IP:7878/dashboard
```

### Windows notes

If port 7878 is already in use, Meridian will auto-kill the existing process and restart.
If that fails: `netstat -ano | findstr :7878` then `taskkill /PID <pid> /F`

### Option C — Linux one-liner install

```bash
curl -fsSL https://raw.githubusercontent.com/meridianmcp/Meridian/main/scripts/install.sh | bash
```

Then run:

```bash
meridian
```

Dashboard: **http://localhost:7878/dashboard**

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

1. **Create a project** in the dashboard at `http://localhost:7878/dashboard`, or via MCP:

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

Dashboard: **http://localhost:7878/dashboard**

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
