# Progress Log Format

The progress log is an append-only chronological record paired with every project MOC. It captures decisions, results, commits, and session state. The MOC body stays lean — all narrative goes here.

---

## Entry Format

```markdown
## YYYY-MM-DD | <agent>

**Did:** <what was accomplished this session — concrete, specific>
**Decided:** <any significant decision and the reason — optional, omit if none>
**Result:** <outcome of analysis, experiment, or implementation — optional>
**Next:** <what comes next — should match next_task in MOC frontmatter>
```

**`<agent>`** identifies who wrote the entry: `claude-code`, `claude-desktop`, `human`, or the model/tool name. This matters when multiple agents contribute to the same project.

---

## When to Append

Write a log entry when any of the following occur:

- A key decision is made (architectural, methodological, strategic)
- A commit is made to a codebase
- An analysis produces a result (positive or negative)
- The session ends — **including interruptions**. If work stops mid-task, write a partial entry so the next session can pick up without confusion.
- A significant finding from the vault retrieval informs the work

Do **not** write an entry for routine progress with no decision or result worth recording.

---

## Example Entries

```markdown
## 2026-04-18 | claude-code

**Did:** Rewrote obsidian-vault-manager skill with layered retrieval protocol and Warburg principle
**Decided:** Retire turbovault MCP in favour of obsidian CLI + eval against metadataCache — native Bases access eliminates need for SQLite reimplementation
**Next:** Test eval queries against live vault; write Bases view for &ZettelKastenMemory

---

## 2026-04-19 | claude-code

**Did:** Implemented Step 1–3 eval queries; confirmed metadataCache returns full frontmatter without file reads
**Result:** Step 1 scan of 1243 notes returns in <50ms; summary field coverage ~40% — needs improvement across vault
**Next:** Audit notes missing summary field; propose batch update workflow

---

## 2026-04-20 | human

**Did:** Updated summary fields on lab notes manually
**Decided:** Summary should answer "what is this note actually about" not restate the title
**Next:** Continue with &ZettelKastenMemory Bases view configuration
```

---

## Updating MOC Frontmatter

After writing a log entry, always sync `next_task` and `date_updated` in the paired MOC:

```bash
obsidian property:set name="next_task" value="<next step>" file="@ProjectName"
obsidian property:set name="date_updated" value="YYYY-MM-DDTHH:MM" file="@ProjectName"
```
