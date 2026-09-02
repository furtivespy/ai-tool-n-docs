---
name: beginbot-suno
description: Format text as song lyrics for Suno AI music generation. Use when the user wants to convert text (from a webpage, document, pasted content, or any source) into properly formatted lyrics with verses, choruses, bridges, and other song structure tags that Suno understands.
---

# Suno Lyrics Formatter

Convert any text source into properly formatted song lyrics for Suno AI.

## Critical Rule: Never Alter Content

**Do not change, rewrite, or paraphrase the original words.** Only add:
- Line breaks
- Commas for phrasing
- Paragraph breaks between sections
- Structure tags

The user's words are sacred - formatting only.

## Input Sources

Accept lyrics from:
- Pasted text directly in the conversation
- Webpage URLs (fetch and extract text)
- Documents (read file contents)
- Any text the user provides

## Formatting Rules

### Line Length
- Keep lines **shorter than a sentence**
- Aim for 4-8 words per line
- Break at natural breath points
- Break before conjunctions (and, but, or, so)
- Break after commas or natural pauses

### Adding Commas
- Insert commas at natural pause points
- Use commas to create phrasing within lines
- Commas help singers know where to breathe

### Paragraph Breaks
- Blank line between distinct thoughts
- Blank line before repeated phrases (choruses)
- Blank line at mood or tempo shifts
- Group related lines together (4-6 lines typical)

## Suno Structure Tags

Place on their own line before each section:

```
[Intro]
[Verse 1]
[Verse 2]
[Pre-Chorus]
[Chorus]
[Bridge]
[Outro]
[Hook]
[Instrumental]
```

Optional vocal style tags:
```
[Spoken Word]
[Whispered]
[Belted]
[Slow]
[Fast]
```

## Example Transformation

**Input:**
```
I've been walking through the city all night long thinking about the times we had together and wondering if you ever think of me too when the lights go down
```

**Output:**
```
[Verse 1]
I've been walking through the city,
all night long
Thinking about the times,
we had together
And wondering if you ever,
think of me too
When the lights go down
```

Notice:
- Original words unchanged
- Commas added for phrasing
- Lines broken at 4-6 words
- Breaks before "and", "when"
- Structure tag added

## Workflow

1. **Extract text** from source (URL, file, or pasted)
2. **Preserve all original words** - no rewrites
3. **Break into short lines** - less than a sentence each
4. **Add commas** at natural pause points
5. **Group into paragraphs** - blank lines between sections
6. **Add structure tags** - identify verses, choruses, bridges
7. **Output formatted lyrics** ready for Suno