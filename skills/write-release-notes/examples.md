# Release Notes Examples

Real examples from past releases showing the style and format.

## Example 1: Reminders Feature

```markdown
## Never Forget Your Turn Again! ⏰

**Set reminders using natural language—the bot will tag you when it's time.**

Forget to check back on your game? Need a nudge when it's your turn tomorrow? Just tell the bot "remind me in 2 hours" or "tomorrow at 5pm" and it'll ping you. No more "oops, I forgot it was my turn three days ago." 

### How to use:

* `/reminder when:"in 30 minutes" message:"Check the game"` - Basic reminder
* `/reminder when:"tomorrow at 9am" message:"My turn in Inis"` - Specific time
* `/reminder when:"Friday at 3pm" message:"Game night!"` - Future date
* `/reminder when:"next Monday" message:"Post scores"` - Natural language

### What's cool:

* **Natural language parsing**: Write times like you'd say them—"in 5 minutes", "tomorrow afternoon", "next Friday at 2pm"
* **Discord timestamps**: Confirmation shows the time in your timezone with fancy Discord formatting
* **Persistent storage**: Reminders survive bot restarts—set it and forget it
* **Background delivery**: Bot checks every 30 seconds and delivers right on time
* **Channel-specific**: Reminder gets sent to the channel where you set it

**Example:** `/reminder when:"in 10 minutes" message:"Roll for initiative"`  
Bot replies: "I've set a reminder for **Roll for initiative** on Tuesday, January 21, 2025 10:30 AM (in 10 minutes)."

Then 10 minutes later: "@YourName Reminder: Roll for initiative"

### Technical notes:

* Uses `chrono-node` for robust natural language date parsing
* Reminders stored in Enmap with `nanoid` IDs for persistence
* `ReminderSystem` module checks due reminders every 30 seconds
* Permission checks ensure bot can send before attempting delivery
* Failed reminders (deleted channels, etc.) are logged and cleaned up

Perfect for turn-based games, scheduled sessions, or just remembering to check back! ⏰✨
```

**Analysis:**
- ✅ Catchy headline with emoji
- ✅ Problem-focused opening (forgetting turns)
- ✅ 4 command examples with real syntax
- ✅ 5 cool features (user benefits)
- ✅ Concrete example showing before/after
- ✅ 5 technical notes (brief)
- ✅ 36 lines total (perfect length)
- ✅ Conversational tone throughout

## Example 2: Card Piles & Game Board

```markdown
## Cards Everywhere: Game Board & Custom Piles! 🃏

**Create shared play areas, custom card piles, and route cards anywhere you need them.**

Ever needed a "Market Row" for deck builders? A face-up "River" for drafting? A secret "Buried Treasure" pile? Now you can! The Game Board gives you a shared play area visible to everyone, and Custom Piles let you create any card zones your game needs—public or secret, with flexible configurations.

### How to use:

**Game Board (shared play area):**
* `/cards gameboard view` - See all cards on the board
* `/cards gameboard take card:[CardName] destination:[hand/pile]` - Grab a card
* `/cards gameboard discard card:[CardName]` - Send a card to discard
* `/cards gameboard clear` - Clear the entire board

**Custom Piles:**
* `/cards pile create name:Market secret:false` - Make a new pile
* `/cards pile list` - See all piles and their card counts
* `/cards pile configure pile:Market config:show_top value:true` - Show the top card
* `/cards pile play card:[CardName] pile:Market` - Play from hand to pile
* `/cards pile take card:[CardName] pile:Market` - Take from pile to hand
* `/cards pile draw pile:Market count:3` - Draw cards from pile
* `/cards pile view pile:Market` - See all cards in pile (if allowed)
* `/cards pile peek pile:Market count:5` - Peek at top cards
* `/cards pile shuffle pile:Market` - Shuffle the pile
* `/cards pile flip pile:Market` - Flip top card face-up/down
* `/cards pile delete pile:Market` - Remove the pile

**Route cards to new destinations:**
* `/cards hand discard card:[CardName] destination:gameboard` - Discard to board instead
* `/cards hand discard card:[CardName] destination:pile pilename:Market` - Discard to custom pile
* `/cards hand play card:[CardName] destination:pile pilename:Market` - Play to pile
* `/cards hand return card:[CardName] destination:pile pilename:Market` - Return to pile
* `/cards flip card:[CardName] destination:gameboard` - Flip to board
* `/cards burn card:[CardName] destination:pile pilename:Removed` - Burn to pile

### What's cool:

* **Secret piles**: Hide card contents from other players (perfect for hidden markets or buried cards)
* **Show top card**: Configure piles to display their top card in game status
* **Autocomplete everywhere**: All pile and card selections use smart autocomplete
* **Game Board images**: Cards with URLs show as images in the status
* **Flexible routing**: Send cards to discard (default), game board, or any custom pile
* **History tracking**: All pile actions recorded in game history
* **Status integration**: Game Board and Piles show in `/game status` automatically

**Example setup for a deck builder:**
```
/cards pile create name:Market secret:false
/cards pile configure pile:Market config:show_top value:true
/cards pile draw pile:MainDeck count:5 destination:pile pilename:Market
```
Now everyone sees 5 market cards, and the top card displays in status!

### Technical notes:

* New `gameBoard` array and `globalPiles` array in game data structure
* `defaultGlobalPile` schema with configurable `isSecret`, `viewable`, `showTopCard` options
* `GlobalGameHelper` provides CRUD operations and autocomplete for piles
* `GameFormatter` renders Game Board and Piles in status with image generation
* Extended existing card commands (`discard`, `play`, `return`, `flip`, `burn`) with `destination` parameter
* 13 new pile subcommands for complete pile management
* All actions properly recorded in game history with public/private message handling

Perfect for deck builders, drafting games, market mechanics, and any game needing flexible card zones! 🃏✨
```

**Analysis:**
- ✅ Catchy headline with emoji
- ✅ Opening with relatable scenarios (Market Row, River, Buried Treasure)
- ✅ Commands grouped logically (Game Board, Custom Piles, Routing)
- ✅ 7 cool features (user benefits)
- ✅ Multi-step example workflow
- ✅ 7 technical notes (still concise)
- ✅ 65 lines (slightly longer but acceptable for complex feature)
- ✅ Conversational tone with game-specific examples

## Key Takeaways

1. **Headlines**: Action-oriented, benefit-focused, with emoji
2. **Openings**: Start with relatable problems/scenarios
3. **Commands**: Real syntax with actual parameters
4. **Features**: User benefits, not implementation
5. **Examples**: Show the feature in action
6. **Technical**: Brief, what changed not how
7. **Length**: 30-50 lines ideal, can go slightly longer for complex features
8. **Tone**: Conversational, enthusiastic, problem-solving
