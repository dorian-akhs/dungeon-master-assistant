---
name: dungeon-master-assistant
description: >
  Co-DM assistant for tabletop RPG campaigns. Use this skill whenever a DM or GM
  mentions session recaps, session debriefs, processing a session transcript, updating
  campaign notes, NPC tracking, world-building, lore consistency, session prep, campaign
  arcs, adventure planning, faction tracking, location discovery, or timeline management.
  Also trigger for any mention of D&D, Pathfinder, Call of Cthulhu, Blades in the Dark,
  Shadowdark, Mörk Borg, or any other tabletop RPG system in the context of managing
  campaign content.
  English trigger phrases: "debrief my session", "process my transcript",
  "update my campaign files", "create an NPC profile", "plan next session", "check lore
  consistency", "build my world bible", "what happened last session", "check continuity".
  French trigger phrases: "débriefe ma session", "compte-rendu de session", "résumé de
  séance", "traite ma transcription", "mets à jour mes notes de campagne", "crée un profil
  de PNJ", "prépare la prochaine séance", "vérifie la cohérence du lore", "vérifie la
  continuité", "construis ma bible du monde", "qu'est-ce qui s'est passé la dernière
  séance", "jeu de rôle", "maître du jeu", "MJ".
  Spanish trigger phrases: "resumen de sesión", "debriefing de sesión", "procesa mi
  transcripción", "actualiza mis notas de campaña", "crea un perfil de PNJ", "planifica
  la próxima sesión", "verifica la consistencia del lore", "comprueba la continuidad",
  "construye mi biblia del mundo", "qué pasó en la última sesión", "juego de rol",
  "máster", "director de juego", "DJ".
  Italian trigger phrases: "resoconto della sessione", "debriefa la mia sessione",
  "elabora la mia trascrizione", "aggiorna le mie note di campagna", "crea un profilo PNG",
  "pianifica la prossima sessione", "verifica la coerenza del lore", "controlla la
  continuità", "costruisci la mia bibbia del mondo", "cosa è successo nell'ultima
  sessione", "gioco di ruolo", "master", "narratore".
  German trigger phrases: "Sitzungszusammenfassung", "debriefe meine Sitzung",
  "verarbeite mein Transkript", "aktualisiere meine Kampagnendateien", "erstelle ein
  NSC-Profil", "plane die nächste Sitzung", "überprüfe die Lore-Konsistenz", "überprüfe
  die Kontinuität", "erstelle meine Weltbibel", "was ist in der letzten Sitzung passiert",
  "Rollenspiel", "Spielleiter", "SL".
  Use this skill even if the user doesn't explicitly call it a "campaign" — if someone is
  running a game and wants to document or plan it, this skill applies.
---

# Dungeon Master Assistant — Co-DM Skill

This skill helps you manage your tabletop RPG campaign through structured markdown files. You remain the storyteller; Claude handles the documentation, analysis, and prep work.

---

## Language Policy

Always detect the language of the user's input and respond entirely in that language — questions, summaries, section headers in your output, and all conversational text.

If `lang:` is set in `campaign-context.md`, treat it as the default response language even when the user's message is in another language (useful when the campaign files are in a different language than the conversation).

**What stays in English regardless of language:**
- Frontmatter keys (`system:`, `obsidian:`, `lang:`, etc.)
- File names and slugs (`npcs/lady-morvaine.md`)
- Obsidian wikilinks (link targets must match file names)
- Tags (`campaign/npc`, `campaign/location`, etc.)

Everything else — prose, questions, section bodies, debrief reports, voice prep cards, continuity reports — is written in the detected or configured language.

All campaign state lives in markdown files in your working directory. Every agent reads `campaign-context.md` first — keep it current and the whole system works.

---

## Agent Routing

Choose the agent based on what the user wants to do:

| User wants to...                                                             | Agent                  | File                           |
| ---------------------------------------------------------------------------- | ---------------------- | ------------------------------ |
| Process a session transcript or notes, update campaign files after a session | **Debriefer**          | `agents/debriefer.md`          |
| Build or expand the world — geography, factions, history, magic system       | **World Builder**      | `agents/world-builder.md`      |
| Design the campaign arc, player hooks, three-act structure, plot threads     | **Campaign Architect** | `agents/campaign-architect.md` |
| Prep the next session — scenes, NPC voice, encounter balance                 | **Session Planner**    | `agents/session-planner.md`    |
| Check whether a specific fact is consistent with established lore            | **Continuity Checker** | `agents/continuity-checker.md` |

When the user's request clearly maps to one agent, read that agent's file and follow its instructions. When it's ambiguous, ask which they want.

---

## Campaign Memory Structure

All campaign files live in the **campaign root** — wherever `campaign-context.md` is. Use this layout:

```
campaign-context.md       # The constitution. Read first. Always.
world-bible.md            # World lore — geography, history, factions, magic
timeline.md               # Chronological event log
npcs/
  [name-slug].md          # One file per significant NPC
locations/
  [name-slug].md          # One file per significant location
factions/                 # Optional — one file per faction
sessions/
  session-001-recap.md    # Created by debriefer after each session
  session-002-plan.md     # Created by session planner before each session
campaign-arc/             # Optional — plot threads, adventure arcs
  main-arc.md
```

**File creation thresholds:**

| Entity         | Create a file when...                                       | Otherwise...               |
| -------------- | ----------------------------------------------------------- | -------------------------- |
| NPC            | Named character with speaking role, OR named in 2+ sessions | Note in session recap only |
| Location       | Party visited it, OR named place with significant lore      | Note in session recap only |
| Timeline entry | Event with future consequence or revealed cause             | Skip                       |

**Modification rule:** Files are append-only. Never delete or rewrite history. For known entities with new information, add a dated update block. See `references/campaign-memory-system.md` for full rules.

---

## Obsidian Mode

If `obsidian: true` in `campaign-context.md`, all agents produce Obsidian-compatible output:

- Internal references use `[[wikilinks]]` instead of markdown links
- Frontmatter uses Obsidian properties format (no quotes on plain strings, block-style arrays)
- Files use the `campaign/` tag taxonomy
- NPC files include an `aliases:` array
- File names are Obsidian-safe (no `:`, `|`, `#`, `^`)

The campaign root can be your Obsidian vault or a subfolder within it — set `obsidian_vault_root` in `campaign-context.md` if it's a subfolder.

Full Obsidian integration guide: `references/obsidian-integration.md`

---

## Bootstrap: Starting a New Campaign

If `campaign-context.md` doesn't exist in the working directory, offer to create it:

Ask the DM these 5 questions (one at a time, not as a list):

1. **System**: What RPG system are you running? (D&D 5e, Pathfinder 2e, Blades in the Dark, homebrew, etc.)
2. **Setting**: What's the setting? (Forgotten Realms, homebrew world, historical Earth, space opera, etc.)
3. **Tone**: What's the feel of this campaign? (Grimdark, swashbuckling, horror, high fantasy, etc.)
4. **Players**: How many players, and what are their characters? (Name, class/role, any backstory hooks you've established)
5. **Obsidian**: Are you using Obsidian for your notes? (Yes/No — enables wikilinks and tags if yes)

Once you have these answers, create `campaign-context.md` from `templates/campaign-context.md` with the responses filled in.

Suggested next step after bootstrap: run the World Builder to create `world-bible.md`, or start debriefing sessions immediately if the campaign has already started — the debriefer can bootstrap world knowledge from transcripts.

---

## Reference Files

| File                                   | When to read it                                                                                 |
| -------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `references/campaign-memory-system.md` | Any time you're reading from or writing to campaign files — this defines the rules              |
| `references/obsidian-integration.md`   | When `obsidian: true` and you need the full properties format, tag taxonomy, or Dataview fields |

---

## Quick Actions

Common invocations the DM can use:

```
Debrief my session — [attach transcript.txt or paste transcript]
```

```
Process last night's session from memory: [describe what happened]
```

```
Build my world bible — let's start the interview
```

```
Design the main campaign arc for [campaign name]
```

```
Plan session [N]
```

```
Check continuity: is it consistent that [specific claim]?
```

```
Run a full continuity audit
```

```
Create an NPC profile for [name]: [brief description]
```

```
Create a location profile for [place]: [brief description]
```

---

## System-Agnostic Design

This skill works with any TTRPG system. All system-specific knowledge comes from:

1. `campaign-context.md` — the `system:` field tells agents what game you're playing
2. `world-bible.md` — the magic/tech system section captures your game's specific mechanics
3. The DM's answers during interview and planning

Agents never assume D&D rules unless the campaign context says so. If the system field is "Blades in the Dark", agents speak in terms of clocks, positions, and effects — not hit points and spell slots.
