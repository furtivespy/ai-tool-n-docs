# Weekly Aldi Finds rundown
- Folder: weekly-aldi-finds-rundown
- Schedule: CRON_TZ=America/New_York 30 6 * * 3 (flexible, ±15 min)
- Enabled: yes

## Prompt
Every Wednesday at 7:30 AM ET, send Will a rundown in this chat covering this week's Aldi Finds and the grocery ad. He reads it to decide whether to go to Aldi that day. His store is Aldi at <LOCAL_ALDI_ADDRESS>, Syracuse <STORE_ZIP> (ZIP <HOME_ZIP>). He shops for value, has kids coming for Halloween, and liked the Halloween LEGO standouts.

What to gather for the week starting today, Wednesday:
1. The Aldi Finds that start in stores today, with prices for the <LOCAL_ALDI> store.
2. The top deals in the new regional grocery weekly ad, which goes live at midnight Wednesday.
3. A short preview of next Wednesday's Finds, which Aldi posts a week ahead.

How to get the data: there's a working script at /workspace/aldi/aldi_weekly_pull.py, with sample CSVs in the same folder. It uses Flipp's flyer data by ZIP <HOME_ZIP> and the aldi.us graphql API (store <ALDI_STORE_ID>, <LOCAL_ALDI>; the Finds collection is named like rc-af-all-aldi-finds-M-D-YY). Run it with Shell or adapt it. If it breaks, for example because the query IDs changed, fall back to aldi.us/weekly-specials/aldi-finds, the upcoming-aldi-finds page, and Flipp. If needed, use the box browser without signing in. Doing the gathering in the background is fine.

Format: friendly and skimmable. Lead with the week's dates and 5–8 standout Finds, meaning genuinely good value or fun or popular items (seasonal toys, useful tools, kitchen gear, strong meat prices). Then list other items worth a look, grouped briefly by category. Then give the top 6–10 grocery deals, noting "was" prices when the ad shows them. End with a short heads-up on next week's Finds. Trim the 200–330 item list down to what's actually worth his attention. Never invent items or prices. Give each price as the <LOCAL_ALDI> store price, with the date it was pulled. If data is missing, for example the grocery ad isn't up yet, say so plainly.

Don't post to Discord or anywhere else. This goes only to Will in this chat.
