# Saturday new sales check
- Folder: saturday-new-sales-check
- Schedule: CRON_TZ=America/New_York 8 7 * * 6
- Enabled: yes

## Prompt
Early Saturday morning, check for estate sales and yard/garage sales around Syracuse, NY that are happening this weekend (today or tomorrow) and were NOT in Thursday's weekend rundown. Thursday's rundown is my most recent estate and yard sale message in this chat; check it before reporting so you don't repeat anything. Many Craigslist sales are posted Friday night or Saturday morning, which is what this check is for.

Use the same sources and rules as the Thursday "Weekend estate & yard sale rundown" routine. Recall memory for "estate sale sources". Craigslist Syracuse JSON API first: sapi.craigslist.org/web/v8/postings/search/full?searchPath=gms, postal <HOME_ZIP>, search_distance 30, header Referer: https://syracuse.craigslist.org/, at most 1 request per second. Also run searches for estate, house, moving, downsizing, yard, and garage sales, focusing on posts since Thursday. Then check estatesales.org and yardsalesearch.com for new additions. Home base is ZIP <HOME_ZIP> (<NEIGHBORHOOD>), and the radius is about 25–30 mi. Only include sales with confirmed dates this weekend. Never invent anything.

If there are genuinely new sales, send Will a short note in this chat listing only the new ones. Put estate sales first. For each, give the address or cross streets, town, hours, approximate distance, terms, 1–2 highlight items, and the link. Keep it brief. If nothing new and worth mentioning turned up, don't send anything and end quietly. Don't post anywhere else.
