# Meridian

**Claude Code has no memory between sessions. Meridian fixes that.**

Meridian is an open-source MCP server that gives your AI coding sessions shared persistent memory: a task log, pinned architectural decisions, sprint items, and a goal state that survives context resets. When a session fills up, it generates a tiered handoff that a new session resumes from in seconds.

---

## The problem

Every AI coding session boots blind. You explain your architecture, your constraints, your decisions — and when the context window fills, it's gone. CLAUDE.md is a workaround, not a solution. Copy-pasting summaries by hand doesn't scale.

This is **context debt** — and it compounds.

## The solution

Meridian is a local MCP server every AI session connects to. Sessions share goal state, see each other's task log, and resume from a compressed handoff when context fills up.

```
> start_session(project_id="my-app", session_name="feature-x")
  ✓ session registered · sprint loaded · 12 active tasks

> get_tasks(project_id="my-app", limit=5)
  [DONE]    backend / wire decisions table
  [PENDING] frontend / add notes vtab (claimed by session-2)

> claim_task(task_id="a1f3...")
  ✓ claimed — other sessions skip this one
```

## Key features

- **Persistent goal state** — North star, sprint focus, and content survive context resets
- **Shared task log** — All sessions see what's been done, claimed, or failed
- **Pinned decisions** — Architectural choices stay visible to every session
- **Human-in-the-loop** — Sessions can pause and surface questions to you
- **Tiered handoffs** — Compressed context documents for fast session resumption
- **Dashboard** — Web UI at `http://localhost:7878` showing all sessions, tasks, and sprint items
- **Model-agnostic** — Works with any MCP client: Claude Code, Cursor, Cline, Claude Desktop, LangGraph

## Quick links

- [Quickstart →](quickstart.md)
- [MCP Tools reference →](mcp-tools.md)
- [Hosted tier →](hosted.md)
- [Self-hosting →](self-hosting.md)
- [Configuration reference →](configuration.md)
- [GitHub](https://github.com/meridianmcp/Meridian)
- [Hosted at usemeridian.us](https://usemeridian.us)
