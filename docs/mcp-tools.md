# MCP Tools Reference

Meridian exposes 36 MCP tools. Every AI session that connects to Meridian can call these tools directly.

The most common workflow uses a small subset: `start_session` → `log_task` → `generate_handoff`.

---

## Session management

### `start_session`
Single call to start a coordinated session. Registers the session, loads the current goal, and returns the context block — all in one round-trip.

**Parameters:**
- `project_id` (required) — project ID or name
- `session_name` (required) — descriptive label for this session
- `human_id` (optional) — name of the human driving the session

**Returns:** Session ID, goal state, active tasks, sprint items.

---

### `register_session`
Register a session with a project. Lower-level than `start_session`.

**Parameters:**
- `project_id` (required)
- `name` (required)
- `human_id` (optional)

---

### `heartbeat`
Touch session's `last_seen` timestamp to prevent idle expiration. Call every 5–10 minutes in long-running sessions.

**Parameters:**
- `session_id` (required)
- `project_id` (required)

---

### `get_sessions`
List all active sessions connected to a project.

**Parameters:**
- `project_id` (required)

---

## Goal state

### `get_goal`
Read the current goal state for a project: north star, sprint, content, and recent decisions.

**Parameters:**
- `project_id` (required)

---

### `set_goal`
Set or update the project goal content.

**Parameters:**
- `project_id` (required)
- `content` (required) — markdown goal content
- `session_id` (optional)

---

### `set_north_star`
Update the long-lived product vision. Persists independently of the goal content version.

**Parameters:**
- `project_id` (required)
- `north_star` (required)
- `human_id` (optional)

---

### `set_sprint`
Update the short-term sprint focus. Changes each session or week.

**Parameters:**
- `project_id` (required)
- `sprint` (required) — e.g. `"v1.2 — auth + billing"`
- `session_id` (optional)

---

## Context and handoffs

### `get_context_block`
Return a compact plain-text project context block — north star, sprint, recent tasks, active sessions, blocking failures, and pending HITL requests.

**Parameters:**
- `project_id` (required)
- `mode` — `"full"` (default) or `"chat"` (shorter)

---

### `get_session_brief`
Single-call session orientation for worker/automation sessions. Returns an XML-wrapped context envelope under 500 tokens.

**Parameters:**
- `project_id` (required)
- `session_id` (required)

---

### `generate_handoff`
Generate a tiered context handoff file (L0/L1/L2) for the project. The new session calls `start_session` to load it automatically.

**Parameters:**
- `project_id` (required)
- `session_id` (required)
- `detail` — `"l0"`, `"l1"`, or `"l2"` (default: auto)

---

## Task log

### `log_task`
Log what a session completed, is doing, or failed at.

**Parameters:**
- `project_id` (required)
- `session_id` (required)
- `description` (required) — what was done / what's in progress
- `status` — `"done"` | `"in_progress"` | `"pending"` | `"failed"` (default: `"done"`)

---

### `get_tasks`
Get recent tasks across all sessions for a project.

**Parameters:**
- `project_id` (required)
- `limit` — default 20
- `status` — filter by status (optional)

---

### `claim_task`
Atomically claim a pending task. Returns `None` if already claimed by another session. Used in worker/parallel-agent patterns to prevent duplicate work.

**Parameters:**
- `task_id` (required)
- `session_id` (required)

---

### `release_task`
Release a previously claimed task back to `pending`.

**Parameters:**
- `task_id` (required)
- `session_id` (required)

---

### `complete_task`
Mark a claimed task as done, optionally with a completion note.

**Parameters:**
- `task_id` (required)
- `session_id` (required)
- `note` (optional)

---

### `start_worker_session`
Register a worker session and claim a specific task in one atomic call. Used in automated worker pipelines.

**Parameters:**
- `project_id` (required)
- `task_id` (required)
- `session_name` (optional)

---

### `enqueue_claude_task`
Queue a long-running Claude Code subprocess task. The server manages execution.

**Parameters:**
- `project_id` (required)
- `description` (required)
- `session_id` (optional)

---

## Decisions

### `pin_decision`
Create a pinned decision — an editable architectural record that survives handoffs.

**Parameters:**
- `project_id` (required)
- `title` (required)
- `body` (required) — rationale
- `category` — `TECHNICAL` | `ARCHITECTURAL` | `STRATEGIC` | `PRODUCT` | `BUSINESS` | `TACTICAL` | `COMPETITIVE`

---

### `update_decision`
Patch a pinned decision body, title, category, or status.

**Parameters:**
- `project_id` (required)
- `decision_id` (required)
- `body`, `title`, `category`, `status` (all optional)

---

### `get_pinned_decisions`
List pinned decisions. By default returns only active ones.

**Parameters:**
- `project_id` (required)
- `include_superseded` — include superseded decisions (default: false)

---

### `set_decision`
Append a decision to the append-only decisions log (legacy format, stored in the project row).

**Parameters:**
- `project_id` (required)
- `decision` (required)

---

## Sprint board

### `add_sprint_item`
Append a todo item to the sprint checklist.

**Parameters:**
- `project_id` (required)
- `version` (required) — e.g. `"v1.2"`
- `title` (required)
- `item_group` (optional) — grouping label

---

### `get_sprint_items`
List sprint items with optional status and version filters.

**Parameters:**
- `project_id` (required)
- `version` (optional) — filter by version
- `status` (optional) — `"pending"` | `"done"` | `"failed"` | `"skipped"` | `"pushed"`

---

### `complete_sprint_item`
Mark a sprint item done.

**Parameters:**
- `project_id` (required)
- `item_id` (required)

---

### `skip_sprint_item`
Mark a sprint item skipped with a reason.

**Parameters:**
- `project_id` (required)
- `item_id` (required)
- `reason` (optional)

---

### `fail_sprint_item`
Mark a sprint item failed with a reason.

**Parameters:**
- `project_id` (required)
- `item_id` (required)
- `reason` (optional)

---

### `push_sprint_item`
Push a sprint item to a future version.

**Parameters:**
- `project_id` (required)
- `item_id` (required)
- `to_version` (required) — target version string

---

## Human-in-the-loop (HITL)

### `request_hitl`
Surface a question to the human-in-the-loop queue. The session can optionally block until answered.

**Parameters:**
- `project_id` (required)
- `session_id` (required)
- `question` (required)
- `context` (optional) — additional context
- `blocking` — whether to block session until answered (default: false)

---

### `get_hitl_request`
Poll a HITL request to check if the human has answered.

**Parameters:**
- `hitl_id` (required)

---

## Notes

### `add_note`
Add a per-project wiki note.

**Parameters:**
- `project_id` (required)
- `title` (required)
- `body` (required)
- `tags` (optional) — comma-separated tags

---

### `get_notes`
List project notes, newest first. Optional tag filter.

**Parameters:**
- `project_id` (required)
- `tag` (optional)

---

### `delete_note`
Hard-delete a project note.

**Parameters:**
- `project_id` (required)
- `note_id` (required)

---

## Project lookup

### `create_project`
Create a new Meridian project.

**Parameters:**
- `name` (required)
- `human_id` (optional) — creator name

---

### `list_projects`
List all Meridian projects with names and IDs.

---

### `get_project_by_name`
Look up a project by name (case-insensitive substring match).

**Parameters:**
- `name` (required)
