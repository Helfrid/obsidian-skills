---
name: obsidian-vault-manager
description: Manage Helfrid's Obsidian Zettelkasten vault. Use for all vault interactions — creating and updating notes, navigating the MOC hierarchy, retrieving context across the vault, and maintaining project progress logs. This skill defines the session protocol, frontmatter contract, layered retrieval strategy, and the Warburg serendipity principle.
---

# Obsidian Vault Manager

This skill governs all interactions with Helfrid's Obsidian vault at `/Users/hh65/Notes`. It wraps the `obsidian` CLI and `obsidian eval` (for metadataCache queries). Obsidian must be running.

---

## Frontmatter Contract

Every note **must** have this frontmatter. Never omit fields. `date_updated` is managed automatically by the vault's `update-time-on-edit` plugin — do not set it manually.

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
  - "[[ParentMOC]]"    # list — a note can belong to multiple MOCs
status: <status>
summary: <one-line meaningful summary — this is load-bearing for retrieval>
next_task: <next concrete step>   # MOC notes only
---
```

**`summary` is the most critical field.** The layered retrieval system reads summaries before opening any note. A vague summary ("rust", "notes") makes the note invisible to retrieval. It must answer: *what is this note actually about?*

### Valid Values

**Categories:** `archive`, `areas`, `coding`, `collections`, `daily-notes`, `inbox`, `lab`, `learning`, `papers`, `project-management`, `reading`, `resources`, `teaching`, `writing`

**Types:** `MOC`, `moc`, `collection`, `daily-note`, `draft`, `experiment`, `log`, `meeting`, `note`, `plan`, `project`, `reference`, `study_note`, `synthesis`, `literature-scoop`, `labmeeting`, `learning`

**Statuses:** `active`, `archived`, `complete`, `idea`, `in progress`, `paused`, `priority`, `review`, `to_review`, `unread`

---

## MOC Architecture

Two-layer hierarchy:

```
@TopLevel.md      — domain or area MOC (e.g. @DepMap, @Msc2025, @rust)
  &SubLevel.md    — sub-project or topic MOC (e.g. &ZettelKastenMemory)
    notes         — atomic knowledge notes
```

- Some projects live entirely at `@TopLevel` — no sublevel needed
- `&SubLevel` is created only when a project warrants the extra granularity
- A note can belong to multiple MOCs — `parent` is a list; the Bases view handles membership via frontmatter filters
- MOC body must stay short: **What / Why / How / When** only. Never append prose to a MOC body — narrative goes in the progress log or linked notes.
- Each MOC embeds its Bases view: `![[vault.base#@MOCName]]` or `![[vault.base#&MOCName]]`

### Progress Log

Every project MOC has a paired append-only log:
- `@ProjectName_progresslog.md` or `&ProjectName_progresslog.md`

**At session start, check whether the progress log exists:**
```bash
obsidian eval code="app.vault.getMarkdownFiles().some(f => f.basename === '<MOCName>_progresslog') ? 'exists' : 'missing'"
```

If missing, create it before doing any other work:
```bash
obsidian create name="<MOCName>_progresslog" content="---\ndate_created: <YYYY-MM-DDTHH:MM>\ndate_updated: <YYYY-MM-DDTHH:MM>\ncategory:\n  - <same as MOC>\ntype: log\ntags:\n  - <same as MOC>\nparent:\n  - \"[[<MOCName>]]\"\nstatus: active\nsummary: Progress log for <MOCName> — decisions, results, session records\n---\n\n# <MOCName> — Progress Log" silent
```

See `references/progress_log_format.md` for the entry format. The log is the chronological record — decisions, commits, results, and session-end state. Never put this content in the MOC body.

---

## Session Protocol

### Start

**Step 1 — Run the full MOC scan (Layered Retrieval Step 1):**

Always run the eval query over all MOC summaries at session start. This scan serves two purposes simultaneously:
- **Orientation** — identify the active MOC for this session
- **Serendipity** — surface unexpected connections across the vault (Warburg Principle)

Do not skip this scan even when an active MOC is named in `CLAUDE.md`.

**Step 2 — Establish the active MOC:**

1. If `CLAUDE.md` (Claude Code) or the project description (Claude Desktop) names an `Active project:` MOC → that is the anchor. Read it in full. Report: *"Active MOC: [[MOCName]] — next task: <next_task>"*
2. If not set → use the scan result to identify the best fit for the session topic. Report: *"No active MOC set. Based on the session context I'll use [[MOCName]] — confirm or redirect."*
3. If no fit found → report: *"No active MOC set and I couldn't identify a clear fit. Please name the MOC or project to work under."*

**Step 3 — Apply the Warburg Principle to the scan result:**

With the full MOC summary list in context, check whether any other MOC genuinely reframes or enriches the session topic in a non-obvious way. If so, surface it once: *"[[OtherMOC]] may be relevant here because..."* If nothing stands out, say nothing. The scan result stays in context for the rest of the session — do not re-run it.

**Step 4 — Orient within the active MOC:**
- Check `next_task` and `status` in frontmatter → this is where to continue from
- The Bases view surfaces linked notes — pull specific ones only as the work requires

### During
- Consult the vault via the layered retrieval protocol (see below) as needed
- Append to the progress log at: key decisions, commits, analysis results, and whenever the session ends or is interrupted
- Propose note creation for significant outputs; write only when the user confirms

### End
- Update `next_task` and `status` in MOC frontmatter
- Append a closing entry to the progress log

---

## Layered Retrieval Protocol

Use this when you need information from outside the current working context. Either triggered explicitly ("check the vault for X") or proposed by Claude ("this might be in the vault — want me to check?"). Always confirm with the user before going beyond Step 1.

```
Step 1: eval → all MOC names + summaries           (no file reads — metadataCache only)
         Identify which @TopLevel domains are relevant

Step 2: eval → &SubLevel MOC names + summaries     (no file reads)
         Narrow to relevant topics within those domains

Step 3: eval → linked note summaries               (no file reads)
         Identify candidate notes from the Bases view of relevant MOCs

Step 4: obsidian read                              (1–2 notes maximum)
         Read full content only for confirmed-relevant notes
```

Steps 1–3 use `app.metadataCache` — Obsidian's built-in frontmatter index. No file I/O until Step 4.

### eval Queries

**Step 1 — All MOC names and summaries:**
```bash
obsidian eval code="app.vault.getMarkdownFiles().filter(f => { const fm = app.metadataCache.getFileCache(f)?.frontmatter; return fm?.type === 'moc' || fm?.type === 'MOC'; }).map(f => { const fm = app.metadataCache.getFileCache(f).frontmatter; return f.basename + ': ' + (fm.summary || 'no summary'); }).join('\n')"
```

**Step 2 — SubLevel MOCs for a domain (by tag or category):**
```bash
obsidian eval code="app.vault.getMarkdownFiles().filter(f => { const fm = app.metadataCache.getFileCache(f)?.frontmatter; return (fm?.type === 'moc' || fm?.type === 'MOC') && fm?.category?.includes('<category>'); }).map(f => f.basename + ': ' + (app.metadataCache.getFileCache(f).frontmatter.summary || 'no summary')).join('\n')"
```

**Step 3 — All note summaries linked to a MOC (by tag):**
```bash
obsidian eval code="app.vault.getMarkdownFiles().filter(f => { const fm = app.metadataCache.getFileCache(f)?.frontmatter; return fm?.tags?.includes('<tag>'); }).map(f => f.basename + ': ' + (app.metadataCache.getFileCache(f).frontmatter.summary || 'no summary')).join('\n')"
```

**Step 4 — Read a specific note:**
```bash
obsidian read file="Note Name"
```

---

## The Warburg Principle

The vault's value comes partly from unexpected connections across domains — the *Pathosformel* of the knowledge graph. Surface these serendipitous links, but never force them.

**When to surface a connection:**
- **Session start**: if the Step 1 MOC scan reveals something that genuinely reframes or enriches the session topic, mention it. If nothing stands out, say nothing.
- **Session end**: if the work produced something that genuinely extends or contradicts an existing note, propose the link.

**The test**: Claude must be able to complete the sentence — *"This is relevant because it reframes / extends / contradicts X in a non-obvious way."* If the connection is only a tag match or surface similarity, stay silent. Forced connections are worse than none.

---

## Common CLI Commands

```bash
# Create a note (always include full frontmatter in content)
obsidian create name="Note Name" content="---\n...\n---\n# Body" silent

# Read a note
obsidian read file="Note Name"

# Append to a note (progress log entries, MOC log lines)
obsidian append file="Note Name" content="- 2026-04-18: entry text"

# Update a frontmatter field (date_updated is set automatically on save — do not set manually)
obsidian property:set name="status" value="active" file="Note Name"
obsidian property:set name="next_task" value="Write tests for retrieval module" file="Note Name"

# Search (use as fallback — prefer eval + metadataCache for structured queries)
obsidian search query="search term" limit=10

# Backlinks
obsidian backlinks file="Note Name"
```

Use `silent` on create/open operations to avoid interrupting the user's Obsidian session.
