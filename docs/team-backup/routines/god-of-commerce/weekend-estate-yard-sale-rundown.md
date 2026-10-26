# Weekend estate & yard sale rundown
- Folder: weekend-estate-yard-sale-rundown
- Schedule: CRON_TZ=America/New_York 7 8 * * 4
- Enabled: yes

## Prompt
Every Thursday morning, send Will a rundown in this chat of estate sales and yard/garage sales happening this coming weekend (Thu–Sun, mostly Fri–Sat) around Syracuse, NY. He doesn't get out every weekend, so this is to help him decide whether anything is worth a trip.

HOME BASE: ZIP <HOME_ZIP> (<NEIGHBORHOOD>), about <HOME_COORDINATES>. Sort by approximate straight-line miles from there. The radius is about 25–30 mi. Mention anything past about 30 mi only in a one-line "farther out" note. If Will has said he'll be somewhere else that weekend, re-center on that town.

SOURCES, BEST FIRST (the playbook from Chief of Staff):
1. Craigslist Syracuse. This is the best source and has most listings. Use the public JSON API with curl instead of the web UI: GET https://sapi.craigslist.org/web/v8/postings/search/full?searchPath=gms&sort=date&batch=1-0-360-1-0&lang=en&cc=us&postal=<HOME_ZIP>&search_distance=30 with header "Referer: https://syracuse.craigslist.org/". Also run gms or sss searches for "estate sale", "house sale", "moving sale", "downsizing", "yard sale", and "garage sale". Check that data.areas is syracuse. Listing URLs look like https://www.craigslist.org/view/d/<slug>/<key>. Look back about 10 days, then open each promising listing to confirm the actual sale date, address, and hours. Stay at or under 1 request per second. On a 403, pause, retry once, then move on.
2. estatesales.org city and town pages fetch fine.
3. yardsalesearch.com (it shares data with gsalr and garagesalefinder). Use its town pages: Syracuse, Liverpool, Camillus, Manlius, DeWitt, Cicero, Baldwinsville, Clay, East Syracuse, and others. These catch non-Craigslist sales and extra-day hours.
4. estatesales.net. Plain fetches only show featured sales. Use the box browser, without signing in, for its full list when time allows.
5. Web search for community, church, and HOA sales (e.g. "Syracuse estate sale <dates>", the WAER community calendar).
Facebook hasn't been tried; skip it.

VERIFY: Only include sales whose date is confirmed. Put "date unclear" ones in a short separate list. Watch for Craigslist date fields that conflict with the ad text, map pins that fall back to a default location, and "Syracuse" posts that are actually far away (e.g. Newark, NY). Never invent sales, addresses, hours, or items.

FORMAT (Will's preference): lead with the answer and keep it scannable. He called a long list "way too large and annoying."
- Estate sales go first and highlighted; they're his main interest. House, moving, and downsizing sales run by estate companies count.
- For each estate sale: address or cross streets, town, day(s) and hours, approximate distance, operator, terms (cash only, sign-up list or numbers, bring your own movers, half-price times), 1–2 lines of highlight items, and the link.
- Then yard, garage, and multi-family sales. List the closest ones in full and give the far ones one line each.
- Note when you pulled the data. If it's a thin weekend, say so briefly.
- Optionally end with a one-line offer to plan a route between several sales.

This goes only to Will in this chat. Don't post to Discord unless he asks that week.
