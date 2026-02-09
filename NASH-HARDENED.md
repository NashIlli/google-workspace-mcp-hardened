# Nash-Hardened Google Workspace MCP

This is a hardened fork of [taylorwilsdon/google_workspace_mcp](https://github.com/taylorwilsdon/google_workspace_mcp) with dangerous tools removed and OAuth scopes restricted to minimize blast radius.

## Philosophy

- AI agents should **read freely, write cautiously, and never delete or share**.
- Gmail: draft emails (human reviews before sending), never send directly.
- Drive: read files, never create/delete/share/move.
- Calendar: create and modify events, never delete.
- No chat, forms, contacts, or search tools (unnecessary attack surface).

## Tools Removed

### Gmail
| Tool | Reason |
|------|--------|
| `send_gmail_message` | AI should never send email directly |
| `create_gmail_filter` | Filter creation can redirect/delete mail silently |
| `delete_gmail_filter` | Filter deletion can break mail workflows |
| `modify_gmail_message_labels` | Can archive/trash messages via label manipulation |
| `batch_modify_gmail_message_labels` | Bulk label changes = bulk archive/trash |

### Drive
| Tool | Reason |
|------|--------|
| `create_drive_file` | AI should not create files in Drive |
| `update_drive_file` | Can trash files, change metadata, move files |
| `share_drive_file` | Sharing files externally is a data leak vector |
| `batch_share_drive_file` | Bulk sharing is a severe data leak vector |
| `copy_drive_file` | Copying files can duplicate sensitive data |
| `transfer_drive_ownership` | Irreversible ownership transfer |

### Calendar
| Tool | Reason |
|------|--------|
| `delete_event` | AI should not delete calendar events |

### Entire Services Removed
| Service | Reason |
|---------|--------|
| **Google Chat** | Sending messages as the user is too dangerous |
| **Google Forms** | Creating/modifying forms is unnecessary |
| **Google Contacts** | Contact manipulation is unnecessary and risky |
| **Google Custom Search** | Unnecessary for workspace tasks |

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
- `cse` (Custom Search Engine)

## What Still Works

### Gmail (read + draft)
- `search_gmail_messages` - Search emails
- `get_gmail_message_content` - Read individual emails
- `get_gmail_messages_content_batch` - Read multiple emails
- `get_gmail_attachment_content` - Download attachments
- `get_gmail_thread_content` - Read email threads
- `get_gmail_threads_content_batch` - Read multiple threads
- `list_gmail_labels` - List labels
- `manage_gmail_label` - Create/update/delete labels
- `draft_gmail_message` - Create drafts (human sends manually)
- `list_gmail_filters` - List existing filters

### Drive (read only)
- `search_drive_files` - Search files
- `get_drive_file_content` - Read file content
- `get_drive_file_download_url` - Get download URLs
- `list_drive_items` - List folder contents
- `import_to_google_doc` - Import files to Google Docs format
- `get_drive_file_permissions` - View permissions
- `check_drive_file_public_access` - Check public access
- `get_drive_shareable_link` - Get existing share links

### Calendar (read + create + modify)
- `list_calendars` - List calendars
- `get_events` - Read events
- `create_event` - Create new events
- `modify_event` - Modify existing events
- `query_freebusy` - Check availability

### Docs, Sheets, Slides, Tasks, Apps Script
- All tools preserved (scoped appropriately)

## Upstream Sync Notes

When merging from upstream `taylorwilsdon/google_workspace_mcp`:
1. **Never re-add removed tools** - check diffs carefully
2. **Never expand OAuth scopes** - review `auth/scopes.py` changes
3. **Never re-add removed services** - check `main.py` and `tool_tiers.yaml`
4. See `CLAUDE.md` for automated merge rules
