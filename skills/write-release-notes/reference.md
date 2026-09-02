# Release Notes Writing Reference

## Detailed Guidelines

### Tone & Style
- **Fun and conversational**: Write like you're talking to friends about a cool new feature
- **Enthusiastic but not over-the-top**: Show genuine excitement without being cringey
- **Problem-solving focus**: Start with the problem users face, then show how this solves it
- **Accessible language**: Avoid overly technical jargon unless necessary
- **Emojis strategically**: Use 1-2 relevant emojis in the headline and closing, not everywhere

### Length Guidelines
- **Target: 30-40 lines** for Discord sharing
- **Maximum: 50 lines** - if longer, trim or reorganize
- **Each section**: 3-7 bullet points max
- **Command examples**: Show 3-5 most common use cases, not every possible command

## Section Details

### 1. Headline (Required)
- **Format**: Action-oriented, benefit-focused
- **Examples**: 
  - "Never Forget Your Turn Again! ⏰"
  - "Roll Your Way: Custom Dice Have Arrived! 🎲"
  - "Cards Everywhere: Game Board & Custom Piles! 🃏"
- **Emoji**: Choose one that represents the feature visually

### 2. Opening Hook (Required)
- **First line**: Bold, one-sentence value proposition
- **Follow-up**: Real-world scenarios users can relate to
- **End with**: What the feature enables

### 3. How to Use (Required)
- **Format**: Bullet points with actual command syntax
- **Include**: 3-5 most common/important commands
- **Group**: If multiple command groups, use subheadings
- **Description**: Keep to 5-10 words max per command

### 4. What's Cool (Required)
- **Format**: Bold feature name, then explanation
- **Focus**: User benefits, not implementation details
- **Include**: 4-7 key features that make this special
- **Use cases**: Mention specific game types or scenarios when relevant

### 5. Example/Scenario (Optional but Recommended)
- **Purpose**: Show the feature in action
- **Include**: Actual command + expected output
- **Format**: Can be inline or code block for multi-step flows

### 6. Technical Notes (Required)
- **Keep it brief**: 3-5 bullet points max
- **Focus**: What changed (new files, data structures, dependencies)
- **Avoid**: Deep implementation details, code snippets
- **Include**: New dependencies, major architectural changes, migration notes

### 7. Closing (Optional but Recommended)
- **Format**: One sentence summarizing who benefits
- **Emojis**: Match headline emoji + sparkle

## Common Patterns

### For New Commands
- Emphasize the natural language/autocomplete features
- Show the most common use cases first
- Include error handling or edge cases if notable

### For Feature Extensions
- Highlight what's new vs. what already existed
- Show how existing commands changed
- Emphasize backward compatibility

### For Complex Features
- Break into logical groups (like Game Board vs. Custom Piles)
- Use subheadings in "How to use"
- Provide a complete example workflow

### For Bug Fixes
- Focus on what was broken and how it's fixed
- Less emphasis on "what's cool" (unless the fix adds new capabilities)
- Keep technical notes brief

## Anti-Patterns to Avoid

### ❌ Too Formal
- "This release introduces..."
- "We are pleased to announce..."
- "The following changes have been implemented..."

### ❌ Too Technical
- Deep code explanations
- Implementation details users don't need
- Architecture diagrams or complex schemas

### ❌ Too Long
- Every possible command variation
- Extensive technical documentation
- Multiple examples of the same concept

### ❌ Too Vague
- "Improved card management"
- "Better user experience"
- "Enhanced functionality"

### ❌ Missing Context
- Commands without examples
- Features without use cases
- Changes without "why"

## Examples Reference

### Good Opening Examples
- "Ever wanted to roll a die that says 'Attack', 'Defend', 'Heal'? Or a fruit die with 🍎🍊🍇🍌? Now you can!"
- "Forget to check back on your game? Need a nudge when it's your turn tomorrow? Just tell the bot 'remind me in 2 hours'..."
- "Ever needed a 'Market Row' for deck builders? A face-up 'River' for drafting? A secret 'Buried Treasure' pile?"

### Good Feature Descriptions
- "**Natural language parsing**: Write times like you'd say them—'in 5 minutes', 'tomorrow afternoon', 'next Friday at 2pm'"
- "**Secret piles**: Hide card contents from other players (perfect for hidden markets or buried cards)"
- "**Autocomplete magic**: Never have to remember exact die names—just start typing and see what's available"

### Good Technical Notes
- "Uses `chrono-node` for robust natural language date parsing"
- "New `gameBoard` array and `globalPiles` array in game data structure"
- "Extended existing card commands (`discard`, `play`, `return`, `flip`, `burn`) with `destination` parameter"

## Final Notes

Remember: Release notes are marketing for your features. They should make users excited to try the new functionality. Write for the user who's skimming in Discord, not the developer reading the changelog. Keep it fun, keep it useful, keep it concise!
