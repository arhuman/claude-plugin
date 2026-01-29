# File Schema

## project.md

```markdown
---
shortname: new-accred
name: NEW_ACCRED
created_at: 2025-01-27T10:00:00Z
last_sync: 2025-01-27T15:30:00Z
git_repo: git@github.com:user/new-accred.git
jj_repo: true
---

# NEW_ACCRED

Project description...
```

### Fields

| Field | Type | Description |
|-------|------|-------------|
| `shortname` | string | Kebab-case identifier derived from directory name |
| `name` | string | Original directory name |
| `created_at` | ISO 8601 | Creation date |
| `last_sync` | ISO 8601 | Last successful S3 synchronization |
| `git_repo` | string/null | Remote origin URL (from `.git/config`) |
| `jj_repo` | boolean | `true` if `.jj/` exists |

---

## project_history.md

```markdown
# Project History

- 2025-01-27T10:00:00Z | created
- 2025-01-27T12:00:00Z | task_added | task-001
- 2025-01-27T14:00:00Z | task_completed | task-001
- 2025-01-27T15:30:00Z | synced
```

### Entry Format

```
- {timestamp} | {event} | {details}
```

**Events**: `created`, `task_added`, `task_completed`, `task_cancelled`, `synced`, `sync_failed`, `updated`

---

## task-xxx.md

```markdown
---
id: task-001
title: Implement OAuth
status: in_progress
priority: high
created_at: 2025-01-27T10:30:00Z
started_at: 2025-01-27T14:00:00Z
completed_at: null
---

## Description

Implement OAuth2 authentication flow...

## Notes

Additional notes...
```

### Fields

| Field | Type | Values |
|-------|------|--------|
| `id` | string | `task-XXX` (XXX = sequential number) |
| `title` | string | Short task title |
| `status` | enum | `backlog`, `todo`, `in_progress`, `done`, `cancelled` |
| `priority` | enum | `low`, `medium`, `high`, `critical` |
| `created_at` | ISO 8601 | Creation date |
| `started_at` | ISO 8601/null | Date of transition to `in_progress` |
| `completed_at` | ISO 8601/null | Date of transition to `done`/`cancelled` |

---

## task-xxx-history.md

```markdown
# History task-001

- 2025-01-27T10:30:00Z | created
- 2025-01-27T14:00:00Z | status_change | backlog → in_progress
- 2025-01-27T15:00:00Z | note | Started work on the API
- 2025-01-27T18:00:00Z | status_change | in_progress → done
```

### Entry Format

```
- {timestamp} | {event} | {details}
```

**Events**: `created`, `status_change`, `priority_change`, `note`, `title_change`

---

## ID Generation

Task IDs are generated sequentially:
1. Read all existing `task-XXX.md` files
2. Extract the highest number
3. Increment by 1
4. Format with padding: `task-001`, `task-002`, ..., `task-999`

---

## S3 Structure

```
s3://{bucket}/{shortname}/
├── project.md
├── project_history.md
└── tasks/
    ├── task-001.md
    ├── task-001-history.md
    └── ...
```

The `shortname` serves as a prefix to isolate projects within the bucket.
