# Claude Code Rules for google-workspace-mcp-hardened

This is a hardened fork. Read NASH-HARDENED.md for full context.

## Hard Rules (never violate)

1. **NEVER re-add removed tools.** The following tools have been deliberately removed and must never be restored:
   - Gmail: `send_gmail_message`, `create_gmail_filter`, `delete_gmail_filter`, `modify_gmail_message_labels`, `batch_modify_gmail_message_labels`
   - Drive: `create_drive_file`, `update_drive_file`, `share_drive_file`, `batch_share_drive_file`, `copy_drive_file`, `transfer_drive_ownership`
   - Calendar: `delete_event`

2. **NEVER re-add removed services.** These services are entirely removed:
   - Google Chat (`gchat/`)
   - Google Forms (`gforms/`)
   - Google Contacts (`gcontacts/`)
   - Google Custom Search (`gsearch/`)

3. **NEVER expand OAuth scopes.** The current scope restrictions are:
   - Gmail: `gmail.readonly` + `gmail.compose` only (NO `gmail.send`, NO `gmail.modify`)
   - Drive: `drive.readonly` only (NO full `drive`, NO `drive.file`)
   - Slides: `presentations.readonly` only
   - No chat, forms, contacts, or search scopes

4. **NEVER add tools that send messages as the user** (email, chat, etc.) without explicit human review.

5. **NEVER add tools that delete user data** (files, events, contacts, etc.).

6. **NEVER add tools that share files or data externally** (Drive sharing, file permissions, etc.).

## Merge Protocol (upstream sync)

When merging from `taylorwilsdon/google_workspace_mcp`:

1. Review every changed file in the diff before merging.
2. Reject any changes to `auth/scopes.py` that expand scopes.
3. Reject any changes to `main.py` that re-add removed services.
4. Reject any changes to `core/tool_tiers.yaml` that re-add removed tools.
5. If upstream adds new tools, evaluate them against the hard rules above before including.
6. After merging, run: `grep -rn "send_gmail_message\|delete_event\|share_drive_file\|create_drive_file\|transfer_drive_ownership\|gchat\|gforms\|gcontacts\|gsearch" --include="*.py" --include="*.yaml"` to verify no removed tools leaked back in.

## File Layout

- `NASH-HARDENED.md` - Full documentation of all hardening changes
- `CLAUDE.md` - This file (rules for AI agents working on this repo)
- `auth/scopes.py` - OAuth scope definitions (restricted)
- `main.py` - Service registration (removed services excluded)
- `core/tool_tiers.yaml` - Tool tier config (removed tools excluded)
- `gmail/gmail_tools.py` - Gmail tools (send/filter/label-modify removed)
- `gdrive/drive_tools.py` - Drive tools (create/update/share/copy/transfer removed)
- `gcalendar/calendar_tools.py` - Calendar tools (delete_event removed)
