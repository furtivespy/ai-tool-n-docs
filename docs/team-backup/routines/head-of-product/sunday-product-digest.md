# Sunday product digest
- Folder: <UUID_9>
- Status: enabled
- Schedule: CRON_TZ=America/New_York 0 15 * * 0 (flexible, earlyMinutes 15, lateMinutes 15; fires ~2:53 PM ET)

## Prompt
Weekly Sunday product digest for Will. Post to Discord only (same status channel as CTO: #wills-ai-bullshit-zone). Do not ping Will in Grok Bot chat unless something is blocked (auth failure, missing Linear access, etc.).

DISCORD WEBHOOK: read id+token from /home/box/agent-data/agents/<AGENT_ID:head-of-product>/discord-webhook.txt. Never paste the URL into chat or any user-facing message. Prefer discord_send_webhook_message (or equivalent) with webhook_id + webhook_token. Username override: "Head of Product". Omit plain content when embeds are enough.

LINEAR CONTEXT:
- Team FUR (id <UUID_2>)
- Projects: Bender Bot (<UUID_3>), Game Bot (<UUID_4>), Iconic Recipes (<UUID_5>), Land Here (<UUID_6>), Stancy Bot (<UUID_7>), Barrelhouse Bill (<UUID_8>)
- Product notes: Bender = all-purpose Discord chat bot (~4 servers). Game Bot = board-game helper on Discord (one active server). Stancy = one-job voice-channel bot, now being upgraded into the starter template for new bots (FUR-109–113); report its status when active, but no ideas list or Council for it. Iconic Recipes = early, wife-driven visual cooking/baking product (waves of availability). Land Here = early personal daily-start-on-the-net tool for Will (and maybe friends). Barrelhouse Bill = new Discord bot for a bourbon enthusiast group: MVP is group-only bottle drop sign-ups (In button, ordered list, bottle limit, waitlist, mod list), then bottle lookup; built from the Stancy template.
- Grim Council consult: Bender Bot, Game Bot, Iconic Recipes, Land Here, and Barrelhouse Bill — whenever that project was active this week. Never Stancy.

STEPS EACH RUN:
1. Look up Linear issues across those projects for the past 7 days. An "active" project is any project with issue creates/updates/comments/status changes (or other real activity) in that window. Skip projects with zero activity.
2. For each active project, summarize current open work: Todo / In Progress / In Review / Backlog worth calling out. Lead with what's moving and what's stuck. Be concrete (issue ids + titles + status).
3. Do brief competitive / similar-product research for that product (what peers shipped or are talking about lately). Keep it decision-useful, not a dump. (Skip for Stancy.)
4. Produce 5–10 next-work ideas for that product (skip for Stancy). Most should be product-focused; include 1–2 technical (deps, upgrades, reliability, DX) that aren't directly customer-facing. Prefer ideas grounded in Linear status + research + the product notes above. Idea briefs must be usable — not one-liners: each idea needs enough context that Will knows what it means and why it matters. Before calling something broken for users, confirm that live code actually calls it.
5. Post ONE Discord webhook message per active product:
   - Title the first embed like "Bender Bot Status" (or "Game Bot Status", etc.).
   - Embed 1 — Current status: open items and their Linear statuses (scannable fields or short bullets; link Linear issues when possible). Color roughly blue/purple for status.
   - Embed 2 — Next ideas: numbered 5–10 ideas with short usable briefs; tag which are technical vs product. Color roughly green/teal for ideas. (Omit for Stancy.)
   - Footer can note the week ending date.
6. If zero projects were active, post a single short "No active Linear product activity this week" embed (still continue to Grokbot digest / Council kickoff as applicable).

GRIM COUNCIL (Bender, Game Bot, Iconic Recipes, Land Here, Barrelhouse Bill — async, do not block the digest):
7. After you have next-work ideas for any of those projects (only if that project was active this week), message Grimoire (agent id <AGENT_ID:grimoire>) and ask them to convene the Grim Council. Give them, per product: one-line product context, Linear pulse highlights, and your ranked top ideas (the same ones you posted, with enough brief text to critique). Ask the Council to: (a) react to / prioritize / challenge those ideas, and (b) propose their own next-work ideas for that product. Prefer ONE combined message to Grimoire with clear sections per product if multiple were active. Never include Stancy. Tell Grimoire to reply to Head of Product when deliberation is done (they can be chatty — no rush). Note: routine subagents may lack SendToAgent — if peer messaging fails, WakeParent with the full Grimoire packet so Head of Product can send it.
8. Do NOT wait for Grimoire or the Council. Continue immediately with the Grokbot digest and finish the run. When Grimoire later replies (on a later Head of Product turn), post their take to Discord as Head of Product without waiting on anything else — title like "Land Here — Grim Council" / "Iconic Recipes — Grim Council" (or grouped embeds): Council reaction to the ideas + Council's own ideas. Keep it scannable. Still no Will chat ping unless blocked.

GROKBOT DIGEST (every run, after product posts / Council kickoff):
9. Research what people are actually doing with Grok Bot / Grok agents / multi-agent teams — not just "I have a team of developers shipping a product." Hunt for show-offs, demos, posts, threads, and writeups of novel or interesting setups: personal ops, research, creative, ops/automation, household, games, education, weird experiments, etc. Prefer concrete accomplishments over vague hype. Cover the last ~1–2 weeks when possible; if the scene is thin that week, pull the best recent examples and say so.
10. Post ONE Discord webhook message titled like "Grokbot Digest" (username still Head of Product) with two embeds:
   - Embed 1 — Scene this week: 4–8 concrete examples of what people built or showed off with Grok Bot / agent teams (what they did, why it's interesting, link when available). Color roughly purple.
   - Embed 2 — Steal-worthy angles: 3–6 ideas for how Will's own Grokbot setup / product lane could borrow from the scene. Color roughly gold/orange.
11. Stay quiet in Will's chat after successful Discord posts. Only message him if blocked.

Look up current Linear / Discord / web research / peer-messaging tool schemas each run; do not rely on frozen argument names from this prompt.
