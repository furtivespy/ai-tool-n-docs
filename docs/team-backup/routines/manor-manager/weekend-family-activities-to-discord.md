# Weekend family activities to Discord

- Folder: weekend-family-activities-to-discord
- Schedule: CRON_TZ=America/New_York 20 8 * * 3 (Wednesdays 8:20 AM)
- Status: enabled

## Prompt

Every Wednesday at 8:20am ET, research family-friendly activities happening this coming weekend (Friday, Saturday, Sunday) near the user: Syracuse, NY, Onondaga County, and places within about a 45-minute drive of <NEIGHBORHOOD>, Syracuse. Then post the result to the user's common Discord webhook. That's the shared webhook in the env var DISCORD_NEWS_BRIEFING_WEBHOOK, the same one the user's other bots use. POST JSON to it from the shell, and never print or reveal the webhook URL. The user explicitly asked for this post, so send it directly without a draft.

Family context: two boys. <CHILD_1> is <CHILD_1_AGE> and in <CHILD_1_GRADE>. He loves animals (elephants, manatees), space, engineering, and reading. <CHILD_2> is <CHILD_2_AGE> and in <CHILD_2_GRADE>. He loves animals (foxes, giraffes) and trains. Both love Bluey, Octonauts, marble runs, CrunchLabs, Frozen, Pixar, sea shanties, and musical theater. Use this to rank events, not to invent them.

How to research: search current local sources, not memory. Prefer event calendars and venue sites such as Visit Syracuse, syracuse.com events, city and county parks, the MOST, Rosamond Gifford Zoo, Everson, libraries (OCPL), farms, and similar. Include Destiny USA only for a non-shopping family event. Verify dates, times, location, cost (free vs ticketed), and whether the event is still on.

What to include: things a family with kids would actually enjoy. That covers festivals, fairs, craft fairs and makers markets, outdoor concerts or performances, park and nature events, farm and animal activities, museums, library programs, sports or rec events that welcome spectators, seasonal celebrations, and similar community happenings. Indoor and outdoor both count. Recurring staples (zoo, children's museum, splash pads) count only if they have a special weekend program or hours worth noting. Don't pad the list.

What to skip: retail and shopping events, store sales, mall promotions, and "shop local" days. A craft fair, makers market, or festival that happens to have vendors is fine; a shopping-only event is out. Skip 21+ nightlife, bar crawls, and anything not reasonably kid-friendly.

Message shape (Discord webhook JSON):
- content: one short line, e.g. "Family weekend in Syracuse — Fri {date}–Sun {date}." Optionally add a second sentence if the weekend is especially packed or thin.
- embeds: exactly three, in order Friday, Saturday, Sunday.
- Each embed has a title of weekday plus calendar date (e.g. "Friday, August 29") and a different color per day.
- One field per event (inline: false). The field name is the event name. The field value gives time, place, cost, who it's good for (ages if known), and a one-line reason to go, using Discord markdown (**bold**, [link](url)). Flag weather-sensitive outdoor events in that field.
- If a day has nothing solid, still include its embed with description "Nothing family-friendly locked in for this day." and no fields.
- Cap at 8 events per day, choosing the best and most family-relevant. If more exist, add a last field named "Also worth a look" with a short comma-separated list of the leftovers.
- Stay inside Discord limits: titles ≤256 characters, field values ≤1024, ≤25 fields per embed, and about 6000 characters across all embeds combined.
- Do not invent events. If a listing is thin or unofficial, say so in that event's field.

After posting, confirm the webhook returned success (2xx). If the post fails, or research can't be done, tell the user in one short line what failed. On success, send the user one short line in chat saying the weekend list is posted to Discord.
