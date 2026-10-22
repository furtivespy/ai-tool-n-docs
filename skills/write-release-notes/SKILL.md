---
name: write-release-notes
description: Write tight Discord-friendly release notes. Use when creating release notes, changelogs, release announcements, or documenting new features for users. Short skim, catchy headline, real commands, user benefits.
---

# Writing Release Notes

Write **tight**. Discord skim, not a blog post. Draft, then cut until every line earns its place.

## Quick Start

1. **Headline**: Catchy title + one emoji
2. **Opening**: Bold one-liner. At most one extra sentence.
3. **Commands**: 2–4 real examples, syntax only
4. **Features**: 3–4 user benefits, one clause each
5. **Example**: One command + one result line
6. **Technical**: 2–3 short bullets
7. **Closing**: One sentence + emoji

**Target: 15–25 lines. Hard cap: 30.** If you hit 30, cut — never add a section to “fill it out.”

## How to deliver

Always send **both**, in this order:

1. **Formatted** — the notes as normal markdown in the reply so they can read them
2. **Copy-paste** — the exact same notes in one fenced `markdown` code block

The code block is the paste payload for Discord or GitHub. No extra commentary inside it. Do not skip it.

## Core Principles

- **Tight**: short clauses, no scene-setting, no restating a bullet in prose
- **Tone**: Fun, conversational (not formal)
- **Focus**: User benefits, not implementation
- **Emojis**: 1–2 in headline/closing only
- **Commands**: Real syntax

## Structure Template

```markdown
## [Catchy Title] 🎲

**[One-line benefit]**

[Optional: one short sentence]

### How to use:

* `/command example` - Brief
* `/command example` - Brief

### What's cool:

* **Feature**: One clause
* **Feature**: One clause

**Example:** `/command example` → result

### Technical notes:

* What changed
* What changed

Perfect for [use case]! 🎲✨
```

## Writing Guidelines

### Headlines
- ✅ "Never Forget Your Turn Again!"
- ❌ "Added Reminder Functionality"

### Opening
- ✅ One benefit line. Optional: one problem→solution sentence.
- ❌ A paragraph of vibe, three scenarios, then the feature

### Commands
- ✅ `/reminder when:"in 10 minutes" message:"Check game"`
- ❌ Every flag, every game name, button-by-button narration

### Features
- ✅ "**Autocomplete**: Type and see available dice"
- ❌ A second sentence explaining the first

## Done when

- [ ] Under 30 lines (count them)
- [ ] Opening is 1–2 sentences total
- [ ] No bullet has a follow-up sentence
- [ ] You would paste it into Discord as-is
- [ ] Reply includes the formatted notes **and** a fenced `markdown` copy-paste block with the same text

## Additional Resources

- Tone, patterns, anti-patterns: [reference.md](reference.md)
- Copy-paste skeleton: [template.md](template.md)
- Tight finished examples: [examples.md](examples.md)
