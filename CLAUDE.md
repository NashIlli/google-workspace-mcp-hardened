# Claude Code Rules for google-workspace-mcp-hardened

This is a hardened fork. Read NASH-HARDENED.md for full context.

## Hard Rules (never violate)

1. **NEVER re-add removed tools.** The following tools have been deliberately removed and must never be restored:
   - Gmail: `send_gmail_message`, `create_gmail_filter`, `delete_gmail_filter`, `modify_gmail_message_labels`, `batch_modify_gmail_message_labels`, `manage_gmail_label`, `list_gmail_filters`
   - Drive: `create_drive_file`, `update_drive_file`, `share_drive_file`, `batch_share_drive_file`, `copy_drive_file`, `transfer_drive_ownership`, `import_to_google_doc`, `update_drive_permission`, `remove_drive_permission`, `set_drive_file_permissions`
   - Calendar: `delete_event`
   - Tasks: `delete_task_list`, `delete_task`, `clear_completed_tasks`

2. **NEVER re-add removed services.** These services are entirely removed (directories deleted):
   - Google Chat (`gchat/`)
   - Google Forms (`gforms/`)
   - Google Contacts (`gcontacts/`)
   - Google Custom Search (`gsearch/`)
   - Google Apps Script (`gappsscript/`)

3. **NEVER expand OAuth scopes.** The current scope restrictions are:
   - Gmail: `gmail.readonly` + `gmail.compose` only (NO `gmail.send`, NO `gmail.modify`, NO `gmail.labels`, NO `gmail.settings.basic`)
   - Drive: `drive.readonly` only (NO full `drive`, NO `drive.file`)
   - No chat, forms, contacts, search, or script scopes

4. **NEVER add tools that send messages as the user** (email, chat, etc.) without explicit human review.

5. **NEVER add tools that delete user data** (files, events, contacts, tasks, etc.).

6. **NEVER add tools that share files or data externally** (Drive sharing, file permissions, etc.).

## Merge Protocol (upstream sync)

When merging from `taylorwilsdon/google_workspace_mcp`:

1. Review every changed file in the diff before merging.
2. Reject any changes to `auth/scopes.py` that expand scopes.
3. Reject any changes to `main.py` or `fastmcp_server.py` that re-add removed services.
4. Reject any changes to `core/tool_tiers.yaml` that re-add removed tools.
5. If upstream adds new tools, evaluate them against the hard rules above before including.
6. After merging, run: `grep -rn "send_gmail_message\|delete_event\|delete_task\|share_drive_file\|create_drive_file\|transfer_drive_ownership\|import_to_google_doc\|update_drive_permission\|remove_drive_permission\|set_drive_file_permissions\|manage_gmail_label\|list_gmail_filters\|clear_completed_tasks\|gchat\|gforms\|gcontacts\|gsearch\|gappsscript" --include="*.py" --include="*.yaml"` to verify no removed tools leaked back in.

## File Layout

- `NASH-HARDENED.md` - Full documentation of all hardening changes
- `CLAUDE.md` - This file (rules for AI agents working on this repo)
- `auth/scopes.py` - OAuth scope definitions (restricted)
- `auth/service_decorator.py` - Service decorator with scope groups (cleaned)
- `main.py` - Service registration, stdio entry point (removed services excluded)
- `fastmcp_server.py` - FastMCP Cloud entry point (removed services excluded)
- `core/tool_tiers.yaml` - Tool tier config (removed tools excluded)
- `gmail/gmail_tools.py` - Gmail tools (send/filter/label-manage removed)
- `gdrive/drive_tools.py` - Drive tools (create/update/share/copy/transfer/permissions removed)
- `gcalendar/calendar_tools.py` - Calendar tools (delete_event removed)
- `gtasks/tasks_tools.py` - Tasks tools (delete/clear removed)
