# Campaign Memory System

This document defines the rules every agent must follow when reading from or writing to campaign files. Read this before any file operation.

---

## Directory Layout

```
[campaign root]/
├── campaign-context.md       # Constitution — always read first
├── world-bible.md            # World lore reference
├── timeline.md               # Chronological event log
├── npcs/                     # One file per significant NPC
│   └── [name-slug].md
├── locations/                # One file per significant location
│   └── [name-slug].md
├── factions/                 # One file per faction (optional)
│   └── [faction-slug].md
├── sessions/                 # Session recaps and plans
│   ├── session-001-recap.md
│   ├── session-002-recap.md
│   └── session-003-plan.md
└── campaign-arc/             # Plot threads and adventure arcs (optional)
    └── [arc-slug].md
```

The campaign root is wherever `campaign-context.md` lives. All relative paths in this document are relative to that root.

---

## File Naming Rules

### Slugs

All file names use slugs: lowercase, hyphens for spaces, no special characters.

| Display Name | Slug |
|---|---|
| Lady Morvaine | `lady-morvaine` |
| The Silver Flagon | `the-silver-flagon` |
| Ashford Crossing | `ashford-crossing` |
| High Priest Zol'thar | `high-priest-zolthar` |

**Forbidden characters in file names**: `:` `|` `#` `^` `[` `]` `*` `?` `/` `\`

Strip apostrophes, periods, and other punctuation. Strip "The " prefix from location names only if it makes the slug awkward (`the-silver-flagon` is fine; `the-ancient-and-terrible-temple-of-forgotten-gods` should be shortened).

### Session Files

Session recaps: `session-NNN-recap.md` where NNN is zero-padded to 3 digits.
Session plans: `session-NNN-plan.md`

Determine NNN by counting existing recap files in `sessions/` and adding 1. Always zero-pad: `001`, `002`, `010`, `100`.

---

## Frontmatter Standards

### Standard Mode (obsidian: false or unset)

**campaign-context.md** — see `templates/campaign-context.md`

**NPC files**:
```yaml
---
name: "Display Name"
aliases: []
status: alive
affiliation: []
first_appeared: "Session 1"
last_seen: "Session N"
disposition: neutral
tags:
  - campaign/npc
---
```

**Location files**:
```yaml
---
name: "Display Name"
type: city
region: ""
status: active
first_visited: "Session N"
tags:
  - campaign/location
---
```

**Session recaps**:
```yaml
---
session: 1
date: "YYYY-MM-DD"
system: "D&D 5e"
players_present: []
tags:
  - campaign/session
---
```

### Obsidian Mode (obsidian: true)

Same fields, but use Obsidian properties format: no quotes on single string values, arrays always on multiple lines.

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

---

## What Goes Where: Decision Tree

### New NPC encountered in a session

```
Is the NPC named?
  No → Note in session recap only. No file.
  Yes →
    Did they have a speaking role or meaningful interaction?
      No → Note in session recap only. No file.
      Yes → Create npcs/[slug].md
    OR
    Have they been mentioned (by name) in 2+ sessions?
      Yes → Create npcs/[slug].md even without direct interaction
```

### New location encountered in a session

```
Did the party physically visit this location?
  Yes → Create locations/[slug].md
  No →
    Is it a named place with significant lore attached?
      Yes → Create locations/[slug].md
      No → Note in session recap only. No file.
```

### New event to record

```
Does this event have a future consequence or revealed cause?
  Yes → Append to timeline.md
  No → Note in session recap only. No timeline entry needed.
Examples of timeline-worthy events:
  - A named NPC dies
  - A major faction makes a move
  - The party makes an enemy or ally
  - Lore is revealed that recontextualizes earlier events
  - A location changes state (destroyed, conquered, abandoned)
```

### New lore revealed

```
Is it a world-level fact (geography, history, magic system, factions)?
  Yes → Update world-bible.md (append to relevant section)
  No →
    Is it NPC-specific?
      Yes → Update that NPC's file (append dated update block)
      No → Note in session recap only.
```

---

## Modification Rules

### The Append-Only Principle

**Never delete or rewrite history.** Campaign files are the permanent record of what happened. You may add, but not remove.

When a known entity has new information:

```markdown
## Session 7 Update

[New information added this session. What changed, what was revealed.]
```

Add this block at the end of the relevant section, not inline with existing text.

### When to Edit Existing Content

Editing (not appending) is only appropriate for:
- Fixing a factual error you introduced in the same session's debrief (not in a prior session)
- Updating the `last_seen` and `status` frontmatter fields
- Updating the campaign-context.md "Campaign Status" section

For everything else, append.

### Continuity Flags

When debrief output contradicts something in an existing file, **do not silently resolve the contradiction**. Instead:

1. Note it in the session recap under "Continuity Flags"
2. Note it in the debrief report to the user
3. Leave both the old file and the new information in place until the DM resolves it

The DM resolves continuity flags. The agent records them.

---

## Linking Standards

### Standard Mode

Internal references use relative markdown links:

```markdown
[Lady Morvaine](../npcs/lady-morvaine.md)
[Silver Flagon](../locations/silver-flagon.md)
```

From inside `sessions/`: path is `../npcs/slug.md`
From inside `npcs/`: path to locations is `../locations/slug.md`

### Obsidian Mode

All internal references use wikilinks:

```markdown
[[Lady Morvaine]]
[[Silver Flagon]]
```

The wikilink uses the NPC/location's display name (`name:` field in frontmatter), not the file slug.

Do not mix styles. If `obsidian: true`, use wikilinks everywhere. If `obsidian: false` or unset, use relative markdown links.

---

## Tag Taxonomy

Used in Obsidian mode. All campaign tags use the `campaign/` prefix.

```
campaign/session          — session recap files
campaign/session-plan     — session plan files
campaign/npc              — NPC profile files
campaign/location         — location profile files
campaign/faction/[slug]   — NPC affiliated with a specific faction
campaign/region/[slug]    — location within a specific region
campaign/world-bible      — world bible file
campaign/visited          — location visited by the party
campaign/mentioned        — location known but not visited
```

Apply the most specific tags available. An NPC affiliated with the Merchants' Guild gets both `campaign/npc` and `campaign/faction/merchants-guild`.

---

## timeline.md Format

```markdown
# Campaign Timeline

## Session 1 — [In-world date or "Beginning"]
- [Event 1]
- [Event 2]
- NPC introduced: [Name]
- Location discovered: [Name]

## Session 2 — [In-world date or "After the events at Ashford"]
- [Event]
```

Append new session blocks at the bottom. Do not reorder.

If the campaign uses in-world dates, include them. If not, use session number and relative references ("three days later", "the following winter").

---

## Obsidian Wikilink Generation

When processing files in Obsidian mode:

1. During Phase 1 (context loading), build a **known world index**: a map of `display_name → file_slug` for all existing NPC and location files.

2. After generating all file content (not during), do a post-processing pass on each file's body text:
   - For each known entity name, wrap the first occurrence in each paragraph with `[[Name]]`
   - Do not double-wikilink: if `[[Lady Morvaine]]` already exists in a sentence, do not re-wrap
   - Do not wikilink generic nouns: "the tavern", "the city", "the guard" are not wikilinks even if a specific tavern/city/guard has a file
   - Use the display name from `name:` frontmatter, not the file slug

3. Apply wikilinks to the recap file and any newly created/updated NPC and location files.
