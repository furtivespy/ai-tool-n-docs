# Release Notes Examples

These match the line cap in [SKILL.md](SKILL.md). Copy the density, not a longer riff on the same feature.

## Reminders

```markdown
## Never Forget Your Turn Again! ⏰

**Set a reminder in plain English and the bot tags you when it's time.**

### How to use:

* `/reminder when:"in 30 minutes" message:"Check the game"`
* `/reminder when:"tomorrow at 9am" message:"My turn in Inis"`
* `/reminder when:"Friday at 3pm" message:"Game night!"`

### What's cool:

* **Plain English**: "in 5 minutes", "tomorrow afternoon", "next Friday at 2pm"
* **Your timezone**: confirmation uses Discord timestamps
* **Survives restarts**: set it and forget it

**Example:** `/reminder when:"in 10 minutes" message:"Roll for initiative"`  
→ "@You Reminder: Roll for initiative" ten minutes later

### Technical notes:

* `chrono-node` parses the when-text
* Stored in Enmap; background check every 30 seconds

Perfect for turns, sessions, or "I'll check back later"! ⏰✨
```

## Cards: Game Board & Piles

```markdown
## Cards Everywhere: Game Board & Custom Piles! 🃏

**Shared play areas and custom piles — public or secret — and you can send cards to either.**

Ever need a Market Row, a face-up River, or buried treasure? Now you can.

### How to use:

**Game Board:**
* `/cards gameboard view` - See the board
* `/cards gameboard take card:[CardName]` - Grab a card

**Piles:**
* `/cards pile create name:Market secret:false` - New pile
* `/cards pile draw pile:Market count:3` - Draw from it

**Route from hand:**
* `/cards hand discard card:[CardName] destination:gameboard`

### What's cool:

* **Secret piles**: Hidden markets or buried cards
* **Show top card**: Surface it in game status
* **Autocomplete**: Piles and cards, no memorizing names

**Example:** `/cards pile create name:Market` then draw 5 into it — everyone sees the market.

### Technical notes:

* New `gameBoard` and `globalPiles` on game data
* Existing discard/play/return/flip/burn take a `destination`

Perfect for deck builders, drafts, and markets! 🃏✨
```
