---
name: write-release-notes
description: Write engaging Discord-friendly release notes with fun conversational tone. Use when creating release notes, changelogs, release announcements, or when documenting new features for users. Follows Discord-optimized format (30-50 lines) with catchy headlines, command examples, and user-focused benefits.
---

# Writing Release Notes

## Quick Start

When writing release notes:

1. **Headline**: Catchy title with emoji (e.g., "Never Forget Your Turn Again! ⏰")
2. **Opening**: One-line benefit + 2-3 sentences explaining problem → solution
3. **Commands**: 3-5 actual command examples with syntax
4. **Features**: 4-7 user benefits (not implementation details)
5. **Example**: Show feature in action
6. **Technical**: 3-5 brief notes on what changed
7. **Closing**: One sentence + emoji

**Target length**: 30-50 lines for Discord sharing

## Core Principles

- **Tone**: Fun, conversational, enthusiastic (not formal)
- **Focus**: User benefits, not implementation
- **Length**: 30-40 lines target, 50 max
- **Emojis**: 1-2 in headline/closing only
- **Commands**: Show real syntax with examples

## Structure Template

```markdown
## [Catchy Title with Emoji] 🎲

**[One-line benefit statement]**

[2-3 sentences: problem → solution with relatable scenarios]

### How to use:

* `/command example` - Brief description
* `/command example` - Brief description

### What's cool:

* **Feature name**: Brief explanation with use case
* **Feature name**: Brief explanation with use case

**Example:** `/command example`  
Result: "What happens"

### Technical notes:

* Brief technical detail (what changed)
* Brief technical detail

Perfect for [use cases]! 🎲✨
```

## Writing Guidelines

### Headlines
- ✅ "Never Forget Your Turn Again!"
- ✅ "Cards Everywhere: Game Board & Custom Piles!"
- ❌ "Added Reminder Functionality"

### Opening
- ✅ Start with relatable scenarios: "Ever needed a Market Row for deck builders?"
- ✅ Use questions: "Forget to check back on your game?"
- ❌ "This release introduces..."

### Commands
- ✅ Real syntax: `/reminder when:"in 10 minutes" message:"Check game"`
- ✅ Include parameters: `name:Market`, `count:5`
- ❌ Generic descriptions without commands

### Features
- ✅ "**Autocomplete**: Type and see all available dice—no memorizing names"
- ❌ "Autocomplete functionality has been implemented"

## Quality Checklist

Before finalizing:
- [ ] Headline catchy with emoji
- [ ] Opening explains problem → solution
- [ ] 3-5 command examples (actual syntax)
- [ ] 4-7 cool features (user benefits)
- [ ] 1 example/scenario included
- [ ] 3-5 technical notes (brief)
- [ ] Total length: 30-50 lines
- [ ] Tone is conversational and fun
- [ ] Real-world use cases mentioned

## Additional Resources

- For detailed writing guide and examples, see [reference.md](reference.md)
- For copy-paste template, see [template.md](template.md)
- For real examples from past releases, see [examples.md](examples.md)
