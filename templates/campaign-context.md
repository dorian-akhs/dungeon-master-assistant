---
system: "D&D 5e"
edition: "2024"
setting: "Homebrew"
tone: "heroic fantasy"
campaign_name: "Untitled Campaign"
start_date: ""
lang: ""
obsidian: false
obsidian_vault_root: "."
house_rules: []
players:
  - name: "Player 1"
    character: "Character Name"
    class: "Fighter"
    level: 1
    pronouns: "they/them"
    player_hooks: []
---

# Campaign Context

This file is the constitution of your campaign. Every AI agent reads it first before taking any action. Keep it up to date — it is the single source of truth for the campaign's fundamental facts.

Do not delete this file. Add to it as your campaign evolves.

---

## Language

The `lang:` field in the frontmatter sets the default language for all agent responses. Leave it empty to have agents mirror the language of your input automatically.

Set it when your campaign files are written in a specific language but you sometimes send messages in another:

```yaml
lang: "fr"   # French
lang: "es"   # Spanish
lang: "it"   # Italian
lang: "de"   # German
lang: "en"   # English (default)
```

Frontmatter keys, file names, slugs, and tags always remain in English regardless of this setting.

---

## Campaign Premise

[2–3 sentences. What kind of story is this? Who are the protagonists, and what is the central conflict or driving question?]

## Tone & Themes

[What emotional register does this campaign operate in? What themes recur — redemption, power, loss, discovery? What does it feel like to play in this world?]

## The World in Brief

[1 paragraph. The elevator pitch for the setting. Detailed world-building lives in `world-bible.md`.]

## House Rules

[List rules that deviate from the base system. Be specific so the Rules Consultant agent can apply them correctly.]

- Example: Milestone leveling (no XP tracking)
- Example: Flanking does not grant advantage

## Player Characters

[Summarize each PC beyond the frontmatter. Include backstory hooks, personality, goals, and relationships that are relevant to the campaign.]

### [Character Name]

- **Player**: [Player name]
- **Background**: [Brief backstory]
- **Goals**: [What does this character want?]
- **Secrets**: [What don't the other PCs know?]
- **Hooks**: [Campaign threads tied to this character]

## What the PCs Don't Know

[Spoiler space. Record unrevealed information here — villain identities, hidden agendas, future reveals — so the Continuity Checker can validate consistency without the risk of accidentally revealing it in session prep.]

## Campaign Status

[Running notes on where the campaign is right now. Update after each session debrief. What's the current situation? Where are the PCs? What are the active threats?]

## DM Notes

[Free space for private notes. Not processed by agents unless you explicitly ask. Use this for brainstorming, reminders, and ideas that aren't ready to be canon yet.]
