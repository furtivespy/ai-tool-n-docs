# Wednesday trivia picture clue
- Folder: wednesday-trivia-picture-clue
- Schedule: CRON_TZ=America/New_York 0,30 10-18 * * 3
- Enabled: true

## Prompt (verbatim)
Will plays trivia at Stingers Pizza Pub (Manlius) on Wednesdays at 7 PM ET. The host posts a picture clue for each week's game. The goal is to find this week's clue, figure out what the picture shows, and post the picture plus useful trivia info to Discord, once per Wednesday. This routine fires every 30 minutes from 10:00 AM to 6:30 PM ET on Wednesdays. That's how "if it isn't posted yet, wait 30 minutes and try again" works, so each run is a single check.

1. Dedupe first. Read /home/box/agent-data/agents/<AGENT_ID:chief-of-staff>/trivia/state.json. If last_posted.date is today's date (YYYY-MM-DD, ET), this week's clue is already posted, so stop silently.

2. Fetch the clue posts. The venue page https://syracusetrivianight.com/venue/stingers is protected by Mod_Security and returns 406 unless the request sends full Chrome browser headers (User-Agent, Accept, Accept-Language). The posts aren't in the static page; they load from https://syracusetrivianight.com/scripts/blog.php?id=14 (send the same browser headers plus Referer https://syracusetrivianight.com/venue/stingers and X-Requested-With: XMLHttpRequest). Each post is an <h2> title such as "10/7 Pic clue" or "9/30 Picture clue", then a "Posted on: ..." line, then an <img alt="..." src="...">. The clue for a Wednesday is often posted late the night before, so match on the M/D date in the title, not the posted time. If no pic/picture clue post titled with today's M/D exists yet, stop silently. On the 6:30 PM run only, if there's still no clue, send Will one short chat message saying the clue never showed up.

3. Identify the picture. Download the image to /workspace/trivia/clue-YYYY-MM-DD.<ext> and look at it yourself (for a GIF, pull a few frames with PIL). Use the alt text as a hint, since the host often names the answer there, and confirm it against what you see and a quick web search. Note when you're unsure, and never invent facts.

4. Post to Discord in #wills-ai-bullshit-zone through the saved webhook. Read the URL from env DISCORD_NEWS_BRIEFING_WEBHOOK, or else from /home/box/agent-data/agents/<AGENT_ID:chief-of-staff>/discord-news-briefing-webhook.txt, and never print or reveal it. Send a multipart POST to <webhook>?wait=true with the header "User-Agent: DiscordBot (https://github.com/discord/discord-api-docs, 1.0)". Set username "Trivia Clue" and attach the image file as files[0]. Make it a single embed:
   - The title is "🐟-style emoji + Stingers picture clue: Wed, Mon D"; pick an emoji that fits the subject.
   - The url is the venue page, and image.url is attachment://<filename>.
   - The description has a bolded "What it is" line, 3–5 crisp quick facts, and a "Likely trivia angles" section covering places, pop culture, famous names, nicknames, and anything else a trivia question could hinge on.
   - The footer gives the post time and the host's note, for example the tournament week.
   Keep it compact. If the file is over 10 MB, use the original image URL in image.url instead of attaching.

5. Verify that the response returned a message id with the embed image. Then write state.json with date, post_title, posted_on, image_url, answer, and discord_message_id.

On success, stay quiet in chat because the Discord post is the deliverable. If anything fails (site blocked, webhook error, image unreadable), tell Will briefly in chat what failed.
