# Mon/Thu tech briefing
- Folder: mon-thu-tech-briefing
- Schedule: CRON_TZ=America/New_York 59 7 * * 1,4
- Enabled: true

## Prompt (verbatim)
Post Will's twice-weekly tech briefing to Discord. Channel: #wills-ai-bullshit-zone in Monster League Chat (guild <DISCORD_GUILD_ID>, channel <DISCORD_CHANNEL_ID>).

Post as username **News Briefing** via the channel webhook. Prefer process.env.DISCORD_NEWS_BRIEFING_WEBHOOK; if unset, read /home/box/agent-data/agents/<AGENT_ID:chief-of-staff>/discord-news-briefing-webhook.txt. Never print or paste the webhook URL/token into chat. POST JSON with curl or a short script; include a DiscordBot User-Agent header (plain urllib without a User-Agent gets 403 from this host). Body must set username to "News Briefing" and include exactly one embed.

Research the past few days (Monday run covers roughly Thu–Mon; Thursday run covers roughly Mon–Thu) on four topics, then post ONE Discord message with ONE embed that covers all four topics. Do not send multiple embeds. Keep it dense and scannable: short bullets or compact field blocks, not long writeups. Consolidate model releases into a few lines (name + one clause each), not a full paragraph per model.

Topics:
1. Self-hosting: What's new? Interesting or buzzing home-lab projects worth adding.
2. AI automations: What are people automating lately? Current hype, cool home-use or software-dev uses.
3. Open source tools / free websites for developers: Hot releases, AI-workflow helpers, or interesting engineer tools that are new or recently hyped/updated.
4. AI general news: Major model releases (consolidated), and AI uses that may benefit society (medical, math, science, education, accessibility, and similar).

Prefer newer or recently buzzing things. Never invent projects, releases, or links; only cite what you verified this run. Prefer project blogs, GitHub, Self-Host Weekly, lab/university outlets, and other primary sources.

Post to Discord only. Do not also ping Will in chat unless posting fails (auth, empty research, missing webhook). On success, stay quiet toward Will. If the webhook fails or the env/file is missing, pause this routine and tell Will to reconnect the News Briefing webhook.
