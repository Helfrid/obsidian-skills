Agent Skills for use with Obsidian.

These skills follow the [Agent Skills specification](https://agentskills.io/specification) so they can be used by any skills-compatible agent, including Claude Code and Codex CLI.

> **This is a personal fork of [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills).**
> It extends the original with `obsidian-vault-manager` — a custom skill for managing a personal Zettelkasten vault as a shared second brain between human and AI agents. The base skills (`obsidian-cli`, `obsidian-markdown`, `obsidian-bases`, `json-canvas`, `defuddle`) are kept in sync with upstream.

## What's Different in This Fork

### obsidian-vault-manager

A personal skill layered on top of the base Obsidian skills, designed around a specific Zettelkasten vault structure. It defines:

- **Frontmatter contract** — required fields for every note, with `summary` as the load-bearing retrieval field and `date_updated` delegated to the `update-time-on-edit` plugin
- **MOC architecture** — two-layer hierarchy (`@TopLevel` / `&SubLevel`) with multi-parent support via `parent` list
- **Session protocol** — adaptive MOC discovery (system prompt → vault scan → ask), progress log auto-creation, and session start/end conventions
- **Layered retrieval** — four-step protocol using `obsidian eval` against `app.metadataCache` (no file I/O until the final step)
- **Warburg Principle** — serendipitous cross-domain connections are surfaced at session start and end, but only when they genuinely reframe the work
- **Progress logs** — append-only `<MOCName>_progresslog.md` paired with every project MOC; auto-created if missing

### vault.base

A single unified `.base` file replaces the original per-folder base files. All views are named and embedded as `![[vault.base#ViewName]]`. No `file.inFolder()` restrictions — connectivity is not limited by folder location.

### Installation for This Setup

Skills are symlinked globally from the repo into `~/.claude/skills/` so updates propagate automatically:

```sh
ln -s /path/to/obsidian-skills/skills/obsidian-vault-manager ~/.claude/skills/obsidian-vault-manager
ln -s /path/to/obsidian-skills/skills/obsidian-cli ~/.claude/skills/obsidian-cli
ln -s /path/to/obsidian-skills/skills/obsidian-markdown ~/.claude/skills/obsidian-markdown
ln -s /path/to/obsidian-skills/skills/obsidian-bases ~/.claude/skills/obsidian-bases
ln -s /path/to/obsidian-skills/skills/defuddle ~/.claude/skills/defuddle
ln -s /path/to/obsidian-skills/skills/json-canvas ~/.claude/skills/json-canvas
```

For Claude Desktop, paste the `obsidian-vault-manager/SKILL.md` content into the project description and add an active project pointer:

```
At the start of every session:
1. Use the obsidian-vault-manager skill — run the full MOC scan, then anchor to [[&MOCName]] (sub-project) and [[@MOCName]] (top-level domain).
2. Read [[&MOCName_progresslog]] to get current project state. If it doesn't exist, create it.

During and at the end of every session:
3. Append to [[&MOCName_progresslog]] when a key decision is made, a feature is built, or the session ends — following the Did / Decided / Next entry format.
```

---

## Installation (Original)

### Marketplace

```
/plugin marketplace add kepano/obsidian-skills
/plugin install obsidian@obsidian-skills
```

### npx skills

```
npx skills add git@github.com:kepano/obsidian-skills.git
```

### Manually

#### Claude Code

Add the contents of this repo to a `/.claude` folder in the root of your Obsidian vault (or whichever folder you're using with Claude Code). See more in the [official Claude Skills documentation](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview).

#### Codex CLI

Copy the `skills/` directory into your Codex skills path (typically `~/.codex/skills`). See the [Agent Skills specification](https://agentskills.io/specification) for the standard skill format.

#### OpenCode

Clone the entire repo into the OpenCode skills directory (`~/.opencode/skills/`):

```sh
git clone https://github.com/Helfrid/obsidian-skills.git ~/.opencode/skills/obsidian-skills
```

Do not copy only the inner `skills/` folder — clone the full repo so the directory structure is `~/.opencode/skills/obsidian-skills/skills/<skill-name>/SKILL.md`.

OpenCode auto-discovers all `SKILL.md` files under `~/.opencode/skills/`. No changes to `opencode.json` or any config file are needed. Skills become available after restarting OpenCode.

---

## Skills

| Skill | Description |
|-------|-------------|
| [obsidian-vault-manager](skills/obsidian-vault-manager) | Personal Zettelkasten vault manager — session protocol, layered retrieval, MOC hierarchy, and Warburg serendipity principle (**this fork**) |
| [obsidian-markdown](skills/obsidian-markdown) | Create and edit [Obsidian Flavored Markdown](https://help.obsidian.md/obsidian-flavored-markdown) (`.md`) with wikilinks, embeds, callouts, properties, and other Obsidian-specific syntax |
| [obsidian-bases](skills/obsidian-bases) | Create and edit [Obsidian Bases](https://help.obsidian.md/bases/syntax) (`.base`) with views, filters, formulas, and summaries |
| [json-canvas](skills/json-canvas) | Create and edit [JSON Canvas](https://jsoncanvas.org/) files (`.canvas`) with nodes, edges, groups, and connections |
| [obsidian-cli](skills/obsidian-cli) | Interact with Obsidian vaults via the [Obsidian CLI](https://help.obsidian.md/cli) including plugin and theme development |
| [defuddle](skills/defuddle) | Extract clean markdown from web pages using [Defuddle](https://github.com/kepano/defuddle-cli), removing clutter to save tokens |
