# Nash-Hardened Google Workspace MCP

This is a hardened fork of [taylorwilsdon/google_workspace_mcp](https://github.com/taylorwilsdon/google_workspace_mcp) with dangerous tools removed and OAuth scopes restricted to minimize blast radius.

## Philosophy

- AI agents should **read freely, write cautiously, and never delete or share**.
- Gmail: draft emails (human reviews before sending), never send directly.
- Drive: read files only, never create/delete/share/move/modify permissions.
- Calendar: create and modify events, never delete.
- Tasks: create, read, update, move — never delete.
- No chat, forms, contacts, search, or apps script tools (unnecessary attack surface).

## Tools Removed

### Gmail
| Tool | Reason |
|------|--------|
| `send_gmail_message` | AI should never send email directly |
| `create_gmail_filter` | Filter creation can redirect/delete mail silently |
| `delete_gmail_filter` | Filter deletion can break mail workflows |
| `modify_gmail_message_labels` | Can archive/trash messages via label manipulation |
| `batch_modify_gmail_message_labels` | Bulk label changes = bulk archive/trash |
| `manage_gmail_label` | Requires gmail.labels scope (removed) |
| `list_gmail_filters` | Requires gmail.settings.basic scope (removed) |

### Drive
| Tool | Reason |
|------|--------|
| `create_drive_file` | AI should not create files in Drive |
| `update_drive_file` | Can trash files, change metadata, move files |
| `share_drive_file` | Sharing files externally is a data leak vector |
| `batch_share_drive_file` | Bulk sharing is a severe data leak vector |
| `copy_drive_file` | Copying files can duplicate sensitive data |
| `transfer_drive_ownership` | Irreversible ownership transfer |
| `import_to_google_doc` | Requires drive.file scope (removed) |
| `update_drive_permission` | Permission modification is a sharing vector |
| `remove_drive_permission` | Permission modification is a sharing vector |
| `set_drive_file_permissions` | Permission modification is a sharing vector |

### Calendar
| Tool | Reason |
|------|--------|
| `delete_event` | AI should not delete calendar events |

### Tasks
| Tool | Reason |
|------|--------|
| `delete_task_list` | AI should not delete task lists |
| `delete_task` | AI should not delete tasks |
| `clear_completed_tasks` | Bulk deletion of completed tasks |

### Entire Services Removed
| Service | Reason |
|---------|--------|
| **Google Chat** | Sending messages as the user is too dangerous |
| **Google Forms** | Creating/modifying forms is unnecessary |
| **Google Contacts** | Contact manipulation is unnecessary and risky |
| **Google Custom Search** | Unnecessary for workspace tasks |
| **Google Apps Script** | Script execution is too powerful / dangerous |

## OAuth Scope Restrictions

### Gmail
- **Allowed:** `gmail.readonly`, `gmail.compose` (draft only)
- **Removed:** `gmail.send`, `gmail.modify`, `gmail.labels`, `gmail.settings.basic`

### Drive
- **Allowed:** `drive.readonly`
- **Removed:** `drive` (full access), `drive.file`

### Calendar
- **Kept full:** `calendar`, `calendar.readonly`, `calendar.events`

### Docs
- **Kept full:** `documents.readonly`, `documents`

### Sheets
- **Kept full:** `spreadsheets.readonly`, `spreadsheets`

### Slides
- **Kept full:** `presentations`, `presentations.readonly`

### Tasks
- **Kept full:** `tasks`, `tasks.readonly`

### Removed Entirely
- All `chat.*` scopes
- All `forms.*` scopes
- All `contacts.*` scopes
- All `script.*` scopes
- `cse` (Custom Search Engine)
- `drive.file` (used by Apps Script and Drive write tools)

## What Still Works

### Gmail (read + draft)
- `search_gmail_messages` - Search emails
- `get_gmail_message_content` - Read individual emails
- `get_gmail_messages_content_batch` - Read multiple emails
- `get_gmail_attachment_content` - Download attachments
- `get_gmail_thread_content` - Read email threads
- `get_gmail_threads_content_batch` - Read multiple threads
- `list_gmail_labels` - List labels
- `draft_gmail_message` - Create drafts (human sends manually)

### Drive (read only)
- `search_drive_files` - Search files
- `get_drive_file_content` - Read file content
- `get_drive_file_download_url` - Get download URLs
- `list_drive_items` - List folder contents
- `get_drive_file_permissions` - View permissions
- `check_drive_file_public_access` - Check public access
- `get_drive_shareable_link` - Get existing share links

### Calendar (read + create + modify)
- `list_calendars` - List calendars
- `get_events` - Read events
- `create_event` - Create new events
- `modify_event` - Modify existing events
- `query_freebusy` - Check availability

### Tasks (read + create + update + move)
- `list_task_lists` - List task lists
- `get_task_list` - Get task list details
- `create_task_list` - Create new task list
- `update_task_list` - Update task list
- `list_tasks` - List tasks in a list
- `get_task` - Get task details
- `create_task` - Create new task
- `update_task` - Update a task
- `move_task` - Move/reorder a task

### Docs, Sheets, Slides
- All tools preserved (scoped appropriately)

## Upstream Sync Notes

When merging from upstream `taylorwilsdon/google_workspace_mcp`:
1. **Never re-add removed tools** - check diffs carefully
2. **Never expand OAuth scopes** - review `auth/scopes.py` changes
3. **Never re-add removed services** - check `main.py`, `fastmcp_server.py`, and `tool_tiers.yaml`
4. **Check both entry points** - `main.py` AND `fastmcp_server.py` load tool modules
5. See `CLAUDE.md` for automated merge rules
