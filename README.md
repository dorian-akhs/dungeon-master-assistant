# Dungeon Master Assistant

A co-DM skill for AI coding assistants that manages your tabletop RPG campaign through structured markdown files. Inspired by the [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD) — specialized agents, markdown-as-code, human-in-the-loop collaboration.

You remain the storyteller. The AI handles documentation, analysis, and prep.

## What It Does

You run your TTRPG sessions. Afterwards, you hand the AI a transcript (or describe what happened from memory), and it:

- **Debriefs your session** — creates a structured recap, updates NPC and location files, appends to the timeline, flags continuity contradictions
- **Builds your world** — guided interview across cosmology, geography, history, factions, magic, and culture, producing a world bible
- **Architects your campaign** — dramatic question, antagonist force, player hooks, three-act structure
- **Plans your next session** — scenes, NPC voice cards, encounter pacing, contingency scenes
- **Checks continuity** — targeted fact-checks or full audits across your entire campaign corpus

All state lives in plain markdown files. No database, no app, no lock-in.

## How It Works

### The Memory System

Campaign state is a folder of markdown files with YAML frontmatter:

```
campaign-context.md       ← The constitution. Read first. Always.
world-bible.md            ← World lore — geography, history, factions, magic
timeline.md               ← Chronological event log
npcs/
  valdris-thornwood.md    ← One file per significant NPC
locations/
  sunken-archive.md       ← One file per significant location
factions/                 ← Optional — one file per faction
sessions/
  session-001-recap.md    ← Post-session debrief output
  session-002-plan.md     ← Pre-session prep output
campaign-arc/             ← Optional — plot threads, adventure arcs
  main-arc.md
```

Files follow an **append-only** principle — history is never deleted or rewritten. New information is added as dated update blocks. Contradictions are flagged for the DM to resolve, never silently merged.

### Agent Routing

The skill routes requests to the appropriate specialist agent:

| You want to...                                          | Agent                |
| ------------------------------------------------------- | -------------------- |
| Process a session transcript or update files after play  | **Debriefer**        |
| Build or expand the world                                | **World Builder**    |
| Design the campaign arc and plot threads                 | **Campaign Architect** |
| Prep the next session                                    | **Session Planner**  |
| Verify lore consistency                                  | **Continuity Checker** |

Each agent has its own detailed playbook under `agents/` with phase-by-phase instructions, edge case handling, and output formats.

## Getting Started

### Installation

This is a pure-markdown skill — no dependencies, no build step.

**Claude Code** (plugin marketplace):
```bash
/plugin marketplace add dorian-akhs/dungeon-master-assistant
/plugin install dungeon-master-assistant@dungeon-master-assistant
```

**Claude Code** (manual) — Add to your `CLAUDE.md` or `.claude/skills/`:
```
Read /path/to/dungeon-master-assistant/SKILL.md for TTRPG campaign management.
```

**Cursor** (marketplace) — Install from the [Cursor Marketplace](https://cursor.com/marketplace) by searching for `dungeon-master-assistant`.

**Cursor** (manual) — Add to your [skills configuration](https://docs.cursor.com/context/skills):

```json
{
  "skills": [
    {
      "path": "/path/to/dungeon-master-assistant/SKILL.md"
    }
  ]
}
```

The skill is designed to work with any LLM assistant that supports reading external instruction files.

### Bootstrap a New Campaign

If no `campaign-context.md` exists, the assistant will walk you through five questions:

1. **System** — What RPG system? (D&D 5e, Pathfinder 2e, Blades in the Dark, homebrew...)
2. **Setting** — What's the world? (Forgotten Realms, homebrew, historical, space opera...)
3. **Tone** — What's the feel? (Grimdark, swashbuckling, horror, high fantasy...)
4. **Players** — Who's at the table? (Characters, classes, backstory hooks)
5. **Obsidian** — Using Obsidian for notes? (Enables wikilinks and tag taxonomy)

From there, start debriefing sessions or build out your world bible.

### Quick Actions

```
Debrief my session — [attach transcript or paste it]
```
```
Process last night's session from memory: [describe what happened]
```
```
Build my world bible — let's start the interview
```
```
Design the main campaign arc
```
```
Plan session 5
```
```
Check continuity: is it consistent that Valdris grew up in Saltmere?
```
```
Run a full continuity audit
```

## Obsidian Integration

Set `obsidian: true` in `campaign-context.md` and all output becomes Obsidian-native:

- **Wikilinks** — `[[Lady Morvaine]]` instead of `[Lady Morvaine](../npcs/lady-morvaine.md)`
- **Properties format** — frontmatter styled for the Obsidian properties panel
- **Tag taxonomy** — `campaign/npc`, `campaign/location`, `campaign/faction/merchants-guild`
- **Aliases** — NPC alternate names for graph view matching
- **Dataview-ready** — frontmatter fields designed for Dataview queries out of the box

Your campaign root can be your vault or a subfolder within it. The graph view turns your campaign into an interconnected knowledge base — session recaps become hub nodes linking every NPC and location that appeared.

## System Agnostic

Works with any TTRPG: D&D, Pathfinder, Call of Cthulhu, Blades in the Dark, Shadowdark, Mörk Borg, FATE, homebrew systems, anything. The skill never assumes D&D rules unless your `campaign-context.md` says so. If you're running Blades in the Dark, agents speak in terms of clocks and positions — not hit points and spell slots.

System knowledge comes from three sources:
1. `campaign-context.md` — the `system:` field
2. `world-bible.md` — your magic/technology rules
3. Your answers during interviews and planning

## Project Structure

```
SKILL.md                            ← Main skill definition and routing
agents/
  debriefer.md                      ← Session transcript → structured files
  world-builder.md                  ← Guided interview → world bible
  campaign-architect.md             ← Plot design → campaign arc files
  session-planner.md                ← Campaign state → session prep
  continuity-checker.md             ← Lore audit → contradiction report
references/
  campaign-memory-system.md         ← Rules for reading/writing campaign files
  obsidian-integration.md           ← Obsidian properties, wikilinks, Dataview
templates/
  campaign-context.md               ← Campaign constitution scaffold
  world-bible.md                    ← World lore scaffold
  session-recap.md                  ← Post-session debrief scaffold
  session-plan.md                   ← Pre-session prep scaffold
  npc-profile.md                    ← NPC file scaffold
  location-profile.md               ← Location file scaffold
evals/
  evals.json                        ← 3 evaluation scenarios with assertions
  files/                            ← Transcripts and synthetic campaign fixtures
```

## Design Principles

**Append-only canon** — Files are the permanent record. History is never deleted, only extended with dated update blocks. This preserves auditability and prevents the AI from "rewriting history."

**DM resolves contradictions** — When session output conflicts with established files, agents raise continuity flags. They never silently merge conflicting information. The DM is always the final authority on what's canon.

**Structure, not story** — The Campaign Architect designs scaffolding (dramatic questions, three-act outlines, player hooks), never plot that requires specific PC choices. Player agency is sacred.

**Conservative file creation** — Not every NPC gets a file. Thresholds are explicit: named characters with speaking roles or 2+ session mentions. This keeps the campaign folder signal-rich.

**Separation of known and secret** — NPC files and the world bible have "DM Secrets" sections, kept separate from player-known facts. The Continuity Checker respects this boundary to avoid spoiler leakage.

## Evaluations

The `evals/` directory contains three behavioral test scenarios:

| Eval | Tests | Key Assertions |
|------|-------|----------------|
| **1** | Standard debrief from transcript | New recap, NPC creation/updates, append-only timeline, continuity flag for contradictions, OOC note capture |
| **2** | Obsidian mode output | Wikilinks, properties format, tag taxonomy, aliases, no mixed link styles, safe filenames |
| **3** | Memory-only debrief (no transcript) | Reconstruction disclaimer, long-running timeline integrity, NPC stub creation from plot context |

Each eval includes input files (transcripts, pre-existing campaign state) and a checklist of assertions the output must satisfy.

## Contributing

This is a markdown-only project — no code to build or test in the traditional sense. Contributions that improve agent instructions, add templates, refine the memory system rules, or expand eval coverage are welcome.

## License

[MIT](LICENSE)
