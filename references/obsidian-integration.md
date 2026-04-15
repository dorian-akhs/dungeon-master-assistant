# Obsidian Integration Guide

This guide covers everything needed to produce Obsidian-compatible campaign files. Obsidian mode activates when `obsidian: true` appears in `campaign-context.md`.

---

## Detection

Read `campaign-context.md` frontmatter. Check for:

```yaml
obsidian: true
obsidian_vault_root: "."   # optional, defaults to campaign root
```

If `obsidian` is absent or `false`, produce standard markdown. Never produce mixed output.

---

## Obsidian Properties Format

Obsidian uses its own YAML properties format that differs from standard YAML in subtle ways. Follow these rules exactly.

### Strings

Single string values: no quotes.
```yaml
name: Lady Morvaine       ✓
name: "Lady Morvaine"     ✗ (Obsidian renders the quotes)
```

Exception: strings containing colons or special YAML characters still need quotes.
```yaml
name: "High Priest: Zol'thar"   ✓
```

### Arrays

Always use block style (multi-line), never inline.
```yaml
tags:           ✓
  - campaign/npc
  - campaign/faction/guild

tags: [campaign/npc]     ✗ (inline style works but breaks Obsidian properties panel)
```

### Dates

Use ISO 8601 format: `YYYY-MM-DD`
```yaml
date: 2026-04-15
```

### Integers

Use bare integers, no quotes.
```yaml
session: 7
```

### Booleans

Bare `true` / `false`, no quotes.

---

## Complete Frontmatter Examples

### Session Recap (Obsidian)

```yaml
---
session: 7
date: 2026-04-15
system: D&D 5e
players_present:
  - Morwen
  - Cassiel
  - Dax
tags:
  - campaign/session
---
```

### NPC Profile (Obsidian)

```yaml
---
name: Lady Morvaine
aliases:
  - The Merchant Countess
  - Morvaine
status: alive
affiliation:
  - Merchants' Guild
first_appeared: Session 3
last_seen: Session 7
disposition: hostile
tags:
  - campaign/npc
  - campaign/faction/merchants-guild
---
```

### Location Profile (Obsidian)

```yaml
---
name: Silver Flagon
type: building
region: Tolvern
status: active
first_visited: Session 2
tags:
  - campaign/location
  - campaign/region/tolvern
  - campaign/visited
---
```

### World Bible (Obsidian)

```yaml
---
setting: The Fractured Reach
system: D&D 5e
tags:
  - campaign/world-bible
---
```

---

## Wikilinks

### When to Use Wikilinks

Use `[[Name]]` for any reference to a named entity that has (or will have) its own file:

- Named NPCs with profile files
- Named locations with profile files
- Named factions with faction files
- The world bible document

### When NOT to Use Wikilinks

Do not wikilink:
- Generic nouns: "the tavern", "the city", "the guard captain"
- System mechanics or spell names
- In-world concepts without a dedicated file (unless creating one)
- Names of entities in the DM Secrets section (these are spoilers — wikilinks in Obsidian create backlinks visible in graph view)

### Wikilink Format

Basic link: `[[Display Name]]`
Link with alias: `[[Display Name|how it appears in text]]`

Example:
```markdown
The party arrived at [[Silver Flagon]] and spoke with [[Mira]], who told them
that [[Aldous Krenn]] had not been seen in three days.
```

### When to Apply

Apply wikilinks in a post-processing pass **after** generating all file content, not during generation. This avoids forward-reference problems and double-wrapping.

Algorithm:
1. Build known-world index: display name → file slug, for all NPC and location files
2. For each paragraph in each generated file:
   - Find first occurrence of each known entity name
   - Wrap with `[[Name]]` if not already wrapped
3. Do not re-wrap already-wikilinked text

---

## Tag Taxonomy

Full tag tree for Obsidian mode:

```
campaign/
├── session              # session recap files
├── session-plan         # session plan files  
├── npc                  # all NPC profiles
├── location             # all location profiles
├── world-bible          # the world bible
├── faction/
│   └── [faction-slug]   # e.g. campaign/faction/merchants-guild
└── region/
    └── [region-slug]    # e.g. campaign/region/tolvern
```

Additional location tags:
- `campaign/visited` — the party has been here
- `campaign/mentioned` — known but not visited

Apply all applicable tags. An NPC affiliated with the Serpent Crown who has been met by the party:
```yaml
tags:
  - campaign/npc
  - campaign/faction/serpent-crown
```

---

## File Naming in Obsidian Mode

Obsidian has restrictions on file names. Apply these rules to all slugs:

**Forbidden characters**: `:` `|` `#` `^` `[` `]` `*` `?` `/` `\`

**Additional Obsidian restrictions**:
- No leading or trailing spaces
- No leading `.` (hidden files)
- No trailing `.`

**Test before writing**: verify the slug contains no forbidden characters before creating the file.

---

## Graph View Optimization

The Obsidian graph view becomes useful for campaigns when files are well-connected. To get a useful graph:

1. **Wikilink liberally in session recaps** — each session recap becomes a hub node connecting all the NPCs and locations that appeared
2. **Use consistent display names** — wikilinks only connect if the name matches exactly. Lady Morvaine and Lady morvaine are different nodes.
3. **Add `aliases:` to NPC files** — this lets Obsidian match wikilinks to alternate name forms
4. **Faction files** create useful cluster nodes — even a minimal faction file (Overview + Members) helps the graph show relationships

---

## Dataview-Compatible Fields

For DMs using the Dataview plugin, these optional frontmatter fields enable powerful queries.

### NPC files
```yaml
status: alive           # alive | dead | unknown | missing | transformed
disposition: neutral    # friendly | neutral | hostile | complex
last_seen: Session 7    # for sorting/filtering
```

**Example Dataview query** — all hostile NPCs currently alive:
```dataview
TABLE affiliation, last_seen
FROM #campaign/npc
WHERE status = "alive" AND disposition = "hostile"
SORT last_seen DESC
```

### Location files
```yaml
type: city              # city | dungeon | wilderness | building | region | planar
status: active          # active | destroyed | abandoned | unknown
first_visited: Session 2
```

**Example Dataview query** — all visited locations:
```dataview
TABLE type, region
FROM #campaign/location AND #campaign/visited
SORT first_visited ASC
```

### Session files
```yaml
session: 7              # integer — enables numeric sorting
date: 2026-04-15        # ISO date — enables date range queries
```

**Example Dataview query** — all sessions in chronological order:
```dataview
TABLE date, players_present
FROM #campaign/session
SORT session ASC
```

These fields are optional. Including them costs nothing and enables powerful future queries, so the debriefer should include them by default in Obsidian mode.

---

## Compatibility Notes

The following Obsidian syntax **breaks** in standard markdown viewers (GitHub, VS Code preview, Marked):

| Obsidian Feature | Breaks in Standard Markdown |
|---|---|
| `[[Wikilinks]]` | Renders as literal text `[[Wikilinks]]` |
| `![[Embedded files]]` | Renders as broken image |
| `#tags` in body text | Renders as heading or literal hash |
| Callout blocks `> [!note]` | Renders as plain blockquote |
| Dataview code blocks | Renders as code, not table |

If the DM needs files to be readable in both Obsidian and standard markdown, avoid embed syntax (`![[...]]`) and inline tags. Wikilinks and frontmatter tags are the safest — they render gracefully as text even when not processed by Obsidian.
