# Project Template

Templates for creating new project MOCs. Choose the level that fits the project scope.

---

## @TopLevel MOC

Use for a major domain, area, or standalone project that doesn't need sub-projects.

```markdown
---
date_created: YYYY-MM-DDTHH:MM
date_updated: YYYY-MM-DDTHH:MM
category:
  - <category>
type: moc
tags:
  - <tag>
  - moc
parent:
status: active
summary: <one-line description of what this project/area is actually about>
next_task: <first concrete step>
---

## What
<What is this project? 2–4 sentences max.>

## Why
<Why does it matter? What gap does it fill? 2–4 sentences max.>

## How
<How will it be approached? Methods, tools, strategy. 2–4 sentences max.>

## When
<Timeline or milestones. 1–2 sentences.>

![[vault.base#@ProjectName]]
```

---

## &SubLevel MOC

Use for a sub-project or focused topic within a @TopLevel domain.

```markdown
---
date_created: YYYY-MM-DDTHH:MM
date_updated: YYYY-MM-DDTHH:MM
category:
  - <category>
type: moc
tags:
  - <tag>
  - moc
parent:
  - "[[@@TopLevelParent]]"
status: active
summary: <one-line description — specific enough to distinguish from parent>
next_task: <first concrete step>
---

## What
<What is this sub-project? 2–4 sentences max.>

## Why
<Why warranted as a separate MOC? 2–4 sentences max.>

## How
<Approach, tools, methods. 2–4 sentences max.>

## When
<Timeline or milestones. 1–2 sentences.>

![[vault.base#&SubProjectName]]
```

---

## Progress Log

Every project MOC gets a paired progress log. Create it alongside the MOC:

Filename: `@ProjectName_progress.md` or `&ProjectName_progress.md`

```markdown
---
date_created: YYYY-MM-DDTHH:MM
date_updated: YYYY-MM-DDTHH:MM
category:
  - <same as MOC>
type: log
tags:
  - <same as MOC>
parent:
  - "[[@@ProjectName]]"
status: active
summary: Progress log for @ProjectName — decisions, results, session records
---

# @ProjectName — Progress Log
```

See `progress_log_format.md` for how to write entries.

---

## CLAUDE.md Pointer

Add this line to `CLAUDE.md` (Claude Code) or the project description (Claude Desktop) to set the active project:

```
Active project: [[&ProjectName]] — <one-line reminder of current focus>
```

Update this when switching projects.
