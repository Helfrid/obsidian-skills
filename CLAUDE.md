# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Active Project

At the start of every session:                                                                                                                                                                                                                                              
  1. Use the obsidian-vault-manager skill — run the full MOC scan, then anchor to [[&ZettelKastenMemory]] (sub-project) and [[@LabRAG]] (top-level domain).
  2. Read [[&ZettelKastenMemory_progresslog]] to get current project state. If it doesn't exist, create it.                                                                                                                                                                              
                                                                                                                                                                                                                                                                              
  During and at the end of every session:                                                                                                                                                                                                                                     
  3. Append to [[&ZettelKastenMemory_progresslog]] when a key decision is made, a feature is built, or the session ends — following the Did / Decided / Next entry format.   
  
## What This Repo Is

A collection of Agent Skills for working with Obsidian vaults, following the [Agent Skills specification](https://agentskills.io/specification). Skills are compatible with Claude Code, Codex CLI, and OpenCode.

There is no build system — this is a documentation-driven repository. All skills are plain markdown files.

## Skill Structure

Each skill lives under `skills/<skill-name>/` and contains:

- `SKILL.md` — required; frontmatter with `name` and `description`, followed by workflow documentation with syntax and examples
- `references/` — optional subdirectory for supplementary reference docs (callouts, properties, function references, examples)

The frontmatter format at the top of every `SKILL.md`:

```yaml
---
name: skill-name
description: One-line description of what the skill does
---
```

## Skills in This Repo

| Skill | Purpose |
|-------|---------|
| `obsidian-markdown` | Obsidian Flavored Markdown syntax (wikilinks, embeds, callouts, properties) |
| `obsidian-bases` | Obsidian Bases `.base` files (views, filters, formulas, summaries) |
| `json-canvas` | JSON Canvas `.canvas` files (nodes, edges, groups) |
| `obsidian-cli` | Obsidian CLI interactions (read, create, search notes; plugin dev) |
| `defuddle` | Extract clean markdown from web pages via Defuddle CLI |
| `obsidian-vault-manager` | Personal Zettelkasten wrapper for Helfrid's vault (custom conventions) |

## Adding or Modifying Skills

- Keep each `SKILL.md` self-contained; a reader should understand the full workflow from that file alone
- Use fenced code blocks with appropriate language tags for all syntax examples
- If a skill's reference material is large, split into `references/` files and link from `SKILL.md`
- The `obsidian-vault-manager` skill is a personal customization layered on top of `obsidian-markdown` — changes to base skills should not break its conventions

## Distribution

Skills are distributed via:
1. Claude Code marketplace: `/plugin marketplace add kepano/obsidian-skills`
2. npx: `npx skills add git@github.com:kepano/obsidian-skills.git`
3. Manual copy into `/.claude` (Claude Code), `~/.codex/skills` (Codex), or `~/.opencode/skills/` (OpenCode)
