---
name: global-project-manager
description: |
  Centralized task and project management with local storage (.claude/global-project/) and optional S3/Minio synchronization.

  AUTOMATIC TRIGGER: Use this skill whenever a task is requested or a new request/question is submitted (considered as a new task).

  Features: task creation/modification, timestamped status management (backlog, todo, in_progress, done, cancelled), multi-project tracking, separate history to save tokens, optional S3 sync, auto-commit with jj on task completion.

  Supported queries: "What is the current task?", "What are the pending tasks?", "Tasks completed this week?", "Projects in progress?", "Status of project X?"
---

# Global Project Manager

## File Structure

```
.claude/global-project/
├── project.md              # Project metadata (lightweight)
├── project_history.md      # Project history
└── tasks/
    ├── task-001.md         # Task (lightweight)
    ├── task-001-history.md # Task history
    └── ...
```

## Project Initialization

If `.claude/global-project/` does not exist, create:

1. The directory `.claude/global-project/tasks/`
2. `project.md` with:
   - `shortname`: directory name in kebab-case (e.g., `NEW_ACCRED` → `new-accred`)
   - `git_repo`: extract URL from `.git/config` if present
   - `jj_repo: true` if `.jj/` exists
3. `project_history.md` with `created` entry

## Task Management

### Create a Task

1. Read existing `task-*.md` files to determine the next ID
2. Create `task-XXX.md` (see [schema.md](references/schema.md))
3. Create `task-XXX-history.md` with `created` entry
4. Add `task_added` entry in `project_history.md`

### Update a Task

1. Modify the frontmatter of `task-XXX.md`
2. Add entry in `task-XXX-history.md`:
   - `status_change | old → new`
   - `priority_change | old → new`
3. Update timestamps:
   - `started_at` when transitioning to `in_progress`
   - `completed_at` when transitioning to `done` or `cancelled`
4. If transitioning to `done` or `cancelled` and `jj_repo: true` in `project.md`:
   - Commit changes using jj with message: `{task_title}` (the task description from `task-XXX.md`)
   - Use: `jj new --allow-empty -m "{task_title}"`

### List Tasks

Read `task-*.md` files (not `-history.md`) and filter by:
- `status`: backlog, todo, in_progress, done, cancelled
- `completed_at` for tasks from this week

## Statuses

| Status | Description |
|--------|-------------|
| `backlog` | Future idea, not planned |
| `todo` | Planned, ready |
| `in_progress` | In progress |
| `done` | Completed |
| `cancelled` | Cancelled |

## Priorities

`low`, `medium`, `high`, `critical`

## S3 Synchronization (optional)

If environment variables are defined (`MINIO_ENDPOINT`, `MINIO_ACCESS_KEY`, `MINIO_SECRET_KEY`), synchronize files to `s3://global_projects/{shortname}/`.

## Detailed Formats

See [references/schema.md](references/schema.md).
