# Usage Examples

## Example 1: Creating First Task

**User**: "Let's implement OAuth2 authentication"

**Actions**:
1. Check if `.claude/global-project/` exists → No
2. Create project structure:
   ```bash
   mkdir -p .claude/global-project
   ```
3. Create `project.md`:
   ```yaml
   ---
   shortname: my-app
   name: my-app
   created_at: 2025-01-29T10:00:00Z
   last_sync: null
   git_repo: git@github.com:user/my-app.git
   jj_repo: true
   ---

   # my-app
   ```
4. Create `project_history.md`:
   ```
   # Project History

   - 2025-01-29T10:00:00Z | created
   - 2025-01-29T10:00:00Z | task_added | task-001
   ```
5. Create `claude/global-project/task-001.md`:
   ```yaml
   ---
   id: task-001
   title: Implement OAuth2 authentication
   status: in_progress
   priority: high
   created_at: 2025-01-29T10:00:00Z
   started_at: 2025-01-29T10:00:00Z
   completed_at: null
   ---

   ## Description

   Implement OAuth2 authentication flow with JWT tokens.
   ```
6. Create `task-001-history.md`:
   ```
   # History task-001

   - 2025-01-29T10:00:00Z | created
   - 2025-01-29T10:00:00Z | status_change | backlog → in_progress
   ```
7. Run: `jj new -m "task-001: Implement OAuth2 authentication"`

## Example 2: Marking Task Complete

**User**: "OAuth is done"

**Actions**:
1. Read `task-001.md`
2. Update frontmatter:
   ```yaml
   status: done
   completed_at: 2025-01-29T16:30:00Z
   ```
3. Append to `task-001-history.md`:
   ```
   - 2025-01-29T16:30:00Z | status_change | in_progress → done
   ```
4. Append to `project_history.md`:
   ```
   - 2025-01-29T16:30:00Z | task_completed | task-001
   ```
5. Run: `jj new -m "Implement OAuth2 authentication"`

## Example 3: Listing Tasks

**User**: "What tasks are pending?"

**Response**:
```
In Progress:
- task-002: Database migration (priority: high)

To Do:
- task-003: Add logging (priority: medium)
- task-004: Update docs (priority: low)
```

## Example 4: Creating Detailed Task Structure

**User**: "Start work on payment integration, and I want detailed tracking"

**Actions**:
1. Create task-005.md (standard process)
2. Create detailed directory:
   ```bash
   mkdir -p .claude/global-project/task-005
   ```
3. Create supplemental files:
   - `task-005/overview.md`: Success criteria, acceptance tests
   - `task-005/approach.md`: Stripe vs PayPal analysis, chosen approach
   - `task-005/checklist.md`:
     ```
     - [ ] Setup Stripe account
     - [ ] Implement payment endpoint
     - [ ] Add webhook handling
     - [ ] Write integration tests
     - [ ] Update documentation
     ```
   - `task-005/notes.md`: Empty, for capturing decisions

## Example 5: Handling Priority Changes

**User**: "Make task-003 critical priority"

**Actions**:
1. Read `task-003.md`
2. Update: `priority: medium` → `priority: critical`
3. Append to `task-003-history.md`:
   ```
   - 2025-01-29T11:15:00Z | priority_change | medium → critical
   ```

## Edge Cases

### Multiple In-Progress Tasks

Valid scenario - list all when asked for "current task".

### No JJ Repository

Skip all `jj` commands. Still track everything in markdown files.

### Task Without Detailed Files

Normal - detailed directory (task-XXX/) is optional, only create when:
- User requests "detailed tracking"
- Task is complex (many steps, long duration)
- Team collaboration requires shared checklist

### Cancelled Task Reactivation

1. Create new task with same title
2. Reference old task in description: "Replaces cancelled task-004"
3. Don't modify old cancelled task

### S3 Sync Failure

1. Add entry to `project_history.md`: `sync_failed | {error}`
2. Continue working locally
3. Don't block task operations on sync failures
