---
name: obsidian-vault-manager
description: Manage Helfrid's Obsidian vault with strict adherence to Zettelkasten metadata standards, YAML templates, and Map of Content (MOC) maintenance. Use when creating, reading, editing, or searching for notes within the vault, or when updating MOCs to reflect new information.
---

# Obsidian Vault Manager

This skill is a specialized wrapper for the Obsidian CLI, tailored specifically for Helfrid's vault structure and metadata conventions.

## Core Mandates

### 1. Metadata Integrity (The Frontmatter Rule)
Every new note **must** include the following YAML frontmatter. Do not omit fields. For existing notes, ensure `date_updated` is refreshed upon modification.

```yaml
---
date_created: YYYY-MM-DDTHH:MM
date_updated: YYYY-MM-DDTHH:MM
category:
  - <category>
type: <type>
tags:
  - <tag>
parent:
  - "[[ParentNote]]"
status: <status>
summary: <one-line summary>
next_task: <next concrete step>
---
```

#### Valid Values

**Categories:** `archive`, `areas`, `coding`, `collections`, `daily-notes`, `dave`, `inbox`, `lab`, `learning`, `papers`, `project-management`, `reading`, `resources`, `teaching`, `uncategorized`, `writing`

**Types:** `MOC`, `collection`, `daily-note`, `draft`, `experiment`, `labmeeting`, `learning`, `literature-scoop`, `log`, `meeting`, `moc`, `note`, `plan`, `project`, `reference`, `study_note`, `synthesis`

**Statuses:** `active`, `archived`, `complete`, `idea`, `in progress`, `paused`, `priority`, `review`, `to_review`, `unread`

### 2. MOC Maintenance (The Connectivity Rule)
Whenever a note is **created** or **significantly updated** (e.g., new key insights added):
1. Identify the note's `category`.
2. Locate the corresponding Map of Content (MOC) by searching for `[[<category> MOC]]`.
3. Append a link to the new/updated note to the MOC to maintain the knowledge graph.

### 3. Directory Synchronization
Notes should be placed in folders corresponding to their `category` to maintain a one-level hierarchy for easy maintenance.

---

## Command Reference (Obsidian CLI)

Use the `obsidian` CLI via shell execution.

**Note Operations:**
- `obsidian create name="Note Name" content="# Header" silent` (Use `silent` to avoid interrupting the user)
- `obsidian read file="Note Name"`
- `obsidian append file="Note Name" content="New content"`
- `obsidian property:set name="status" value="active" file="Note Name"`

**Search & Discovery:**
- `obsidian search query="search term" limit=10`
- `obsidian backlinks file="Note Name"`

**Daily/Task Management:**
- `obsidian daily:read`
- `obsidian daily:append content="- [ ] New task"`

## Syntax Reminders
- Parameters: `key="value"`
- Flags: `silent`, `overwrite`, `total`
- Multiline: Use `\n` for newlines in `content` strings.
