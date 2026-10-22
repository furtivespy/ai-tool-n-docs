# Release Notes Writing Reference

Length and section caps live in [SKILL.md](SKILL.md). This file is tone and patterns only.

## Tone

- Fun and conversational, like telling a friend
- Enthusiastic, not cringey
- User-facing words, not jargon
- Emojis only in the headline and closing

## Patterns

**New commands:** Show the command people will actually type. Autocomplete if it saves them remembering names.

**Feature extensions:** What is new vs what already existed. One line on compatibility if it matters.

**Complex features:** Group commands with a subheading. Still stay under the line cap — drop groups, do not narrate all of them.

**Bug fixes:** What was broken, what works now. Skip “what’s cool” unless the fix adds a capability.

## Anti-patterns

### Formal
- "This release introduces..."
- "We are pleased to announce..."

### Technical dump
- File names, schemas, architecture — unless a migration needs them

### Padded
- A prose recap of the bullets
- Three scenarios when one will do
- Every command variation

### Vague
- "Improved card management"
- "Better user experience"

## Tight examples (steal these)

**Opening:** "Forget to check back on your game? Tell the bot 'remind me in 2 hours.'"

**Feature:** "**Secret piles**: Hide a market or buried cards from the table"

**Technical:** "Uses `chrono-node` for natural-language dates"
